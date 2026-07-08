<div align="center">
# 👋 안녕하세요, 백엔드 · DEVOPS 엔지니어 입니다 ![Profile Views](https://komarev.com/ghpvc/?username=MyoungSoo7&style=for-the-badge&color=brightgreen)
</div>
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

**⚡ Impact Highlights**

- 🔥 **장애 대응** — velero node-agent `OOMKilled 44회` 반복 → 메모리/병렬도 튜닝으로 **0회** 안정화
- 🚀 **배포 자동화** — 수동 배포 → **ArgoCD GitOps** 전환, `git push` 한 번으로 프로덕션까지 자동 반영
- 🛡️ **결제 무결성** — Transactional Outbox + **Triple Idempotency** (L1 outbox / L2 processed / L3 DB unique) 로 at-least-once 메시징에서 중복 처리 차단
- 💾 **스토리지 최적화** — ELK `hot/warm/cold` ILM tier 로 로그 수명주기 자동 관리, 오래된 로그 저비용 노드로 이관
- 📈 **관측성 3층** — Prometheus(메트릭) + ELK(로그) + 커스텀 대시보드(상태) 로 6노드·40+ 서비스를 단일 화면에서 관제 


