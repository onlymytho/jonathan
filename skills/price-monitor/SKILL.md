---
name: sppt-price-monitor
description: |
  소싱가 변동 감지 + 마진 체크 + 판매가 조정 제안 워크플로우.
  Use when asked to "가격 변동 체크", "마진 확인", "소싱가 올랐는지".
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# price-monitor

소싱 채널의 도매가 변동을 감지하고 판매가 조정을 제안하는 워크플로우.

## When to use

- 사용자가 "가격 변동 체크해줘", "마진 확인해줘", "소싱가 올랐는지 봐줘" 같은 요청을 할 때
- 정기적으로 소싱가와 판매가의 마진을 점검할 때

## Prerequisites

- 최소 1개 판매 채널 + 1개 소싱 채널 설정
- 등록된 상품의 소싱 채널 상품 ID를 알고 있어야 함

## Steps

### 1. 소싱 상품 현재 가격 조회

```bash
sppt source detail <sourceChannel> <sourceProductId>
```

도매가(wholesalePrice)를 확인합니다.

### 2. 판매 상품 현재 가격 조회

```bash
sppt product get <sellingChannel> <sellingProductId>
```

현재 판매가를 확인합니다.

### 3. 마진 계산

```
현재 마진 = (판매가 - 도매가 - 배송비) / 판매가 × 100
```

마진 기준:
| 마진율 | 상태 | 조치 |
|---|---|---|
| 30% 이상 | 양호 | 유지 |
| 15~30% | 주의 | 모니터링 강화 |
| 15% 미만 | 위험 | 판매가 인상 권고 |
| 0% 미만 | 적자 | 즉시 판매가 조정 또는 판매 중지 |

### 4. 가격 조정 (필요시)

마진이 기준 이하일 경우 사용자에게 판매가 조정을 제안합니다.

제안 판매가 계산:
```
제안가 = 현재 도매가 × 1.3 + 배송비
```

사용자 확인 후, 새 가격을 담은 JSON 파일을 작성하고 업데이트합니다:

```bash
# /tmp/price-update.json
# { "variants": [{ "id": "...", "sku": "...", "optionLabel": "기본", "price": <원가>, "salePrice": <제안가>, "stockQuantity": <현재재고> }] }

sppt product update <sellingChannel> <productId> --file /tmp/price-update.json
```

또는 가격 룰을 직접 적용하려면 `--markup` 플래그를 활용합니다:

```bash
sppt product register <sellingChannel> --file /tmp/product.json --markup 1.3 --margin-floor 15
```

### 5. 복수 상품 모니터링

여러 상품을 점검할 때는 Step 1~4를 반복합니다.
결과를 표로 정리합니다:

```
가격 모니터링 결과
─────────────────────────────────────────────
상품명        소싱가    판매가    마진    상태
아이폰 케이스  8,500원  13,900원  27%     주의
USB 허브      12,000원  19,900원  35%     양호
블루투스 이어폰 15,200원 18,900원  12%    위험 ⚠
─────────────────────────────────────────────
```

## Error handling

- **소싱 상품 조회 실패**: 상품이 품절되었거나 삭제되었을 수 있음 — 대체 소싱처 검색 권장
- **판매 상품 조회 실패**: 채널 연결 상태 확인

## Notes

- 환율 변동 (CJ Dropshipping 이용 시)도 마진에 영향을 줍니다
- 배송비는 채널/지역에 따라 다르므로 사용자에게 확인합니다
- PostgreSQL이 연결되어 있으면 가격 이력이 자동 기록됩니다
- 주기적 모니터링은 주 1회 이상 권장합니다
