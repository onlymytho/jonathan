---
name: sppt-order-collect
description: |
  멀티채널 주문을 한번에 모아보는 워크플로우.
  Use when asked to "주문 모아줘", "전채널 주문 조회", "오늘 주문 몇 개야".
allowed-tools:
  - Bash
  - Read
  - Glob
  - Grep
---

# order-collect

설정된 모든 판매 채널의 주문을 한번에 조회하고 통합 요약하는 워크플로우.

## When to use

- "오늘 주문 몇 개야?", "주문 모아줘", "전채널 주문 현황" 요청 시
- 매일 아침 주문 현황 파악할 때
- 멀티채널 주문을 한눈에 보고 싶을 때

## Prerequisites

- 최소 1개 판매 채널 설정 완료
- `sppt status`로 채널 연결 확인

## Steps

### 1. 채널 상태 확인

```bash
sppt status
```

연결된 채널 목록을 파악합니다. 연결 실패한 채널이 있으면 사용자에게 알립니다.

### 2. 채널별 주문 조회

연결된 각 채널에서 주문을 조회합니다:

```bash
sppt order list cafe24 --days 1
sppt order list coupang --days 1
sppt order list smartstore --days 1
sppt order list shopify --days 1
```

`--days` 기본값은 7일이지만, 일일 체크에는 `--days 1`을 사용합니다.
사용자가 특정 기간을 요청하면 해당 기간으로 조정합니다.

### 3. 통합 요약

채널별 결과를 통합하여 사용자에게 보여줍니다:

```
📦 주문 현황 (최근 1일)
─────────────────────────────────────
채널          신규    처리중    완료    합계
cafe24         3       2        5      10
coupang        5       1        8      14
smartstore     2       0        3       5
shopify        1       0        1       2
─────────────────────────────────────
합계          11       3       17      31
```

### 4. 미처리 주문 안내

신규(미확인) 주문이 있으면 즉시 처리가 필요함을 안내합니다:

- 미확인 주문 → `/sppt-order-fulfill`로 처리 안내
- 발주 확인 후 송장 미등록 → 송장 등록 필요 안내

### 5. 주문 상세 (선택)

사용자가 특정 주문을 더 보고 싶으면:

```bash
sppt order get <channel> <orderId>
```

## Error handling

- **채널 연결 실패**: 해당 채널 건너뛰고 나머지 채널 결과 표시, 재인증 안내
- **SmartStore 24시간 제한**: CLI가 자동으로 일 단위 순회하므로 걱정 불필요
- **Rate limit**: CLI rate limiter가 자동 처리

## Notes

- 매일 아침 체크 루틴으로 활용하면 효과적
- Coupang은 발주 확인 전에만 취소가 가능하므로 빠른 확인 중요
- `--json` 플래그로 JSON 출력 가능 (자동화/파이프라인 연동 시)
