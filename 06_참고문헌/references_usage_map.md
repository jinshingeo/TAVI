# 참고문헌 사용 위치 매핑

> 코드 파일별로 어떤 문헌이 어떤 목적으로 사용됐는지 정리

---

## 코드 파일별 사용 문헌

| 파일 | 관련 문헌 | 사용 목적 |
|---|---|---|
| `11_thermal_catchment.py` | Hägerstrand (1970), Miller (1991) | STP → catchment 개념 |
| `11_thermal_catchment.py` | Moreno et al. (2021) | TIME_BUDGET = 15분 |
| `11_thermal_catchment.py` | Bröde et al. (2012) | UTCI 카테고리 → penalty(0~4) |
| `12_catchment_all_stations.py` | 위와 동일 | 7개 역 전체 확장 |
| `13_spatial_env_variables.py` | Oke (1987) | catchment zone 정의 |
| `15_svf_per_link.py` | **Oke (1987)** | H/W → SVF 공식 |
| `16_utci_link_corrected.py` | Lindberg & Grimmond (2011) | SVF_COEFF = 8.0°C |
| `16_utci_link_corrected.py` | Chen & Ng (2012) | CANOPY_COEFF = 2.5°C |
| `16_utci_link_corrected.py` | Bröde et al. (2012) | 임계값 38°C (very strong heat stress) |
| `17_catchment_corrected.py` | Oke (1987), Bröde et al. (2012) | 링크 회피 모델 |

---

## 핵심 파라미터 출처 추적

| 파라미터 | 값 | 출처 | 확보 여부 |
|---|---|---|---|
| TIME_BUDGET | 15분 | Moreno et al. (2021) | ✅ |
| WALK_SPEED | 4.5 km/h | WHO / 교통공학 일반 기준 | ✅ |
| UTCI 카테고리 경계 | 26/32/38/46°C | Bröde et al. (2012) | ✅ |
| UTCI 임계값 (링크 제거) | 38°C | Bröde et al. (2012) "very strong heat stress" | ✅ |
| SVF 공식 | H/W canyon | Oke (1987) | ✅ |
| SVF_COEFF | 8.0°C | Lindberg & Grimmond (2011) | ✅ (근사값) |
| CANOPY_COEFF | 2.5°C | Chen & Ng (2012) | ✅ (근사값) |
| α (열 패널티 가중치) | 0.15 | **미확보** | ❌ 확인 필요 |
| 도로 폭 (road type별) | 각 유형별 | 국토부 도로설계기준 | ⚠️ 확인 필요 |

---

## 논문 본문에서 인용이 필요한 위치

### 연구 방법 섹션

1. **STP/Catchment 정의**
   > "본 연구에서는 Hägerstrand(1970)의 Space-Time Prism 개념과 Miller(1991)의 네트워크 기반 PPA 분석을 적용하여..."

2. **15분 시간예산**
   > "도보 접근성의 시간예산은 Moreno et al.(2021)의 '15분 도시' 개념에 근거하여 15분(900초)으로 설정하였다."

3. **UTCI 열스트레스 카테고리**
   > "UTCI 값은 Bröde et al.(2012)의 국제 표준 카테고리에 따라 38°C 이상을 '매우 강한 열스트레스(very strong heat stress)'로 정의하고, 해당 링크를 보행 회피 구간으로 설정하였다."

4. **SVF 계산**
   > "링크별 SVF는 Oke(1987)의 도시 협곡 공식 SVF = 1/√(1+(H/W)²)을 적용하여 산출하였다. H는 링크 버퍼 20m 내 건물 평균 높이(층수×3m), W는 도로 유형별 표준 폭으로 설정하였다."

5. **UTCI 보정**
   > "SVF에 의한 UTCI 보정은 Lindberg & Grimmond(2011)의 연구를 근거로 완전 개활지 대비 최대 8°C 감소를 적용하였으며, 수목 캐노피에 의한 추가 보정은 Chen & Ng(2012)를 참조하여 완전 피복 시 2.5°C로 설정하였다."
