![방문](https://komarev.com/ghpvc/?username=MyoungSoo7&label=%ED%94%84%EB%A1%9C%ED%95%84%20%EB%B0%A9%EB%AC%B8&color=blue&style=flat-square)
[![블로그 방문자](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Flemuel.goatcounter.com%2Fcounter%2FTOTAL.json&query=%24.count_unique&label=%EB%B8%94%EB%A1%9C%EA%B7%B8%20%EB%B0%A9%EB%AC%B8%EC%9E%90&color=blue&style=flat-square)](https://myoungsoo7.github.io/)
[![블로그 글](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fmyoungsoo7.github.io%2Fsearch.json&query=%24.length&label=%EB%B8%94%EB%A1%9C%EA%B7%B8%20%EA%B8%80&color=blue&style=flat-square)](https://myoungsoo7.github.io/)
[![공개 리포](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fapi.github.com%2Fusers%2FMyoungSoo7&query=%24.public_repos&label=%EA%B3%B5%EA%B0%9C%20%EB%A6%AC%ED%8F%AC&color=blue&style=flat-square&cacheSeconds=3600)](https://github.com/MyoungSoo7?tab=repositories)

서버 여섯 대로 K3s 클러스터를 직접 굴리고, 그 위에 이커머스 정산 MSA 를 올려 운영합니다. 인프라 배선부터 도메인 코드까지 한 사람이 세우고, 무너지지 않게 게이트로 지키는 쪽 일을 합니다.
## 클러스터

k3s에 6개 노드(74 vCPU / 122 GiB) - 네임스페이스 46개, Deployment 83개에 StatefulSet 20개, DaemonSet 6개, CronJob 13개. Running 파드는 145개, 배포는 ArgoCD (Application 60개, 전부 Healthy).
관측은 Prometheus · Grafana · Tempo 에 fluent-bit → Elasticsearch 로그 파이프라인. 메시징은 Strimzi Kafka 4.2.0 (KRaft dual-role), 업무 토픽 47개에 DLT 17개. 시크릿은 SOPS + age 로 암호화해서 커밋하니 리포에 평문이 없고, 외부로 나가는 건 포트포워딩 없이 Cloudflare Tunnel 만 지납니다.
지금 돌아가는 서비스 전체 목록은 [www.lemuel.co.kr](https://www.lemuel.co.kr) 에 한 페이지로 정리해 뒀습니다

## 메인 프로젝트

이커머스 주문 → 결제 → 셀러 정산 → 복식부기 원장이 축이고, 거기서 대출·투자·계정계·기업분석까지 늘렸습니다. [jen.lemuel.co.kr](https://jen.lemuel.co.kr/) 에서 돌고 있고 클러스터에는 Deployment 18개 + StatefulSet 2개로 올라가 있습니다.
아키텍처는 확장에 유리한 헥사고날인데, 말로만 지키는 경계는 반드시 무너져서 ArchUnit 으로 CI 에서 막습니다.

### 쪼갠 다음에, 실측으로 다시 합쳤습니다

시작은 모놀리스였고 바운디드 컨텍스트로 쪼갰습니다. 이벤트 드리븐으로 바꾸고 CQRS 프로젝션까지 갔습니다. 그런데 여기서 한 번 더 방향을 틀었습니다 — **쪼갠 경계 중 상당수를 다시 합쳤습니다.**

합친 근거는 취향이 아니라 측정입니다. 두 서비스 사이의 이벤트 간선과 코드 참조를 세어 봤더니 0이었던 것들이 있었습니다. 결합도가 0인 경계는 지킬 것이 없는 경계입니다. 유지 비용만 냅니다.

- ADR 0038 — financial-statements · economics · market · common-data 4개를 `external-data-service` 로 통합. 넷 다 쓰기 오너십이 외부 기관에 있는 read-only 조회이고 이벤트 계약이 0이었습니다
- ADR 0039 — loan + investment 를 `finance-service` 로, deposit 은 `settlement-service` 로. 대출과 투자는 "검증된 정산 실적을 근거로 자금을 조달한다" 는 한 유스케이스의 두 경로였습니다
- ADR 0040 — ai-service 를 `settlement-service` 로. 챗봇이 답하는 것이 정산 일정·수수료·지급·홀드백이고 RAG 근거도 정산 문서였습니다
- ADR 0041 / 0042 / 0043 — notification → operation, organization → order, board·education → operation
- ADR 0044 — company-service 를 3-way 로 해체. 원자료는 external-data 로, 판정과 문서함은 finance 의 reputation 슬라이스로
- ADR 0046 — 반대로 external-data 는 **합치지 않기로** 했습니다. 흡수하자는 근거가 "소비자가 finance 하나뿐" 이었는데, 실측해 보니 프론트엔드가 게이트웨이를 통해 직접 소비하고 있었습니다. 근거 자체가 사실이 아니었습니다

그래서 지금 `settings.gradle.kts` 가 선언하는 모듈은 6개입니다 — `order` · `settlement` · `finance` · `external-data` · `gateway` · `operation`. 여기에 `shared-common` 이 composite build 로 붙어, 로컬에서는 included build 로 치환되고 배포 시에는 publish 된 아티팩트로 소비됩니다.

중요한 건 흡수해도 슬라이스 경계는 살아 있다는 점입니다. 코드는 `github.lms.lemuel.deposit`, `github.lms.lemuel.ai` 같은 슬라이스로 그대로 남고, 그 경계는 `DepositArchitectureTest` · `AiArchitectureTest` · `FinanceArchitectureTest` 가 계속 강제합니다. 프로세스를 합친 것이지 경계를 버린 것이 아닙니다.

### 이벤트와 정합성

- 토픽별 JSON-Schema 56종과 프로듀서/컨슈머 계약 테스트
- 발행은 Transactional Outbox 로 하고, 중복은 outbox `event_id` UNIQUE → `processed_events` PK → 비즈니스 UNIQUE 세 겹으로 받습니다. 실패한 건 DLQ 로 빼서 다시 태웁니다
- 컨슈머가 50개 파일에 흩어져 있어서, 두 서비스가 같은 `group-id` 를 들거나 DLT 배선이 빠진 리스너는 커밋 단계에서 막습니다. 카프카는 예외도 로그도 없이 조용히 잃는 쪽이라서요
- PG 사(toss/kcp/nice/inicis)별로 서킷 브레이커를 따로 뒀습니다
- 정산 서비스는 주문·결제·상품·유저를 Kafka 로 자기 DB 에 투영하고 QueryDSL 로 조회합니다

### 언어와 도구

JVM 코어는 Java 입니다(`.java` 4,310 파일). 트랜잭션 경계가 중요한 곳은 전부 여기에 있습니다. 나머지는 성질에 맞게 갈랐습니다 — 실시간 시세 스트리밍은 Go(`market-stream-service`), 영수증 OCR 은 Python(`receipt-ocr-service`), 프론트는 React 19 + Vite + TypeScript 입니다. Kotlin 은 이 리포가 아니라 [lemuel-xr](https://github.com/MyoungSoo7/lemuel-xr) 과 [inter-asat](https://github.com/MyoungSoo7/inter-asat) 쪽에 있습니다.

커버리지는 LINE 90% 밑으로 내려가면 빌드가 깨집니다(핵심 도메인 패키지는 INSTRUCTION 별도 룰). 테스트 클래스 1,228개(`*Test` 1,147 + `*IT` 71), ADR 45건, Flyway 마이그레이션 287개. 그리고 `harness-guard` 워크플로가 문서에 적힌 수치와 실제 코드를 대조합니다. 문서는 손대지 않으면 언젠가 반드시 거짓말이 되기 때문입니다.

한 가지 덧붙이면, 커버리지 게이트도 한 번 공전한 적이 있습니다. 클린 빌드에서 측정 대상이 빈 집합으로 굳으면 리포트는 클래스 0개로 나오고 90% 검증은 "위반 없음" 으로 통과합니다. 게이트가 켜져 있는데 아무것도 재지 않는 상태였고 빌드는 초록이었습니다. 지금은 대상이 0개인지를 실행 시점에 확인합니다.

주로 쓰는 것: Java 25 · Spring Boot 4.0.7 · Spring Cloud 2025.1 · Gradle 9.1 (Kotlin DSL) · PostgreSQL 17 (Flyway) · Elasticsearch 8.17(Nori) · Redis 7 · Kafka 4.2.0 · React 19 + Vite + TypeScript · Testcontainers · SonarCloud · Snyk.

## AI 에이전트를 이 리포에 붙이면서 만든 것

LLM 에이전트한테 코드를 맡기려면 규칙을 문서로 적는 걸로는 부족합니다. 문서는 읽히기도 하고 안 읽히기도 하니까, 기계가 강제하는 층을 따로 만들었습니다.
규칙 엔진 하나(`guard.mjs`, 규칙 19종)가 세 시점에 붙습니다 — 편집 직전 훅에서 차단, pre-commit 에서 커밋 거부, CI 에서 재차단. 한 층을 건너뛰어도 다음 층이 같은 규칙으로 막는 구성입니다. 규칙 면제 주석은 사유·이슈·담당자·만료일이 다 있어야 유효해서, 무기한 예외가 문법적으로 불가능합니다.
게이트는 베스트 프랙티스 목록이 아니라 대부분 한 번 터진 사건에서 나왔습니다. 체크 예외에 롤백하지 않는 `@Transactional`, 프록시를 안 타는 self-invocation, 같은 `group-id` 를 든 컨슈머, 파티션 수를 코드 밖에서 바꾸는 일 — 전부 컴파일도 테스트도 통과하고 운영에서만 틀리는 것들입니다.
어떻게 구성했고 어디가 약한지는 [블로그](https://myoungsoo7.github.io/2026/08/18/what-a-harness-score-of-8-6-actually-measures/)에 근거와 함께 적어 뒀습니다.

## 이외 프로젝트 
- [asat.lemuel.co.kr](https://asat.lemuel.co.kr/) — 청각 재활 훈련
- [xr.lemuel.co.kr](https://xr.lemuel.co.kr/) — XR, 영적 훈련
- [shop.lemuel.co.kr](https://shop.lemuel.co.kr/) — 쇼핑몰
 ADR 로 결정과 이유를 적고, 문서의 수치에는 다시 세는 명령을 같이 씁니다. 그때그때 배운 건 [블로그](https://myoungsoo7.github.io/)에 정리합니다.
