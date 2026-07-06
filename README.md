<div align="center">

# 👋 안녕하세요, 백엔드 · 인프라 엔지니어 입니다 
**6노드 K3s 클러스터**를 세워 두고 그 위에서 서비스를 운영합니다.<br/>
클린 아키텍처와 이벤트 기반 MSA, 그리고 GitOps로 *"코드부터 프로덕션까지"* 를 손으로 만져 봅니다.
<br/>
![Profile Views](https://komarev.com/ghpvc/?username=MyoungSoo7&style=for-the-badge&color=brightgreen)
</div>

---
## 🧑‍💻 About
- 🏗️ **클린 아키텍처 · DDD · 이벤트 기반 MSA** 로 시스템의 경계를 설계합니다
- 🏠 집에서 **6대의 이기종 머신**(노트북·맥미니·데스크탑)으로 **K3s 클러스터**를 운영합니다
- 🔁 **GitOps(ArgoCD)** 로 push 한 번에 프로덕션까지 자동 배포되는 파이프라인을 굴립니다
- 📈 **Prometheus · Grafana · ELK** 로 관측(observability)을 3층으로 쌓았습니다
- ✍️ 배운 것은 [**기술 블로그**](https://MyoungSoo7.github.io/)에 정리합니다
---

## 🏠 k3s 클러스터
```
┌─ K3s Cluster ────────────────────────────────┐
│  🖥  Nodes        6 / 6 Ready                             │
│  📦  Namespaces   60                                     │
│  🧩  Pods         330+                                   │
│  🚀  Delivery     GitOps (ArgoCD) · GitHub Actions       │
│  📊  Observability Prometheus · Grafana · ELK            │
│  🌐  Ingress      Cloudflare Tunnel (외부 IP 없이 공개)    │
└──────────────────────────────────────────────────────┘
```

**Live Services**

| 서비스 | 역할 | 링크 |
|---|---|---|
| 🌐 Homepage | 메인 서비스 | [lemuel.co.kr](https://www.lemuel.co.kr/) |
| 🚀 ArgoCD | GitOps 배포 | [argocd.lemuel.co.kr](https://argocd.lemuel.co.kr/) |
| 🔎 K8s | 대시보드 | [dashboard.lemuel.co.kr](https://dashboard.lemuel.co.kr/) |
| 📊 Grafana | 메트릭 모니터링 | [grafana.lemuel.co.kr](https://grafana.lemuel.co.kr/) |
| 🔎 Kibana | 로그 분석 | [kibana.lemuel.co.kr](https://kibana.lemuel.co.kr/) |

---

## 📊 By the Numbers

<div align="center">

| | | |
|:---:|:---:|:---:|
| **6** nodes | **40+** microservices | **19** public domains |
| K3s 클러스터 | 60 namespaces · 188 pods | 100% 자체 도메인 |
| **100%** on-prem | **18+** ArgoCD apps | **15M+** log docs |
| 클라우드 비용 $0 | GitOps 자동배포 | ELK 실시간 색인 |

</div>

> 규모(scale)는 위 표, 임팩트(impact)는 아래 — *"문제 → 결과"* 로.

**⚡ Impact Highlights**

- 🔥 **장애 대응** — velero node-agent `OOMKilled 44회` 반복 → 메모리/병렬도 튜닝으로 **0회** 안정화
- 🚀 **배포 자동화** — 수동 배포 → **ArgoCD GitOps** 전환, `git push` 한 번으로 프로덕션까지 자동 반영
- 🛡️ **결제 무결성** — Transactional Outbox + **Triple Idempotency** (L1 outbox / L2 processed / L3 DB unique) 로 at-least-once 메시징에서 중복 처리 차단
- 💾 **스토리지 최적화** — ELK `hot/warm/cold` ILM tier 로 로그 수명주기 자동 관리, 오래된 로그 저비용 노드로 이관
- 📈 **관측성 3층** — Prometheus(메트릭) + ELK(로그) + 커스텀 대시보드(상태) 로 6노드·40+ 서비스를 단일 화면에서 관제

---

## 🚀 Featured Projects

- **[settlement](https://github.com/MyoungSoo7/settlement)** — 주문·결제·정산·승인 시스템. *헥사고날 아키텍처 · Transactional Outbox · Triple Idempotency · ArchUnit* 으로 경계를 컴파일러처럼 강제
- **[approval](https://github.com/MyoungSoo7/approval)** — 결재 승인 시스템 (Kotlin · Spring Boot 3.5 · PostgreSQL)
- **[sns](https://github.com/MyoungSoo7/approval)** — sns 시스템 (Java · Spring Boot 2.7 · Kafka)
- **[pharmacy-recommend](https://github.com/MyoungSoo7/pharmacy-recommend)** — 약국 추천 (Java 17 · Spring Boot 3.2 · DDD · Redis)
- **[shopping-lowprice](https://github.com/MyoungSoo7/sns-portfolio)** — 저가 쇼핑 (Spring Boot · JPA · Scheduler · Spring Security)

---

## 📊 GitHub Stats

<div align="center">

![MyoungSoo7's GitHub stats](https://github-readme-stats.vercel.app/api?username=MyoungSoo7&show_icons=true&theme=tokyonight&hide_border=true&count_private=true)
![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=MyoungSoo7&layout=compact&theme=tokyonight&hide_border=true&langs_count=8)

![GitHub Streak](https://streak-stats.demolab.com?user=MyoungSoo7&theme=tokyonight&hide_border=true)

</div>

---

<div align="center">

### ✍️ 배운 것은 글로 남깁니다 → [**MyoungSoo7.github.io**](https://MyoungSoo7.github.io/)

*"직접 만들고, 직접 굴리고, 직접 정리한다."*

</div>
