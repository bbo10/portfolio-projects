# 손익 기준 신용 승인 정책 설계

**질문:** 정확한 모델이 손익도 좋은 승인 정책일까?
**답:** 승인율 60% 이상을 지키면서 Expected Profit이 가장 큰 RandomForest + Threshold 0.35를 골랐습니다. Validation에서는 좋았지만 Test에서 흔들려, 전면 도입 대신 20% A/B 테스트를 설계했습니다.
→ 결과와 차트: 포트폴리오 사이트 (배포 후 링크)

2025.01.16 – 01.24 · 4인 팀 · 기여도 25% (EDA, Threshold 설계 방향 제안) · German Credit 공개 데이터 1,000건

## 노트북 지도 (`final-python-credit.ipynb`)

| 셀 | 단계 | 내용 |
|---|---|---|
| 1–16 | step 1 | 가설 기반 EDA (대출 금액·기간, 주거 형태, 연령·직업별 리스크) |
| 17–32 | step 2 | 결측 보완(그룹 최빈값), 파생 변수, 룰 기반 정책 비교 |
| 33–60 | step 3 | RandomForest vs XGBoost, Threshold별 정책 성능 |
| 61–69 | step 4 | 모델 성능을 손익(Expected Profit)으로 환산 |
| 70–74 | step 4-2 | 승인율 제약(80% / 60% / 40%)별 최적 Threshold |
| 75–112 | step 5 | 손실 가정 민감도, Test 1회 평가, Control vs Treatment, A/B 테스트 표본 크기 |

## 핵심 코드

**1. 정확도 대신 손익으로 평가하기** (셀 64)
승인 1건은 +1, 부실 승인 1건은 −5로 두고 정책을 돈으로 비교합니다.

```python
def evaluate_profit(y_true, proba, threshold, gain=1, loss=5):
    y_pred = (proba >= threshold).astype(int)
    approved = (y_pred == 0)                      # 승인 = 예측 good(0)
    total_approved = approved.sum()
    bad_approved = ((y_true == 1) & approved).sum()
    expected_profit = total_approved * gain - bad_approved * loss
    return total_approved, bad_approved, expected_profit
```

**2. 운영 제약 안에서 Threshold 고르기** (셀 72)
승인율이 너무 낮으면 운영할 수 없는 정책이 되므로, 승인율 60% 이상인 후보 중에서만 손익이 가장 큰 값을 고릅니다. 선택은 Validation 데이터로만 했습니다.

```python
min_approval = 0.60
cand = merged[merged["rf_approval"] >= min_approval].copy()
best_rf = cand.loc[cand["rf_profit"].idxmax(),
                   ["threshold", "rf_approval", "rf_bad_recall", "rf_profit"]]
```

## 재현 방법

- 데이터: German Credit 공개 데이터 (1,000행, 10컬럼). 저장소에는 포함하지 않았습니다.
- 라이브러리: pandas, NumPy, scikit-learn, XGBoost, SciPy, matplotlib
- 노트북을 위에서부터 순서대로 실행합니다.
