<div align="center">

# 👋 안녕하세요, 서버 엔지니어 입니다 ![Profile Views](https://komarev.com/ghpvc/?username=MyoungSoo7&style=for-the-badge&color=brightgreen)

집에서 6대의 물리 노드로 K3s 클러스터를 직접 운영합니다. 클라우드 없이, 외부 IP 없이, GitOps 로.

</div>

## 🏠 운영 중인 K3s 클러스터

```
┌─ K3s Cluster (v1.35.4+k3s1) ───────────────────────────────────
│  🖥   Nodes         6 / 6 Ready   ·  74 vCPU / 122 GiB RAM
│  📦  Namespaces    55
│  🧩  Workloads     123 Deployment·StatefulSet · 6 DaemonSet · 13 CronJob
│  🚀  Running Pods  154
│  🔁  Delivery      ArgoCD GitOps (64 Application) · GitHub Actions · Image Updater
│  📊  Observability Prometheus · Grafana · Tempo · ELK(ECK, hot/warm/cold ILM)
│  🧵  Messaging     Strimzi Kafka (KRaft dual-role)
│  💾  Backup        Velero (Kopia) · 야간 pg_dump CronJob 7종
│  🔐  Secrets       SOPS + age (GitOps 에 평문 시크릿 0)
│  🌐  Ingress       Cloudflare Tunnel — 외부 IP·포트포워딩 없이 공개
└────────────────────────────────────────────────────────────────
```

| 노드      | 역할                                  | vCPU / RAM  |
| --------- | ------------------------------------- | ----------- |
| `lemuel`  | control-plane · etcd voter            | 4 / 32 GiB  |
| `ilwon`   | control-plane · etcd voter · 스토리지 | 12 / 32 GiB |
| `solomon` | control-plane · etcd voter            | 4 / 15 GiB  |
| `isagal`  | worker (주력 컴퓨트)                  | 40 / 15 GiB |
| `louise`  | worker                                | 8 / 16 GiB  |
| `david`   | worker                                | 6 / 15 GiB  |

