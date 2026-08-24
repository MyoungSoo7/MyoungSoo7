[![블로그 방문자](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Flemuel.goatcounter.com%2Fcounter%2FTOTAL.json&query=%24.count_unique&label=%EB%B8%94%EB%A1%9C%EA%B7%B8%20%EB%B0%A9%EB%AC%B8%EC%9E%90&color=blue&style=flat-square)](https://myoungsoo7.github.io/)
<br>
서버 여섯 대로 K3s 클러스터를 직접 굴리고, 그 위에 이커머스 정산 MSA 를 올려 운영합니다. 인프라 배선부터 도메인 코드까지 한 사람이 세우고, 무너지지 않게 게이트로 지키는 쪽 일을 합니다.

## 클러스터

k3s에 6개 노드(74 vCPU / 122 GiB) - 네임스페이스 55개, Deployment·StatefulSet 123개에 DaemonSet 6개, CronJob 13개. 파드는 대략 150개, 배포는 ArgoCD (Application 59개).
관측은 Prometheus · Grafana · Tempo 에 fluent-bit → Elasticsearch 로그 파이프라인. 메시징은 Strimzi Kafka 4.2.0 (KRaft dual-role), 토픽 42개. 시크릿은 SOPS + age 로 암호화해서 커밋하니 리포에 평문이 없고, 외부로 나가는 건 포트포워딩 없이 Cloudflare Tunnel 만 지납니다.
지금 돌아가는 서비스 전체 목록은 [www.lemuel.co.kr](https://www.lemuel.co.kr) 에 한 페이지로 정리해 뒀습니다

## 메인 프로젝트

이커머스 주문 → 결제 → 셀러 정산 → 복식부기 원장이 축이고, 거기서 대출·투자·계정계·기업분석까지 늘렸습니다. 시작은 모놀리스였고 바운디드 컨텍스트로 쪼개고, 이벤트 드리븐으로 바꾸고, CQRS 프로젝션까지 왔습니다. [jen.lemuel.co.kr](https://jen.lemuel.co.kr/) 에서 돌고 있고 Deployment 22개입니다.
아키텍처는 확장에 유리한 헥사고날인데, 말로만 지키는 경계는 반드시 무너져서 ArchUnit 으로 CI 에서 막습니다.

- 토픽별 JSON-Schema 34종과 프로듀서/컨슈머 계약 테스트
- 발행은 Transactional Outbox 로 하고, 중복은 outbox `event_id` UNIQUE → `processed_events` PK → 비즈니스 UNIQUE 세 겹으로 받습니다. 실패한 건 DLQ 로 빼서 다시 태웁니다
- 컨슈머가 55개 파일에 흩어져 있어서, 두 서비스가 같은 `group-id` 를 들거나 DLT 배선이 빠진 리스너는 커밋 단계에서 막습니다. 카프카는 예외도 로그도 없이 조용히 잃는 쪽이라서요
- PG 사(toss/kcp/nice/inicis)별로 서킷 브레이커를 따로 뒀습니다.
- MSA의 장애격리의 강점을 최대한 활용하며 확장했으며, 정산 서비스는 주문·결제·상품·유저를 Kafka 로 자기 DB 에 투영하고 QueryDSL 로 조회합니다.
- 공통단 jar를 두어서, 퍼블리시된 아티팩트로 배포됩니다.

언어는 22개 서비스에 섞여 있습니다. Java 15 · Kotlin 2 · Go 2 · Python 3. 실시간 시세 스트리밍과 웹훅 수신은 Go, 예측·백테스트·이상탐지는 Python, 트랜잭션 경계가 중요한 코어는 Java/Kotlin 으로 갔습니다. 다만 폴리글랏 7종은 아직 MVP 단계고 `reconciliation` 은 사실상 스켈레톤입니다.
커버리지는 LINE 90% 밑으로 내려가면 빌드가 깨집니다(핵심 도메인은 INSTRUCTION 80%). 테스트 클래스 758개, 전체 빌드 3,534개, ADR 29건. 그리고 `harness-guard` 워크플로가 문서에 적힌 수치와 실제 코드를 대조합니다. 문서는 손대지 않으면 언젠가 반드시 거짓말이 되기 때문입니다.
주로 쓰는 것: Java 25 · Spring Boot 4.0.4 · Spring Cloud 2025.1 · Gradle 9 (Kotlin DSL) · PostgreSQL 17 (Flyway 마이그레이션 240개) · Elasticsearch 8.17(Nori) · Redis 7 · Kafka · React 19 + Vite + TypeScript · Testcontainers · SonarCloud · Snyk.

## AI 에이전트를 이 리포에 붙이면서 만든 것

LLM 에이전트한테 코드를 맡기려면 규칙을 문서로 적는 걸로는 부족합니다. 문서는 읽히기도 하고 안 읽히기도 하니까, 기계가 강제하는 층을 따로 만들었습니다.
규칙 엔진 하나(`guard.mjs`, 규칙 18종)가 세 시점에 붙습니다 — 편집 직전 훅에서 차단, pre-commit 에서 커밋 거부, CI 에서 재차단. 한 층을 건너뛰어도 다음 층이 같은 규칙으로 막는 구성입니다. 규칙 면제 주석은 사유·이슈·담당자·만료일이 다 있어야 유효해서, 무기한 예외가 문법적으로 불가능합니다.
게이트는 베스트 프랙티스 목록이 아니라 대부분 한 번 터진 사건에서 나왔습니다. 체크 예외에 롤백하지 않는 `@Transactional`, 프록시를 안 타는 self-invocation, 같은 `group-id` 를 든 컨슈머, 파티션 수를 코드 밖에서 바꾸는 일 — 전부 컴파일도 테스트도 통과하고 운영에서만 틀리는 것들입니다.
어떻게 구성했고 어디가 약한지는 [블로그](https://myoungsoo7.github.io/2026/08/18/what-a-harness-score-of-8-6-actually-measures/)에 근거와 함께 적어 뒀습니다.

## 이외 프로젝트

- [eln.lemuel.co.kr](https://eln.lemuel.co.kr/) — 청각 재활 훈련
- [xr.lemuel.co.kr](https://xr.lemuel.co.kr/) — XR, 영적 훈련
 ADR 로 결정과 이유를 적고, 문서의 수치에는 다시 세는 명령을 같이 씁니다. 그때그때 배운 건 [블로그](https://myoungsoo7.github.io/)에 정리합니다.

