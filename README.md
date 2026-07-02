<div align="center">

# 👋 안녕하세요, 임명수입니다 

### 백엔드 · 인프라 엔지니어 &nbsp;|&nbsp; *직접 만들고, 직접 굴리는* 개발자

집에 **6노드 K3s 클러스터**를 세워 두고 그 위에서 서비스를 운영합니다.<br/>
헥사고날 아키텍처와 이벤트 기반 MSA, 그리고 GitOps로 *"코드부터 프로덕션까지"* 를 손으로 만져 봅니다.

<br/>

[![Blog](https://img.shields.io/badge/Tech_Blog-000000?style=for-the-badge&logo=github&logoColor=white)](https://MyoungSoo7.github.io/)
[![Homepage](https://img.shields.io/badge/lemuel.co.kr-4285F4?style=for-the-badge&logo=googlechrome&logoColor=white)](https://www.lemuel.co.kr/)
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

## 🛠 Tech Stack

**Backend**

![Java](https://img.shields.io/badge/Java-007396?style=flat-square&logo=openjdk&logoColor=white)
![Kotlin](https://img.shields.io/badge/Kotlin-7F52FF?style=flat-square&logo=kotlin&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-6DB33F?style=flat-square&logo=springboot&logoColor=white)
![JPA](https://img.shields.io/badge/JPA/Hibernate-59666C?style=flat-square&logo=hibernate&logoColor=white)
![Kafka](https://img.shields.io/badge/Apache_Kafka-231F20?style=flat-square&logo=apachekafka&logoColor=white)

**Architecture**

![Hexagonal](https://img.shields.io/badge/Hexagonal-FF6B6B?style=flat-square&logo=buffer&logoColor=white)
![MSA](https://img.shields.io/badge/MSA-4A90D9?style=flat-square&logo=apachespark&logoColor=white)
![Event-Driven](https://img.shields.io/badge/Event--Driven-8E44AD?style=flat-square&logo=amazonsqs&logoColor=white)
![DDD](https://img.shields.io/badge/DDD-2C3E50?style=flat-square&logo=domino&logoColor=white)

**Infra & DevOps**

![K3s](https://img.shields.io/badge/K3s-FFC61C?style=flat-square&logo=k3s&logoColor=black)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![ArgoCD](https://img.shields.io/badge/Argo_CD-EF7B4D?style=flat-square&logo=argo&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white)
![Cloudflare](https://img.shields.io/badge/Cloudflare_Tunnel-F38020?style=flat-square&logo=cloudflare&logoColor=white)

**Data & Observability**

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat-square&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat-square&logo=redis&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat-square&logo=mysql&logoColor=white)
![Elasticsearch](https://img.shields.io/badge/Elasticsearch-005571?style=flat-square&logo=elasticsearch&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=flat-square&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-F46800?style=flat-square&logo=grafana&logoColor=white)

**Frontend**

![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=nextdotjs&logoColor=white)
![React](https://img.shields.io/badge/React-61DAFB?style=flat-square&logo=react&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)

---

## 🏠 Homelab — *내 손으로 만든 프로덕션*

> 클라우드에 카드를 긋는 대신, **집에 클러스터를 세웠습니다.** 배포·모니터링·장애 대응을 전부 직접.

```
┌─ Homelab K3s Cluster ────────────────────────────────┐
│  🖥  Nodes        6 / 6 Ready  (노트북 · 맥미니 · 데스크탑) │
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

## 🚀 Featured Projects

- **[settlement](https://github.com/MyoungSoo7/settlement)** — 주문·결제·정산·승인 시스템. *헥사고날 아키텍처 · Transactional Outbox · Triple Idempotency · ArchUnit* 으로 경계를 컴파일러처럼 강제
- **[approval](https://github.com/MyoungSoo7/approval)** — 결재 승인 시스템 (Kotlin · Spring Boot 3.5 · PostgreSQL)
- **[sns](https://github.com/MyoungSoo7/approval)** — 결재 승인 시스템 (Kotlin · Spring Boot 3.5 · PostgreSQL)
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
