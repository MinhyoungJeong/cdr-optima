

# CDR Optima — 🌍 Carbon Removal Portfolio Optimization Platform

Built with Python, FastAPI, Next.js, PostgreSQL, and Machine Learning.

# 탄소 제거(CDR) 크레딧 포트폴리오 AI 최적화 의사결정 시스템

기업이 Net-Zero 목표를 달성하기 위해 CDR 크레딧을 구매할 때, 예산·영속성·기술 다각화 제약 조건을 동시에 만족하는 최적의 포트폴리오를 자동으로 산출하고, 그 근거(시장 데이터·공식 문서·통계 분석)까지 투명하게 제시하는 AI 기반 의사결정 지원 시스템입니다.

🎥 Demo
https://youtu.be/DTGAUNjjDIw

✨ Velog
https://velog.io/@happyrachel/series/Carbon-Removal-Project

<br>

## 📷 Screenshots

### 1. Landing Page
<img width="1454" height="921" alt="스크린샷 2026-09-04 오후 3 09 50" src="https://github.com/user-attachments/assets/155a45e2-b14e-40df-bab1-33d0a8fb44ae" />

### 2. Market Insights 
 — 시장 온보딩
방법론 구성 · 영속성 분포 · 공급 집중도 · 연도별 성장 추이를 카드 형식으로 안내

<img width="1454" height="921" alt="스크린샷 2026-09-04 오후 3 11 25" src="https://github.com/user-attachments/assets/f0444cca-5983-4a52-831d-2fd57f33c52b" />
<img width="1454" height="921" alt="스크린샷 2026-09-04 오후 3 12 07" src="https://github.com/user-attachments/assets/d074422a-7759-4039-bdcc-da7d47e10a77" />

### 3. Optimize — 포트폴리오 빌더 (4-Step Wizard)
Company → Budget → Constraints → Review
<img width="1138" height="921" alt="CDR Optima" src="https://github.com/user-attachments/assets/db5d31a8-309f-4ab4-955c-916304f54661" />

### 4. Results — 최적화 로딩 페이지 및 결과
KPI 요약 · 선택된 프로젝트 테이블 · AI Portfolio Analysis

<img width="1454" height="921" alt="스크린샷 2026-09-04 오후 3 13 18" src="https://github.com/user-attachments/assets/4ed045f6-ec3b-4fa2-b862-8594b2f9e7a2" />

고객의 원하는 조건에 맞는 최적해가 없을 시 -> Goal Programming으로 가장 비슷한 해를 찾음 
(최적해가 있을 경우 -> 최적해로 출력) 

<img width="1454" height="921" alt="스크린샷 2026-09-04 오후 3 13 46" src="https://github.com/user-attachments/assets/57d5bc8f-ff63-4591-951a-119e05b90feb" />

<img width="1454" height="921" alt="스크린샷 2026-09-04 오후 3 15 26" src="https://github.com/user-attachments/assets/ca98b540-9757-4c99-9053-5a5232211d4d" />


### 5. Methodology & Evidence &  Portfolio Ask — AI 챗봇
RAG로 검색한 공식 방법론 문서 요약 + 출처 인용
포트폴리오 데이터 · 방법론 문서 · 클러스터링 결과에 근거해서만 답변
<img width="1141" height="864" alt="스크린샷 2026-09-04 오후 3 18 05" src="https://github.com/user-attachments/assets/c02e4177-ad55-455b-9bd3-4402786d1be6" />

### 6. ML Analytics
K-means · Activity Segments (2D/3D) · PCA 해석 · Lead Time 비교
<img width="962" height="689" alt="스크린샷 2026-09-04 오후 3 16 56" src="https://github.com/user-attachments/assets/03a5f309-3847-4057-b0ce-b1040c0bfe5e" />

### 7. My Page
누적 포트폴리오 대시보드 · PDF 리포트 다운로드
<img width="930" height="921" alt="스크린샷 2026-09-04 오후 3 20 00" src="https://github.com/user-attachments/assets/54ac498f-1a61-4861-8348-65552ca4210b" />


