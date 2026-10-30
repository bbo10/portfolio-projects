# Instacart 장바구니·RFM 분석 기반 재구매 전략

**질문:** 재구매를 늘리려면 주문 크기와 빈도 중 무엇을 키워야 할까?
**답:** 주문 빈도입니다. 장바구니 크기 구간과 관계없이 재구매율은 약 60%로 비슷했습니다.
→ 결과와 차트: 포트폴리오 사이트 (배포 후 링크)

2025.02.19 – 02.28 · 4인 팀 · 기여도 30% (데이터 전처리, 재구매 EDA) · 주문-상품 기록 33,819,106건, 고객 206,209명

주문-상품 기록 약 3,380만 건 규모의 데이터를 메모리 최적화(자료형 축소)와 함께 처리하기 위해 Python(pandas)으로 분석했습니다.

## 노트북 지도 (`instacart_6조.ipynb`)

| 셀 | 단계 | 내용 |
|---|---|---|
| 1–19 | 1. 전처리 | prior/train 주문-상품 통합, 상품·분류·요일 결합 → `instacart_total.csv` |
| 20–24 | 2. 가설 1 | 5개 상위 카테고리별 재구매율 |
| 25–34 | 2. 가설 2 | 장바구니 크기별 주문 수와 재구매율 |
| 35–46 | 2-2. 이상치 | IQR, Z-Score, 백분위수 비교 → 제거하지 않기로 결정 |
| 47–56 | 3. 연관규칙 | 최소 지지도 5%, 최소 신뢰도 30% |
| 57–91 | 4. RFM | R·F·M 기준, 점수, 세그먼트, 로그 변환 전후 비교 |

## 핵심 코드

**1. 이상치를 지우기 전에 확인하기** (셀 43)
상위 1%를 이상치로 표시한 뒤, 이 고객들의 재구매율을 따로 봤습니다. 재구매율이 78.9%로 일반 고객(44.1%)보다 높아 핵심 고객으로 남겼습니다.

```python
threshold = basket['basket_size'].quantile(0.99)
basket['percentile_outlier'] = np.where(
    basket['basket_size'] > threshold, 'outlier', 'normal')
```

**2. 메모리를 줄이며 카테고리별 재구매율 계산** (셀 22)
3천만 행이 넘는 데이터라 자료형을 줄여 계산했습니다.

```python
df['reordered'] = df['reordered'].astype('int8')
category_reorder_rate = (df.groupby('category')['reordered']
                           .mean().sort_values(ascending=False))
```

**3. 변환보다 원본 분포를 택한 이유** (셀 72)
이상치 제거와 로그 변환을 적용하면 자주 주문하는 핵심 고객의 차이가 작아져, 변환 전 결과를 쓰기로 했습니다.

## 재현 방법

- 데이터: Instacart 공개 주문 데이터(orders, order_products prior/train, products, aisles, departments)와 카테고리 매핑 파일 `category.csv`. 용량 문제로 저장소에는 포함하지 않았습니다.
- 라이브러리: pandas, NumPy, matplotlib, seaborn, plotly, mlxtend
- 데이터가 커서 메모리가 넉넉한 환경을 권장합니다.
