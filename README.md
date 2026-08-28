![방문](https://komarev.com/ghpvc/?username=MyoungSoo7&label=%ED%94%84%EB%A1%9C%ED%95%84%20%EB%B0%A9%EB%AC%B8&color=blue&style=flat-square)
[![블로그 방문자](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Flemuel.goatcounter.com%2Fcounter%2FTOTAL.json&query=%24.count_unique&label=%EB%B8%94%EB%A1%9C%EA%B7%B8%20%EB%B0%A9%EB%AC%B8%EC%9E%90&color=blue&style=flat-square)](https://myoungsoo7.github.io/)
[![블로그 글](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fmyoungsoo7.github.io%2Fsearch.json&query=%24.length&label=%EB%B8%94%EB%A1%9C%EA%B7%B8%20%EA%B8%80&color=blue&style=flat-square)](https://myoungsoo7.github.io/)
[![공개 리포](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fapi.github.com%2Fusers%2FMyoungSoo7&query=%24.public_repos&label=%EA%B3%B5%EA%B0%9C%20%EB%A6%AC%ED%8F%AC&color=blue&style=flat-square&cacheSeconds=3600)](https://github.com/MyoungSoo7?tab=repositories)

서버 여섯 대로 K3s 클러스터를 굴리고, 그 위에 이커머스 정산 MSA 를 올려 운영합니다. 인프라 배선부터 도메인 코드까지 혼자 세우고, 무너지지 않게 게이트로 지킵니다.

## 클러스터
노드 6대(74 vCPU / 122 GiB)에 네임스페이스 46개. Deployment 83 · StatefulSet 20 · DaemonSet 6 · CronJob 13, Running 파드 145개를 ArgoCD Application 60개를 운영중입니다.
지금 돌아가는 서비스 목록은 [www.lemuel.co.kr](https://www.lemuel.co.kr) 에 한 페이지로 정리해 뒀습니다.

## 메인 프로젝트( 5개의 java 및 go, python 7개의 msa 폴리글랏 서비스)
[jen.lemuel.co.kr](https://jen.lemuel.co.kr/) 에서 결과적 일관성을 보장하는 분산 트랜잭션 처리를 구축했습니다.
 
## AI 에이전트를 이 리포에 붙이면서 만든 것
LLM 에이전트한테 코드를 맡기려면 규칙을 문서로 적는 걸로는 부족합니다. 문서는 읽힐 때도 있고 아닐 때도 있으니, 기계가 강제하는 층을 따로 만들었습니다.
어떻게 구성했고 한계는 무엇인가는 [블로그](https://myoungsoo7.github.io/2026/08/18/what-a-harness-score-of-8-6-actually-measures/)에 근거와 함께 적어 뒀습니다.

## 그 밖에(Harness Engineering)
- [asat.lemuel.co.kr](https://asat.lemuel.co.kr/) — 청각 재활 훈련(jnd 알고리즘)
- [xr.lemuel.co.kr](https://xr.lemuel.co.kr/) — XR, 영적 훈련(vr)
- [shop.lemuel.co.kr](https://shop.lemuel.co.kr/) — 쇼핑몰(이커머스 도메인)
## Before Claude(original coding)
- [news.lemuel.co.kr](https://asat.lemuel.co.kr/) — 뉴스(python)
- [sns.lemuel.co.kr](https://asat.lemuel.co.kr/) — sns(kafka)

결정과 이유는 ADR 로 적고, 문서의 수치에는 다시 세는 명령을 같이 붙입니다. 그때그때 배운 건 [블로그](https://myoungsoo7.github.io/)에 정리합니다.
