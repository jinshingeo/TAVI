# Thermal Catchment Area
**열환경을 반영한 도보 접근성 공간 단위 제안 및 지하철 역세권 적용**

> 석사 논문 연구 | 서울 성동구 | 업데이트: 2026-04-21

---

## 연구 개요

### 핵심 아이디어

기존 Catchment(역세권)는 **물리적 이동 거리**만 반영한다.  
본 연구는 **UTCI 기반 열환경 임계값**을 적용하여 폭염 시 보행 불가 링크를 제거한  
**Thermal Catchment Area**를 새로운 공간 접근성 단위로 제안한다.

```
Classic Catchment:  전체 네트워크 → Dijkstra → 시간예산 내 도달 노드셋
Thermal Catchment:  UTCI ≥ 38°C 링크 제거 → 축소 네트워크 → Dijkstra → 도달 노드셋

reduction_pct (%) = (Classic − Thermal) / Classic × 100
```

### 왜 하드 컷인가

기존 연구들은 UTCI를 **소프트 패널티**로 반영한다 — 이동속도를 늦추거나 경로 선택 확률을 낮추는 방식.  
이는 "더 오래 걸리지만 결국 도달 가능"하다는 전제를 깔고 있다.

본 연구는 **UTCI 38°C (Bröde et al. 2012 — "very strong heat stress")** 를 이진 임계값으로 적용한다.

> 이 기준 이상의 링크는 보행 자체가 중단된다는 보수적(conservative) 추정.  
> 실제 접근성 손실은 본 연구 결과보다 더 클 수 있다.

---

## 케이스 스터디

**서울 성동구 지하철역 7개** — Thermal Catchment 개념 검증용 사례

| 역 | 노선 | reduction_pct (13시) | 특성 |
|----|------|---------------------|------|
| **응봉역** | 경의중앙선 | **68.6%** | 교량 노출, 최고 취약 |
| **서울숲역** | 수인분당선 | **67.8%** | 공원 인접에도 노출 링크 많음 |
| 왕십리역 | 2·5호선 외 | 46.1% | 대형 환승역 |
| 행당역 | 5호선 | 41.1% | — |
| 옥수역 | 3호선·중앙선 | 29.8% | 한강 인접 |
| 뚝섬역 | 2호선 | 35.6% | — |
| **성수역** | 2호선 | **13.0%** | 하천·녹지 인접, 최저 취약 |

> 수치 출처: `catchment_solweig_summary.json` (SOLWEIG 기반 UTCI, 13시 기준)

---

## 선행연구 대비 위치

| 연구 | 열환경 지표 | 패널티 방식 | 분석 대상 | 새 공간 단위 |
|------|-----------|------------|---------|------------|
| Basu et al. (2024) *Cities* | UTCI | 소프트 (경로 확률) | 일반 보행권 | ❌ |
| Aydin et al. (2026) *SCS* | UTCI (CFD) | 소프트 (PTT 증가) | POI 접근성 | ❌ |
| Dong et al. (2024) *G&S* | LST (위성) | 없음 | UGS 접근성 | ❌ |
| Wang et al. (2025) *UC* | LST 기반 DI | 없음 | UGS 접근성 | ❌ |
| **본 연구** | **UTCI (SOLWEIG)** | **하드 컷 (이진)** | **지하철 역세권** | **✅** |

---

## 방법론

### UTCI 계산

```
기상 입력: Open-Meteo Archive API (2025-07-28~08-03, 7일 평균)
           Tair, RH, 풍속, GHI (성동구 중심 위치)

MRT 계산 (Lindberg et al. 2008 단순화):
  주간: MRT = Tair + 0.5 × √(GHI × cos_z) × SVF
  야간: MRT = Tair - 2.0 × SVF

캐노피 보정: Chen & Ng (2012) — 수목 차폐 최대 -2.5°C

UTCI: pythermalcomfort (Bröde et al. 2012 표준 다항식)
```

SVF(Sky View Factor)가 링크 간 UTCI 공간 변이의 핵심 결정 변수.

### 분석 파이프라인

```
01_네트워크/         OSM 보행 네트워크 구축 (성동구)
      ↓
04_분석결과/
  15_svf_per_link.py        SVF + 캐노피 링크별 계산
  19_solweig_utci.py        SOLWEIG 기반 링크별 UTCI 계산
  20_catchment_solweig.py   7개 역 Thermal Catchment 계산
  22_regression_analysis.py 역·링크 단위 회귀분석
```

---

## 디렉토리 구조

```
TAVI/
├── 01_네트워크/            OSM 보행 네트워크
├── 02_기상데이터/          기상 및 승하차 원시 데이터
├── 03_건물데이터/          건물 형태, DEM
├── 04_분석결과/            분석 스크립트 및 결과물
│   ├── 15_svf_per_link.py
│   ├── 19_solweig_utci.py
│   ├── 20_catchment_solweig.py
│   ├── 22_regression_analysis.py
│   ├── 25_vulnerability_component.py   (후속 연구용 보존)
│   ├── 26_tavi_index.py                (후속 연구용 보존)
│   ├── 27_link_criticality.py          (후속 연구용 보존)
│   └── figures/
├── 05_시각화/
├── docs/
│   ├── 2026-04-21_research_direction_update.md   ← 현재 연구 방향
│   ├── 2026-04-21_research_direction_update.docx
│   └── 2026-04-16_TAVI_analysis_guide.ipynb
├── 선행연구/
├── CLAUDE.md
├── ROADMAP.ipynb
└── README.md
```

---

## 남은 작업

| 우선순위 | 항목 | 상태 |
|---------|------|------|
| ★★★ | 일별 승하차 데이터 수집 (서울 열린데이터 OA-12914) | 미완 |
| ★★★ | 폭염일 vs 비폭염일 승하차 검증 분석 | 미완 |
| ★★ | Classic vs Thermal 2SFCA 비교 코드 | 미완 |
| ★★ | 민감도 분석 (임계값 35/38/41°C, 시간예산 10/15/20분) | 미완 |
| ★ | 영어 논문 초안 | 미완 |

---

## 버전 히스토리

| 버전 | 날짜 | 핵심 내용 |
|------|------|----------|
| STP_v3 | 2026-04-01 | ASOS 기반 UTCI, 응봉동·성수동 PPA 비교 |
| TAVI_v1 | 2026-04-10 | 연구 방향 전환 — Catchment 기반 분석 수립 |
| TAVI_v2 | 2026-04-16 | SOLWEIG UTCI 통일, H×E×V 프레임 도입 |
| **현재** | **2026-04-21** | **Thermal Catchment 개념 논문으로 확정, H×E×V 제거** |

---

## 투고 목표

- 1순위: **Landscape and Urban Planning** (SCI Q1)
- 2순위: **Urban Climate** (SCI Q1)
- 일정: 2026년 7월 중순

---

## 참고 문헌 (핵심)

- Bröde et al. (2012). Deriving the operational procedure for the Universal Thermal Climate Index (UTCI). *IJB*, 56, 481–494.
- Lindberg et al. (2008). SOLWEIG — Modelling spatial variations of 3D radiant fluxes. *IJB*, 52, 697–713.
- Basu et al. (2024). Hot and bothered: Measuring the impact of heat on walkability. *Cities*, 155, 105468.
- Aydin et al. (2026). UTCI-adjusted pedestrian accessibility. *Sustainable Cities and Society*.
- Dong et al. (2024). Measuring urban thermal environment from accessibility-based perspective. *Geography and Sustainability*, 5, 329–342.
- Wang et al. (2025). Supply and demand analysis of urban thermal environments. *Urban Climate*, 60, 102356.
