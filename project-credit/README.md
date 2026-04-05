# Credit Risk Policy Optimization  
신용 승인 정책 고도화: 리스크 예측 모델과 손익 기반 의사결정 설계

## Overview
이 프로젝트는 신용 리스크 예측을 단순 분류 문제가 아닌  
**손익 기반 의사결정 문제**로 정의하고, 운영 가능한 승인 정책을 설계하는 것을 목표로 했습니다.

모델의 정확도를 높이는 것보다  
**Expected Profit과 승인율 제약을 동시에 만족하는 Threshold 설계**에 집중했습니다.

---

## Problem
신용 승인에서는 단순 정확도로 정책을 결정할 수 없습니다.

- 부실 승인 1건이 여러 건의 정상 수익을 상쇄
- 높은 Accuracy가 실제 손익 악화로 이어질 수 있음

따라서 다음 기준이 필요했습니다:

- Expected Profit (핵심 KPI)
- 승인율 가드레일 (운영 제약)
- Bad Approval Rate / Bad Recall (리스크 지표)

---

## Approach

### 1. 데이터 및 전처리
- German Credit 데이터 (1,000건)
- 클래스 불균형 (Bad 약 30%)
- 결측치 집중 컬럼 → 그룹 기반 최빈값 대체

→ 단순 dropna 대신  
**구조적 결측 보완 전략 적용**

### 2. Feature Engineering
- Credit_ratio = Credit amount / Duration

→ 모델이 실제로 가장 중요하게 사용한 변수

---

### 3. 모델링
- RandomForest, XGBoost 비교
- 출력 확률 기반 정책 정의

```python
if P(bad) ≥ threshold:
    Reject
else:
    Approve
