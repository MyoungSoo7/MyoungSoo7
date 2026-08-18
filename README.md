 
## 클러스터
6개 노드 74 vCPU / 122 GiB. K3s v1.35.4, 네임스페이스 55개, Deployment·StatefulSet 123개에 DaemonSet 6개, CronJob 13개. 파드는 대략 150개, 배포는 ArgoCD (Application 64개). 
지금 돌아가는 서비스 전체 목록은 [www.lemuel.co.kr](https://www.lemuel.co.kr) 에 한 페이지로 정리해 뒀습니다. 네임스페이스·공개 URL·상태가 같이 보입니다.

## 메인 프로젝트 
이커머스 주문 → 결제 → 셀러 정산 → 복식부기 원장이 축이고, 거기서 대출·투자·계정계·기업분석까지 늘렸습니다. 시작은 모놀리스였고 바운디드 컨텍스트로 쪼개고, 이벤트 드리븐으로 바꾸고, CQRS 프로젝션까지 왔습니다. [jen.lemuel.co.kr](https://jen.lemuel.co.kr/) 에서 돌고 있고 Deployment 22개입니다.
아키텍처는 확장에 유리한 헥사고날인데, 말로만 지키는 경계는 반드시 무너져서 ArchUnit 으로 CI 에서 막습니다. 

- 토픽별 JSON-Schema 34종과 프로듀서/컨슈머 계약 테스트
- PG 사(toss/kcp/nice/inicis)별로 서킷 브레이커를 따로 뒀습니다.
- MSA의 장애격리의 강점을 최대한 활용하며 확장햇으며, 정산 서비스는 주문·결제·상품·유저을 Kafka 로 자기 DB 에 투영하고 QueryDSL 로 조회합니다. 
- 공통단 jar를 두어서, 퍼블리시된 아티팩트로 배포됩니다.

언어는 22개 서비스에 섞여 있습니다. Java 15 · Kotlin 2 · Go 2 · Python 3. 실시간 시세 스트리밍과 웹훅 수신은 Go, 예측·백테스트·이상탐지는 Python, 트랜잭션 경계가 중요한 코어는 Java/Kotlin 으로 갔습니다. 다만 폴리글랏 7종은 아직 MVP 단계고 `reconciliation` 은 사실상 스켈레톤입니다.
커버리지는 LINE 90% 밑으로 내려가면 빌드가 깨집니다(핵심 도메인은 INSTRUCTION 80%). 테스트 클래스 758개, 전체 빌드 3,534개, ADR 29건. 그리고 `harness-guard` 워크플로가 문서에 적힌 수치와 실제 코드를 대조합니다. 문서는 손대지 않으면 언젠가 반드시 거짓말이 되기 때문입니다.
주로 쓰는 것: Java 25 · Spring Boot 4.0.4 · Spring Cloud 2025.1 · Gradle 9 (Kotlin DSL) · PostgreSQL 17 (Flyway 마이그레이션 240개) · Elasticsearch 8.17(Nori) · Redis 7 · Kafka · React 19 + Vite + TypeScript · Testcontainers · SonarCloud · Snyk.

## 이외 프로젝트
- [eln.lemuel.co.kr](https://eln.lemuel.co.kr/) — 청각 재활 훈련
- [xr.lemuel.co.kr](https://xr.lemuel.co.kr/) — XR, 영적 훈련

 
