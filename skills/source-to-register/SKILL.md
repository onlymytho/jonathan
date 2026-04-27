---
name: sppt-source-to-register
description: |
  소싱 채널에서 상품 검색 → 마진 계산 → 판매 채널 등록 워크플로우.
  Use when asked to "소싱해서 등록해줘", "도매매에서 찾아서 올려줘", "상품 소싱".
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# source-to-register

소싱 채널에서 상품을 검색하고 판매 채널에 등록하는 워크플로우.

## When to use

- 사용자가 "이 상품 소싱해서 등록해줘", "도매매에서 찾아서 카페24에 올려줘" 같은 요청을 할 때
- 소싱 → 등록 전체 플로우가 필요할 때

## Prerequisites

- 최소 1개 소싱 채널 설정: `sppt config get domemae`
- 최소 1개 판매 채널 설정: `sppt config get cafe24`

## Steps

### 1. 소싱 채널에서 상품 검색

```bash
sppt source search <sourceChannel> "<keyword>"
```

결과에서 상품 목록이 표시됩니다. 사용자에게 어떤 상품을 선택할지 확인합니다.

여러 소싱 채널을 비교하려면 `waterfall-source` 스킬을 먼저 실행하세요.

### 2. 상품 상세 조회

```bash
sppt source detail <sourceChannel> <productId>
```

도매가(wholesalePrice), 최소주문수량(minOrderQty), 옵션, 이미지 URL을 확인합니다.

### 3. 마진 계산 + 상품 데이터 변환

판매가 계산 공식:
```
sellingPrice = wholesalePrice × 마진배수 + 배송비
```

기본값:
- 마진배수: 1.3 (30% 마진)
- 배송비: 3,000원 (국내) / 소싱채널에 따라 상이

소싱 데이터를 판매 채널 등록용 JSON으로 변환합니다:

```json
{
  "name": "[상품명 — 사용자 확인 후 수정 가능]",
  "description": "[소싱 상세에서 가져온 설명]",
  "price": "[계산된 판매가]",
  "variants": [...],
  "images": ["[소싱 이미지 URL]"],
  "category": "[사용자에게 카테고리 확인]"
}
```

JSON 파일을 `/tmp/shopport-register-<timestamp>.json`에 저장합니다.

### 4. 판매 채널에 등록

```bash
sppt product register <sellingChannel> --file /tmp/shopport-register-<timestamp>.json
```

등록 결과에서 채널 상품 ID를 확인합니다.

### 5. 등록 확인

```bash
sppt product get <sellingChannel> <productId>
```

## Error handling

- **소싱 검색 결과 없음**: 다른 소싱 채널로 재시도하거나 키워드를 변경
- **Rate limit 초과**: 잠시 대기 후 재시도 (rate limiter가 자동 처리)
- **등록 실패**: 에러 메시지 확인 후 JSON 데이터 수정하여 재시도

## Notes

- Coupang은 상품 등록 시 카테고리 코드가 필수입니다
- SmartStore는 상품 등록 후 검수 과정이 있어 즉시 노출되지 않을 수 있습니다
- 이미지 URL은 소싱 채널의 원본 URL을 사용합니다 (채널에 따라 외부 이미지 차단 가능)
