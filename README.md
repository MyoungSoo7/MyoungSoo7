# MyoungSoo7

집에 서버 여섯 대를 두고 K3s 클러스터를 직접 굴립니다. 클라우드는 안 쓰고, 공유기에 포트포워딩도 열어두지 않았습니다. 외부로 나가는 건 전부 Cloudflare Tunnel 을 지납니다.

처음부터 이럴 생각은 아니었고, 노는 컴퓨터가 하나씩 생길 때마다 노드로 붙이다 보니 여기까지 왔습니다. 그래서 스펙이 제각각입니다.

## 클러스터

| 노드      | 역할                                  | vCPU / RAM  |
| --------- | ------------------------------------- | ----------- |
| `lemuel`  | control-plane · etcd voter            | 4 / 32 GiB  |
| `ilwon`   | control-plane · etcd voter · 스토리지 | 12 / 32 GiB |
| `solomon` | control-plane · etcd voter            | 4 / 15 GiB  |
| `isagal`  | worker (주력 컴퓨트)                  | 40 / 15 GiB |
| `louise`  | worker                                | 8 / 16 GiB  |
| `david`   | worker                                | 6 / 15 GiB  |

합쳐서 74 vCPU / 122 GiB. K3s v1.35.4, 네임스페이스 55개, Deployment·StatefulSet 123개에 DaemonSet 6개, CronJob 13개. 파드는 대체로 150개 언저리가 떠 있습니다.

배포는 ArgoCD 로 합니다 (Application 64개). 시크릿은 SOPS + age 로 암호화해서 커밋하니 리포에 평문은 없습니다. 백업은 Velero(Kopia) 와 야간 `pg_dump` 크론 7종. 관측은 Prometheus · Grafana · Tempo · ELK 를 쓰고, ELK 는 hot/warm/cold ILM 으로 오래된 로그를 알아서 싼 노드로 내립니다. 메시징은 Strimzi Kafka(KRaft).

지금 돌아가는 서비스 전체 목록은 [www.lemuel.co.kr](https://www.lemuel.co.kr) 에 한 페이지로 정리해 뒀습니다. 네임스페이스·공개 URL·상태가 같이 보입니다.

## 주로 붙잡고 있는 것 — jen

이커머스 주문 → 결제 → 셀러 정산 → 복식부기 원장이 축이고, 거기서 대출·투자·계정계·기업분석까지 늘렸습니다. 시작은 모놀리스였고 바운디드 컨텍스트로 쪼개고, 이벤트 드리븐으로 바꾸고, DB-per-service + CQRS 프로젝션까지 왔습니다. [jen.lemuel.co.kr](https://jen.lemuel.co.kr/) 에서 돌고 있고 Deployment 22개입니다.

구조는 헥사고날인데, 말로만 지키는 경계는 반드시 무너져서 ArchUnit 으로 CI 에서 막습니다. 도메인이 Spring 을 알면 안 되고, application 이 JPA 를 직접 부르면 안 되고, adapter 가 남의 도메인을 참조하면 안 됩니다. 규칙은 세 줄인데 있고 없고가 꽤 다릅니다.

제일 신경 쓴 건 중복입니다. Transactional Outbox 로 도메인 트랜잭션 안에서 outbox row 를 쓰고, 워커 여러 개가 `FOR UPDATE SKIP LOCKED` 로 경합 없이 집어 발행합니다. at-least-once 라 중복은 언젠가 옵니다 — outbox `event_id` UNIQUE, `processed_events` PK, 마지막으로 비즈니스 UNIQUE 제약. 세 겹으로 받아서 한 겹이 뚫려도 다음이 막게 해뒀습니다. 실패한 건 DLQ 로 빠지고 관리자 엔드포인트로 다시 태웁니다.

나머지는 이런 것들입니다.

- 토픽별 JSON-Schema 34종과 프로듀서/컨슈머 계약 테스트
- outbox row 에 W3C `traceparent` 를 실어 발행과 소비를 한 트레이스로 봅니다. 비동기 경계를 넘어가면 트레이스가 끊겨서 디버깅이 갑자기 어려워집니다
- PG 사(toss/kcp/nice/inicis)별로 서킷 브레이커를 따로 뒀습니다. 4xx 는 실패율 계산에서 뺍니다 — 그건 우리 요청이 틀린 거지 상대가 죽은 게 아니라서요
- 정산 서비스는 주문·결제·상품·유저 read model 을 Kafka 로 자기 DB 에 투영하고 QueryDSL 로 조회합니다. 조회하겠다고 남의 DB 를 찌르지 않습니다
- `shared-common` 은 composite build 로 뺐습니다. 로컬은 included build, 배포는 퍼블리시된 아티팩트

언어는 22개 서비스에 섞여 있습니다. Java 15 · Kotlin 2 · Go 2 · Python 3. 실시간 시세 스트리밍과 웹훅 수신은 Go, 예측·백테스트·이상탐지는 Python, 트랜잭션 경계가 중요한 코어는 Java/Kotlin 으로 갔습니다. 다만 폴리글랏 7종은 아직 MVP 단계고 `reconciliation` 은 사실상 스켈레톤입니다.

커버리지는 LINE 90% 밑으로 내려가면 빌드가 깨집니다(핵심 도메인은 INSTRUCTION 80%). 테스트 클래스 758개, 전체 빌드 3,534개, ADR 29건. 그리고 `harness-guard` 워크플로가 문서에 적힌 수치와 실제 코드를 대조합니다. 문서는 손대지 않으면 언젠가 반드시 거짓말이 되기 때문입니다.

주로 쓰는 것: Java 25 · Spring Boot 4.0.4 · Spring Cloud 2025.1 · Gradle 9 (Kotlin DSL) · PostgreSQL 17 (Flyway 마이그레이션 240개) · Elasticsearch 8.17(Nori) · Redis 7 · Kafka · React 19 + Vite + TypeScript · Testcontainers · SonarCloud · Snyk.

## 돌아가는 것들

- [jen.lemuel.co.kr](https://jen.lemuel.co.kr/) — 정산·대출·투자
- [eln.lemuel.co.kr](https://eln.lemuel.co.kr/) — ASAT, 청각 재활 훈련
- [xr.lemuel.co.kr](https://xr.lemuel.co.kr/) — XR, 영적 훈련

## 기억에 남는 삽질 몇 개

Velero 의 node-agent 가 44번 연속 OOMKilled 됐습니다. 결국 메모리 한도와 병렬도 두 줄을 고쳐서 0으로 내려왔는데, 그 두 줄을 찾는 데 며칠 걸렸습니다.

노드 하나에서만 DB 커넥션 풀이 자꾸 말랐습니다. 애플리케이션 코드를 한참 뒤졌는데 범인은 그 노드에 랜선과 WiFi 동글이 같은 주소로 동시에 붙어 있던 거였습니다. 인바운드가 느린 쪽으로 흘렀던 거고, 동글을 뽑으니 응답이 43.5ms 에서 0.23ms 가 됐습니다. 그날 이후로는 코드보다 배선을 먼저 의심합니다.

백엔드도 없이 502 만 뱉던 도메인 규칙 11개를 지웠습니다. 예전에 만들고 안 치운 것들인데, 대시보드에 빨간 줄이 상시로 켜져 있으면 진짜 장애가 났을 때 그게 안 보입니다. 안 쓰는 서비스도 replicas 0 으로 내려 자원을 회수했습니다.

배포도 원래는 손으로 했습니다. ArgoCD 로 옮기고 나서는 `git push` 하나로 프로덕션까지 갑니다. 되돌리는 것도 그만큼 쉬워졌다는 게 사실 더 큽니다.