> 📋 **전체 서비스 인벤토리 → [www.lemuel.co.kr](https://www.lemuel.co.kr)**
> 클러스터에서 돌고 있는 100개 서비스를 네임스페이스·공개 URL·상태와 함께 한 페이지에 표시합니다.

## 🧩 메인 프로젝트 

**이커머스 주문 → 결제 → 셀러 정산 → 복식부기 원장**을 축으로 대출·투자·계정계·기업분석까지 확장한 **폴리글랏 모노-MSA**.
모놀리스 → 바운디드 컨텍스트 분리 → 이벤트 드리븐 → DB-per-service + CQRS 프로젝션 순으로 진화시켰고,
[jen.lemuel.co.kr](https://jen.lemuel.co.kr/) 에서 실제 운영 중입니다 (클러스터에 22개 Deployment).

**아키텍처**

- **헥사고날 (Ports & Adapters)** — `domain` / `application` / `adapter` 단방향 의존
- **ArchUnit 으로 경계를 컴파일러처럼 강제** — 서비스마다 아키텍처 테스트를 두고 ① 도메인의 Spring 의존 금지 ② application 의 JPA 직접 참조 금지 ③ adapter 의 cross-domain 참조 금지 를 CI 에서 차단
- **Transactional Outbox** — 도메인 트랜잭션 안에서 outbox row 를 쓰고, 멀티 워커 폴러가 `FOR UPDATE SKIP LOCKED` 로 경합 없이 집어 발행. DLQ + 관리자 재처리 엔드포인트까지
- **Triple Idempotency** — L1 outbox `event_id` UNIQUE → L2 `processed_events` PK → L3 비즈니스 UNIQUE 제약. at-least-once 메시징에서 어느 한 층이 뚫려도 다음 층이 막습니다
- **CQRS 프로젝션** — DB-per-service 이후 정산 서비스가 Kafka 컨슈머로 주문/결제/상품/유저 read model 을 자기 DB 에 투영하고, QueryDSL 로 조회. 조회 때문에 다른 서비스 DB 를 찌르지 않습니다
- **이벤트 계약을 코드로** — 토픽별 JSON-Schema 34종 + 프로듀서/컨슈머 계약 테스트
- **비동기 경계를 넘는 트레이싱** — outbox row 에 W3C `traceparent` 를 실어 발행/소비 구간을 하나의 트레이스로 연결
- **결제사 장애 격리** — Resilience4j 서킷 브레이커를 PG 사별(toss/kcp/nice/inicis)로 분리, 4xx 는 실패율에서 제외
- **shared-common 을 composite build 로 분리** — 로컬은 included build, 배포는 publish 된 아티팩트로 소비

**스택**

| 영역       | 사용 기술                                                                                                          |
| ---------- | ------------------------------------------------------------------------------------------------------------------ |
| 언어       | Java 25 · Kotlin · Go 1.22 · Python 3.11                                                                           |
| 프레임워크 | Spring Boot 4.0.4 · Spring Cloud 2025.1 · FastAPI                                                                  |
| 빌드       | Gradle 9 (Kotlin DSL) · composite build                                                                            |
| 데이터     | PostgreSQL 17 (Flyway 240 마이그레이션) · Elasticsearch 8.17(Nori) · Redis 7 · Kafka(Strimzi KRaft / dev Redpanda) |
| 프론트     | React 19 · Vite · TypeScript                                                                                       |
| 품질       | ArchUnit · Testcontainers · JaCoCo · SonarCloud · Snyk                                                             |

**서비스 구성 (22종)** — Java 15 · Kotlin 2(`notification`, `reconciliation`) · Go 2(`market-stream`, `payment-webhook`) · Python 3(`screening-backtest`, `anomaly`, `forecast`)
언어는 도메인 특성에 맞춰 골랐습니다. 실시간 시세 스트리밍·웹훅 수신은 Go, 예측·백테스트·이상탐지는 Python, 트랜잭션 경계가 중요한 코어는 Java/Kotlin.
※ 폴리글랏 7종은 아직 MVP 단계이고 `reconciliation` 은 스켈레톤입니다

**품질 게이트** — JaCoCo LINE 커버리지 **90%** 미만이면 빌드 실패(핵심 도메인 INSTRUCTION 80%), `check` 가 커버리지 검증에 의존.
테스트 클래스 758개 · 전체 빌드 3,534 테스트 통과 · ADR 29건 (리포 `STATUS.md` 기준, 재검증 커맨드를 문서에 함께 기재).
CI 는 `paths-filter` 로 변경 모듈만 빌드하고, `harness-guard` 워크플로가 문서에 적힌 수치와 실제 코드를 대조해 **문서-코드 드리프트를 차단**합니다.

## 🌐 Live Services

| 서비스            | 역할                                | 링크                                            |
| ----------------- | ----------------------------------- | ----------------------------------------------- |
| 💰 Jen Settlement | 정산·대출·투자·금융 (메인 프로젝트) | [jen.lemuel.co.kr](https://jen.lemuel.co.kr/)   |
| 👂 ASAT           | 청각 재활 훈련                      | [eln.lemuel.co.kr](https://eln.lemuel.co.kr/)   |
 
## ⚡ Impact Highlights

- 🔥 **장애 대응** — velero node-agent `OOMKilled 44회` 반복 → 메모리/병렬도 튜닝으로 **0회** 안정화
- 🚀 **배포 자동화** — 수동 배포 → **ArgoCD GitOps** 전환, `git push` 한 번으로 프로덕션까지 자동 반영
- 🛡️ **결제 무결성** — Transactional Outbox + **Triple Idempotency** (L1 outbox / L2 processed / L3 DB unique) 로 at-least-once 메시징에서 중복 처리 차단
- 💾 **스토리지 최적화** — ELK `hot/warm/cold` ILM tier 로 로그 수명주기 자동 관리, 오래된 로그 저비용 노드로 이관
- 🧹 **인벤토리 정리** — 백엔드 없이 502 만 뱉던 도메인 규칙 11개 제거, 미사용 서비스 파킹(replicas 0)으로 리소스 회수
- 📈 **관측성 3층** — Prometheus(메트릭) + ELK(로그) + 커스텀 대시보드(상태) 로 6노드·100개 서비스를 단일 화면에서 관제