<br>

## 🎯 프로젝트 배경

자발적 탄소시장(VCM)에서 기업이 CDR 크레딧을 구매할 때 직면하는 문제:

- 96개 프로젝트 중 어떤 조합이 **예산 안에서 최적**인가
- 영속성(20~1,000년+)·기술 다양성 등 **다중 제약을 동시에 만족**하는 포트폴리오를 어떻게 구성하는가
- 수학적으로 최적인 결과가 **시장에서도 신뢰할 수 있는 근거**를 갖는가

이 문제를 **MILP 최적화 + 시장 세그먼트 분석(K-means·PCA) + RAG 기반 문서 근거 + LLM 설명·챗봇**의 다층 구조로 해결합니다.

<br>

## 🏗️ 시스템 아키텍처

```
[Puro.earth Registry]
   ├─ Retirement Export ──────────┐
   ├─ Issuance Export ──┐         │
   └─ Project Export ───┤         │
                         ▼         ▼
              [Market Insights]  [Data Pipeline]
              (mtime 캐시 →      transactions_raw.csv (거래 단위)
               CSV 교체 시        + projects_milp.csv (프로젝트 파생)
               재시작 없이 반영)      ↓
                                DataLoader
                            (creditType 기반 durability_score 산출)
                                   ↓
                    ┌──────────────┼──────────────┐
                    ▼              ▼              ▼
             MILP Optimizer   K-means + PCA   RAG (ChromaDB)
             (PuLP + CBC)     (Activity        + LLM 설명
             Goal Programming  Segment 분류)    (방법론 문서 근거)
                    │              │              │
                    └──────────────┼──────────────┘
                                   ▼
                          [FastAPI Backend]
   POST /api/v1/optimize                → MILP 최적화
   GET  /api/v1/market-insights         → 시장 통계 (mtime 캐시)
   GET  /api/v1/ml/k-means              → K-means·PCA 결과 + 해석
   POST /api/v1/portfolios              → 결과 저장 (PostgreSQL)
   GET  /api/v1/portfolios/{id}/pdf     → PDF 리포트
   POST /api/v1/portfolios/{id}/ask     → 저장된 포트폴리오 Q&A
   POST /api/v1/portfolios/ask-preview  → 미저장 결과 Q&A
                                   ↓
                          [Next.js Frontend]
   Landing → Market Insights → Optimize 위저드 → Results
   (K-means/PCA 시각화 · Methodology & Evidence · Portfolio Ask 챗봇) → My Page
```

<br>

## 핵심 기능

### 1. MILP 포트폴리오 최적화
- 예산 / 목표 제거량 / 방법론별 집중 한도 / 포트폴리오 평균 영속성 / 최소 프로젝트 수 등 다중 제약 하 총 구매 비용 최소화 (PuLP + CBC Solver)
- 최적해가 없을 경우, 목표계획법(Goal Programming)으로 제약별 편차 변수를 최소화하여 병목 제약을 진단하고 완화안을 제시
- 프로젝트 영속성(`permanence_years`)은 후보 필터로 하드 컷 적용

### 2. 시장 세그먼트 분석 (K-means · PCA)
- 96개 프로젝트를 대상으로 K-means 군집화(LOW/MEDIUM/HIGH_ACTIVITY 3세그먼트)
- 동일 피처 공간에서 PCA 3축으로 투영, **각 축의 로딩(loading) 기반 해석 문장을 자동 생성**
- 선택된 포트폴리오 vs 전체 시장 비교 규칙(세그먼트 쏠림, 은퇴 소요일 편차, 단일 프로젝트 의존도)으로 인사이트 자동 서술
- 2D/3D 시각화 토글 지원

### 3. 영속성(Durability) 산정 — creditType 기반
- 기존 methodology 하드코딩 매핑 방식에서, **거래 데이터의 공식 등급(creditType: CORC / CORC 20+ / CORC 100+ / CORC 1000+)** 중 프로젝트별 최신 발행분 기준으로 산정하는 방식으로 전환
- 로그 변환 + Min-Max 정규화로 `durability_score`(0~1) 산출, K-means 피처 및 MILP 제약에 공통 사용

