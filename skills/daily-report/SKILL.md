---
name: sppt-daily-report
description: |
  매일 아침 매출/주문 현황 브리핑 워크플로우.
  Use when asked to "데일리 리포트", "오늘 브리핑", "매출 요약해줘", "아침 리포트".
---

# daily-report

매일 아침 매출/주문 현황을 브리핑 형식으로 요약하는 워크플로우.

## When to use

- "데일리 리포트", "오늘 매출", "아침 브리핑" 요청 시
- 매일 아침 운영 현황을 한눈에 파악하고 싶을 때

## Prerequisites

- 최소 1개 판매 채널 설정 완료

## Steps

### 1. 데이터 수집

커맨드 **1회**로 모든 데이터를 가져옵니다:

```bash
sppt report daily --json
```

옵션:
- `--period 7d` (기본값) — 기간 변경 가능
- `--top 3` (기본값) — 상품 순위 개수

### 2. 브리핑 포맷으로 출력

JSON 응답을 파싱하여 아래 포맷으로 출력합니다.
매출 금액은 천 단위 콤마 포맷을 사용합니다.
인사말은 시간대에 맞게 조정합니다 (아침/오후/저녁).

```
안녕하세요. 좋은 아침이에요. 이번 주 매출 현황을 요약해 드릴게요.

- 총 판매건수: {summary.totalQuantity}건 / 총 매출 {summary.totalRevenue}원
- 채널별 매출
  - {channels[].channel}: {revenue}원 ({quantity}건, {pct}%)
- 가장 많이 팔린 상품
  - 1위: {topProducts[0].product} ({quantity}건 / {revenue}원)
  - 2위: ...
  - 3위: ...

{pendingOrders.length > 0 이면}
⚠️ 미처리 주문 {N}건 — 발주 확인이 필요합니다.
  - {channel} {orderId}: {product} ({amount}원)

자세한 내용은 대시보드에서 확인할 수 있어요.
대시보드를 보여드릴까요?

`sppt dashboard`

다음은 매출 분석을 해드릴게요.
무엇부터 분석해 드릴까요?

1. 지난 주 대비 매출이익률에 영향을 준 원인 분석
2. 지난 주 대비 채널별 매출 분포 변화 원인 분석
3. 상품별 판매 트렌드 심층 분석
```

### 3. 후속 분석 (선택)

사용자가 1~3 중 선택하면 해당 분석을 수행합니다:

1. **매출이익률 분석**: `sppt settlement summary --days 7` + `sppt settlement summary --days 14` 비교
2. **채널별 분포 분석**: `sppt report --by channel --period 7d` vs `sppt report --by channel --period 2w` 비교
3. **상품 트렌드 분석**: `sppt report --by product --period 7d` 전주 대비 증감 확인

## Error handling

- **채널 연결 실패**: CLI가 자동으로 건너뛰고 나머지 채널 결과 반환
- **주문 0건**: "이번 주 주문이 없습니다" 표시
