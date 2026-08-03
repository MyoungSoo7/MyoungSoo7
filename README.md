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

## 🧩 메인 프로젝트 — [settlement](https://github.com/MyoungSoo7/settlement)

**주문 · 결제 · 정산 · 승인** 파이프라인을 중심으로 대출/투자/시장분석 도메인까지 묶은 **폴리글랏 모노-MSA**.
[jen.lemuel.co.kr](https://jen.lemuel.co.kr/) 에서 실제 운영 중이며, 클러스터에 22개 Deployment 로 떠 있습니다.

**아키텍처**

- **헥사고날 (Ports & Adapters)** — `domain` / `application` / `adapter` 단방향 의존
- **ArchUnit 으로 경계를 컴파일러처럼 강제** — 서비스마다 아키텍처 테스트를 두고 ① 도메인의 Spring 의존 금지 ② application 의 JPA 직접 참조 금지 ③ adapter 의 cross-domain 참조 금지 를 CI 에서 차단
- **CQRS** — 쓰기는 `order-service`, 조회는 `settlement-service`(= `settlement-query`) 로 분리
- **Transactional Outbox** — `PENDING → PUBLISHED` 상태머신 + 배치 폴링 발행 + DLQ, Micrometer 메트릭으로 적체 관측
- **Triple Idempotency** — L1 outbox `event_id` UNIQUE → L2 `processed_events` PK → L3 비즈니스 UNIQUE 제약. at-least-once 메시징에서 어느 한 층이 뚫려도 다음 층이 막습니다
- **shared-common 을 composite build 로 분리** — 로컬은 included build, 배포는 publish 된 아티팩트로 소비

**스택**

| 영역       | 사용 기술                                              |
| ---------- | ------------------------------------------------------ |
| 언어       | Java 25 · Kotlin · Go · Python 3.12                    |
| 프레임워크 | Spring Boot 4.0 · Spring Cloud 2025.1                  |
| 빌드       | Gradle (Kotlin DSL) · composite build                  |
| 데이터     | PostgreSQL 17 · Elasticsearch · Redis · Kafka(Strimzi) |
| 프론트     | React · TypeScript                                     |
| 품질       | ArchUnit · Testcontainers · JaCoCo · SonarQube         |

**서비스 구성 (22종)** — Java 15 · Kotlin 2(`reconciliation`, `notification`) · Go 2(`market-stream`, `payment-webhook`) · Python 3(`forecast`, `screening-backtest`, `anomaly`)
언어는 도메인 특성에 맞춰 선택했습니다. 실시간 시세 스트리밍·웹훅 수신은 Go, 예측·백테스트·이상탐지는 Python, 트랜잭션 경계가 중요한 코어는 Java/Kotlin.

## 🌐 Live Services

| 서비스            | 역할                                | 링크                                            |
| ----------------- | ----------------------------------- | ----------------------------------------------- |
| 💰 Jen Settlement | 정산·대출·투자·금융 (메인 프로젝트) | [jen.lemuel.co.kr](https://jen.lemuel.co.kr/)   |
| 👂 ASAT           | 청각 재활 훈련                      | [eln.lemuel.co.kr](https://eln.lemuel.co.kr/)   |
| 🎮 Lemuel XR      | 성경 기반 감정 회복 서사 게임       | [xr.lemuel.co.kr](https://xr.lemuel.co.kr/)     |
| 📊 DART Analytics | 전자공시 기업 재무분석              | [dart.lemuel.co.kr](https://dart.lemuel.co.kr/) |
| ✍️ Ghost Blog     | IT articles                         | [blog.lemuel.co.kr](https://blog.lemuel.co.kr/) |
| 🗂 전체 인벤토리   | 클러스터 100개 서비스 한눈에        | [www.lemuel.co.kr](https://www.lemuel.co.kr/)   |

## ⚡ Impact Highlights

- 🔥 **장애 대응** — velero node-agent `OOMKilled 44회` 반복 → 메모리/병렬도 튜닝으로 **0회** 안정화
- 🚀 **배포 자동화** — 수동 배포 → **ArgoCD GitOps** 전환, `git push` 한 번으로 프로덕션까지 자동 반영
- 🛡️ **결제 무결성** — Transactional Outbox + **Triple Idempotency** (L1 outbox / L2 processed / L3 DB unique) 로 at-least-once 메시징에서 중복 처리 차단
- 💾 **스토리지 최적화** — ELK `hot/warm/cold` ILM tier 로 로그 수명주기 자동 관리, 오래된 로그 저비용 노드로 이관
- 🧹 **인벤토리 정리** — 백엔드 없이 502 만 뱉던 도메인 규칙 11개 제거, 미사용 서비스 파킹(replicas 0)으로 리소스 회수
- 📈 **관측성 3층** — Prometheus(메트릭) + ELK(로그) + 커스텀 대시보드(상태) 로 6노드·100개 서비스를 단일 화면에서 관제