### 4. 소진속도(Lead Time) 통계
- 기존 Random Forest 기반 velocity score 예측은 Data Leakage 우려로 **화면에서 제거**(백엔드 로직은 보존)
- 대신 `avg_days_to_retire = completedOn − issuanceDate`의 프로젝트별 통계를 96개 전체 가로 막대 그래프로 제공, 시장 평균 대비 상대 비교 표시

### 5. 방법론 근거 제공 (RAG)
- 방법론 공식 PDF를 400단어 단위 / 80단어 오버랩으로 청킹, `all-MiniLM-L6-v2`(Sentence-Transformers)로 임베딩하여 ChromaDB에 색인
- Methodology & Evidence 아코디언에 기술개요·영속성 근거·인증구조·리스크 4개 섹션을 사전 정의 쿼리로 검색해 요약 제공, 방법론별 대표 이미지 병기

### 6. Portfolio Ask — 근거 기반 AI 챗봇
- 포트폴리오 결과 화면 전용 플로팅 위젯. 질문 유형에 따라 3개 도구 중 선택 호출:
  1. `get_portfolio_context` — 저장된 MILP 결과 수치 조회 (재계산 없음)
  2. `search_methodology_docs` — 선택된 프로젝트의 방법론 문서만 RAG 검색
  3. `get_clustering_context` — K-means·PCA 해석/인사이트 조회
- 근거를 찾지 못하면 지어내지 않고 정직하게 거절, 저장 전(preview) / 저장 후(saved) 포트폴리오 모두 질의 가능

### 7. Market Insights — 시장 온보딩
- Optimize 이전에 CDR 시장 규모·방법론 구성·영속성 분포·공급 집중도·연도별 성장·지역별 수요/공급을 카드 형식으로 안내
- CSV 교체만으로 통계가 즉시 갱신되는 mtime 기반 캐시 (서버 재시작 불필요)

### 8. 포트폴리오 저장 · 리포트
- 최적화 결과를 PostgreSQL에 저장, 마이페이지에서 누적 대시보드·개별 리포트 조회
- PDF 리포트 다운로드 (한글 폰트 지원)
- LLM 기반 AI Portfolio Analysis 요약 자동 생성

<br>

## 🛠️ 기술 스택

