---
name: sppt-inventory-sync
description: |
  공급사 재고 기준 멀티채널 재고 분배 + 동기화 워크플로우.
  Use when asked to "재고 동기화", "채널별 재고 맞춰줘", "재고 분배".
allowed-tools:
  - Bash
  - Read
  - Edit
  - Glob
  - Grep
---

# inventory-sync

공급사 재고를 기준으로 4개 판매 채널의 재고를 분배하고 동기화하는 워크플로우.

## When to use

- 사용자가 "재고 동기화해줘", "채널별 재고 맞춰줘", "재고 분배해줘" 같은 요청을 할 때
- 공급사 재고 변동 시 멀티채널 재고 업데이트가 필요할 때

## Prerequisites

- 최소 2개 이상 판매 채널 설정
- `sppt status`로 채널 연결 상태 확인

## Steps

### 1. 재고 분배 계획 수립

```bash
sppt inventory plan <totalStock> [sku]
```

예시:
```bash
sppt inventory plan 500 SKU-001
```

출력 예시:
```
재고 분배 계획 (SKU: SKU-001, 총 재고: 500)
────────────────────────────────
버퍼 재고: 50 (10%)
배분 가능: 450
────────────────────────────────
cafe24:      135 (30%)
coupang:     180 (40%)
smartstore:   90 (20%)
shopify:      45 (10%)
```

사용자에게 분배 비율이 적절한지 확인합니다.

### 2. 채널별 재고 업데이트

각 채널에 순차적으로 재고를 동기화합니다:

```bash
sppt inventory sync cafe24 <productId> 135
sppt inventory sync coupang <productId> 180
sppt inventory sync smartstore <productId> 90
sppt inventory sync shopify <productId> 45
```

채널별 재고 업데이트 방식 차이:
- **Shopify**: delta 방식 (동시성 안전, 현재값 대비 증감)
- **Cafe24, Coupang**: 절대값 방식 (직접 수량 설정)
- **SmartStore**: 상품 전체 PUT 방식 (가장 비효율적)

### 3. 동기화 확인

```bash
sppt product get <channel> <productId>
```

각 채널에서 재고가 올바르게 반영되었는지 확인합니다.

## Error handling

- **재고 업데이트 실패**: Rate limit일 수 있음, 잠시 후 재시도
- **SmartStore 실패**: 상품 전체 PUT이므로 다른 필드가 변경될 수 있음 — 업데이트 전후 상품 정보 비교
- **Shopify delta 계산 오류**: 현재 재고를 먼저 조회한 후 차이값으로 업데이트

## Notes

- 버퍼 재고(기본 10%)는 과잉 판매 방지용입니다
- 분배 비율은 채널별 판매 실적에 따라 조정하세요
- 재고가 0이 되면 모든 채널에서 품절 처리를 권장합니다
- Shopify의 delta 방식이 가장 안전하므로, 동시 접근이 많은 상품은 Shopify를 기준으로 하세요
