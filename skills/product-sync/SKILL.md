---
name: sppt-product-sync
description: |
  소싱 상품을 판매 채널에 배치로 동기화/등록하는 워크플로우.
  Use when asked to "상품 동기화", "배치 등록", "전채널 등록", "상품 복사".
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Glob
  - Grep
---

# product-sync

소싱 상품을 여러 판매 채널에 배치로 등록/동기화하는 워크플로우.

## When to use

- "상품 전채널에 올려줘", "배치로 등록해줘", "카페24 상품 쿠팡에도 올려줘"
- 한 채널에 등록된 상품을 다른 채널로 복사할 때
- 다수 상품을 한번에 등록할 때

## Prerequisites

- 최소 2개 판매 채널 설정 완료
- 등록할 상품 데이터 (JSON 파일 또는 기존 채널 상품 ID)

## Steps

### 1. 원본 상품 확인

기존 채널에서 상품 목록을 확인합니다:

```bash
sppt product list cafe24
```

또는 특정 상품 상세:

```bash
sppt product get cafe24 <productId>
```

### 2. 배치 동기화 실행

한 채널의 상품을 다른 채널로 배치 복사:

```bash
sppt product sync-batch --from cafe24 --to coupang --ids <id1,id2,id3>
```

전체 채널 동시 등록:

```bash
sppt product register --all-channels --file /tmp/product.json
```

### 3. 결과 확인

배치 결과가 CSV로 저장됩니다. 실패한 항목을 확인합니다:

```bash
# 결과 CSV에서 실패 건 확인
```

실패한 상품은 에러 메시지를 확인하고 개별 재시도합니다:

```bash
sppt product register coupang --file /tmp/failed-product.json
```

### 4. 매핑 등록

채널 간 상품 매핑을 등록하여 재고 동기화에 활용합니다:

```bash
sppt mapping add <cafe24Id> <coupangId>
```

## Error handling

- **카테고리 코드 불일치**: 채널별 카테고리 체계가 달라 매핑 필요
- **이미지 URL 차단**: 일부 채널은 외부 이미지 URL을 차단 — 이미지 업로드 필요
- **필수 필드 누락**: 채널별 필수 입력 항목이 다름 (쿠팡: 카테고리 코드 필수)

## Notes

- `--all-channels`는 설정된 모든 판매 채널에 동시 등록합니다
- 배치 복사 결과는 자동으로 CSV에 기록됩니다
- 채널 간 가격 차등은 `--markup` 옵션으로 조정 가능합니다