### Backend
![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat&logo=fastapi&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=flat&logo=postgresql&logoColor=white)
![SQLAlchemy](https://img.shields.io/badge/SQLAlchemy-D71F00?style=flat)

### ML / Optimization / RAG
![scikit-learn](https://img.shields.io/badge/scikit--learn-F7931E?style=flat&logo=scikit-learn&logoColor=white)
![PuLP](https://img.shields.io/badge/PuLP-MILP+GoalProgramming-green?style=flat)
![ChromaDB](https://img.shields.io/badge/ChromaDB-vector--search-purple?style=flat)
![Sentence-Transformers](https://img.shields.io/badge/Sentence--Transformers-all--MiniLM--L6--v2-orange?style=flat)

### Frontend
![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat&logo=next.js&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat&logo=typescript&logoColor=white)
![Recharts](https://img.shields.io/badge/Recharts-22B5BF?style=flat)

### Infra / DB
![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)

<br>

## 📊 데이터

- **출처**: [Puro.earth Registry](https://registry.puro.earth/) — Retirement / Issuance / Project Export 3종
- **규모**: 은퇴(Retirement) 거래 2,078건 / 96개 프로젝트 / 방법론 약 7~8종
- **용도 구분**:
  - Retirement Export → MILP 최적화, K-means·PCA, Lead Time 통계의 단일 소스 (`transactions_raw.csv`)
  - Issuance + Project Export → Market Insights 전용 (`issuance_raw.csv`, `project_raw.csv`), Retirement 데이터와는 공유하되 사본을 만들지 않고 재사용
- **집계 단위**: 거래 단위(2,078건)가 아닌 **프로젝트 단위(96개)로 집계**하여 사용 — 거래 빈도가 높은 프로젝트가 군집화에서 과대 대표되는 표본 중복(pseudo-replication) 문제 방지
- **전처리**: `days_to_retire = completedOn − issuanceDate` 파생, creditType 기반 영속성 산정, StandardScaler 표준화

⚠️ 실거래 데이터 보안상 CSV 원본은 저장소에 포함되지 않습니다.

<br>

## 📁 프로젝트 구조

```
cdr_optima/
├── api/
│   ├── routes_optimize.py          # MILP / GP 최적화 엔드포인트
│   ├── routes_ml.py                # K-means · PCA 결과 제공
│   ├── routes_market_insights.py   # Market Insights 통계 API (mtime 캐시)
│   ├── routes_portfolios.py        # 포트폴리오 저장·조회·PDF·Ask
│   ├── routes_explain.py           # 선택 근거 설명(LLM)
│   ├── routes_projects.py
│   └── routes_auth.py
├── services/
│   ├── milp_optimizer.py           # MILP + Goal Programming
│   ├── kmeans_clustering.py        # RiskClusterer + PCA + 축 해석 생성
│   ├── random_forest.py            # VelocityScoreModel (백엔드 보존, 화면 미노출)
│   ├── data_loader.py              # DataLoader (creditType 기반 durability 산출 포함)
│   ├── feature_engineering.py
│   ├── market_insights.py          # Market Insights 통계 서비스
│   ├── portfolio_ask.py            # 3-tool 라우팅 챗봇
│   ├── llm_explainer.py            # AI Portfolio Analysis 생성
│   ├── certification.py            # min_project_years → 인증 등급 서버 계산
│   ├── portfolio_pdf.py            # PDF 리포트 생성
│   └── methodology_rag/
│       ├── indexer.py              # PDF 청킹(400 word/80 overlap) + 임베딩 색인
│       ├── retriever.py            # 벡터 검색 + 인용 생성
│       └── catalog.py
├── models/
│   ├── schemas.py                  # Pydantic 스키마
│   └── (ORM)                       # User · Portfolio · PortfolioProject
├── scripts/
│   ├── generate_projects_milp.py   # 월간 데이터 갱신 자동화
│   └── verify_permanence_fix.py    # durability 산정 방식 전/후 비교 검증
└── data/
    ├── transactions_raw.csv        # Retirement (git 제외)
    ├── projects_milp.csv           # MILP 파생 (git 제외)
    ├── market/                     # Issuance · Project raw (git 제외)
    └── chroma_methodology/         # RAG 벡터 인덱스
```

<br>

## 담당 파트

| 영역 | 담당 내용 |
|---|---|
| 데이터 파이프라인 | Puro.earth 3종 데이터 수집·전처리, 프로젝트 단위 집계 설계 |
| ML 모델링 | K-means·PCA 세그먼트 분석, durability 산정 방식 검증·개선 |
| 백엔드 | FastAPI 서버, MILP/GP 최적화 엔진, RAG·챗봇 라우팅, PDF 생성 |
| 프론트엔드 | Next.js Market Insights·Optimize 위저드·Results 대시보드·챗봇 위젯 |
| 데이터 분석 | EDA, 상관관계 분석, 클러스터링 검증(Silhouette·Gap Statistic·VIF) (Velog 연재) |

<br>

## 분석 블로그 (Velog)

- [1편] 프로젝트 기획 및 개요
- [2편] 데이터 파이프라인 — 수치형 변수 기초 통계 및 시각화
- [3편] CDR Projects 상관계수 분석 (Pearson · Spearman)
- [4편] K-means 세그먼트 검증 (Silhouette · Gap Statistic · 다중공선성 분석)

<br>

## 개발 기간
2026.03 — 2026.09 (졸업 프로젝트)

## 팀 구성
3인 팀 (백엔드 · 프론트엔드 · ML/데이터 분석)

---

본 저장소는 포트폴리오 목적으로 공개하며, 관련 공식문서 및 csv파일은 저작권 문제로 공개하지 않습니다. 감사합니다. 
