---
name: sppt-settlement-report
description: |
  멀티채널 정산 현황 조회 + 수수료 계산 + 수익 요약 워크플로우.
  Use when asked to "정산 확인", "이번 달 수익", "수수료 얼마야", "실수령액".
allowed-tools:
  - Bash
  - Read
  - Glob
  - Grep
---

# settlement-report

멀티채널 정산 현황을 조회하고 수수료/실수령액을 계산하는 워크플로우.

## When to use

- "이번 달 정산 확인해줘", "수수료 얼마나 나가?", "실수령액 계산해줘"
- 채널별 정산 비교가 필요할 때
- 수수료 규칙을 설정/변경할 때

## Prerequisites

- 최소 1개 판매 채널 설정 완료
- 수수료 규칙 설정 권장: `sppt settlement fee list`

## Steps

### 1. 수수료 규칙 확인

```bash
sppt settlement fee list
```

채널별 수수료 규칙이 설정되어 있는지 확인합니다.
없으면 사용자에게 수수료율을 물어보고 설정합니다:

```bash
sppt settlement fee set coupang --rate 10.8
sppt settlement fee set cafe24 --rate 3.3
sppt settlement fee set smartstore --rate 5.5
```

### 2. 정산 요약 조회

```bash
sppt settlement summary --days 30
```

채널별 정산 합계를 조회합니다. 특정 채널만 보려면:

```bash
sppt settlement summary coupang --days 30
```

### 3. 정산 목록 상세

```bash
sppt settlement list --days 30
```

건별 정산 내역을 확인합니다.

### 4. 개별 주문 정산 계산

특정 주문의 수수료/실수령액을 계산합니다:

```bash
sppt settlement calc coupang <orderId> --amount 29900
```

출력 예시:
```
정산 계산: coupang 주문 #12345
──────────────────────────
판매가:       29,900원
수수료 (10.8%): -3,229원
배송비:       -3,000원
──────────────────────────
실수령액:     23,671원
마진율:       20.8%
```

### 5. 결과 요약

사용자에게 채널별 정산 비교표를 제공합니다:

```
📊 정산 요약 (최근 30일)
────────────────────────────────────────
채널          매출      수수료     실수령    마진율
cafe24       850,000   28,050    821,950   96.7%
coupang    2,150,000  232,200  1,917,800   89.2%
smartstore   620,000   34,100    585,900   94.5%
────────────────────────────────────────
합계       3,620,000  294,350  3,325,650   91.9%
```

## Error handling

- **수수료 규칙 미설정**: 채널 기본 수수료율로 안내 후 설정 유도
- **정산 데이터 없음**: 조회 기간 확대 또는 주문 데이터 임포트 안내

## Notes

- 수수료율은 채널·카테고리·판매자 등급에 따라 다를 수 있음
- PostgreSQL 연결 시 정산 이력이 자동 누적됩니다
- 매월 정산 리포트로 채널별 수익성 비교 권장
