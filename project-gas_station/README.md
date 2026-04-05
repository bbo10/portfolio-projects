# Gas Station Optimization  
주유 의사결정을 위한 실질 비용 기반 시뮬레이션

## Overview
이 프로젝트는 “가장 저렴한 주유소가 항상 최적일까?”라는 질문에서 시작되었습니다.

단순 가격이 아닌  
**이동비, 연비, 혜택을 포함한 실질 총비용(Net Total Cost)** 기준으로  
주유 의사결정 모델을 설계했습니다.

---

## Problem

기존 의사결정 방식:

- 리터당 가격 기준 선택

문제:

- 이동 거리 비용 미반영
- 실제 총비용 왜곡

---

## Approach

### 1. 비용 구조 정의

Net Total Cost:

- 주유비
- 이동비 (거리 / 연비 × 가격)
- 혜택 (포인트 등)

---

### 2. 이동비 계산

- Haversine 거리 기반
- 실제 도로 환경 보정 적용

---

### 3. 의사결정 기준

최소 Net Total Cost를 가지는 주유소 선택

---

## Result

- 근거리 대비 약 2.7% 비용 절감
- 최저가 전략 대비 약 5.4% 절감

→ 단순 가격 전략보다 우수

---

## Guardrail

- 1km 추가 이동 시 최소 4.5원/L 절감 필요

→ 이동 한계 기준 설정

---

## System

- 입력:
  - 연비
  - 주유량
  - 위치

- 출력:
  - 최적 주유소
  - 예상 비용

---

## Key Takeaways

- 단일 지표 최적화는 잘못된 의사결정을 유도할 수 있음
- 비용 구조를 통합한 기준 설계가 중요
- 분석 결과를 즉시 활용 가능한 형태로 만드는 것이 핵심

---

## Tech Stack
Python, Kakao API, OpiNet API  
Geospatial Analysis, Simulation, Feature Engineering
