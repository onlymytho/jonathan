---
name: sppt-product-parse
description: |
  주문 상품명을 SKU/카테고리/제품라인으로 해체하는 파싱 워크플로우.
  Use when asked to "상품명 분석", "SKU별 정리", "미매핑 상품 확인".
---

# product-parse

주문 데이터의 상품명을 사용자 정의 규칙에 따라 SKU/카테고리/제품라인 단위로 해체.

## When to use

- "상품명 분석해줘", "SKU별로 정리해줘", "제품라인별 매출 보고 싶어"
- 주문 데이터 임포트 후 상품 카탈로그를 구축하고 싶을 때
- "미매핑 상품 있는지 확인해줘"

## Prerequisites

- 주문 데이터가 임포트되어 있어야 함 (`sppt import <file>` 또는 API 동기화)
- 상품 분류 규칙 파일이 있으면 더 정확함 (없으면 LLM이 자체 판단)

## Steps

### 1. 상태 확인

먼저 현재 상태를 파악합니다.

```bash
sppt product unmapped
```

미매핑 상품명이 몇 건인지 확인합니다. 이미 전부 매핑되어 있으면 완료.

### 2. 분류 규칙 확인

```bash
sppt product rules
```

클라우드(대시보드 DB)에서 현재 규칙을 읽어옵니다.

- **규칙이 있으면**: 내용을 읽고 규칙을 이해합니다.
- **규칙이 없으면**: 사용자에게 물어봅니다:
  - "상품 분류 규칙이 있나요? (카테고리, 제품라인 등)"
  - 사용자가 알려주면 아래 방법으로 저장합니다.
  - 없으면 상품명에서 자체 판단합니다.

#### 규칙 저장/수정 방법 (AI용)

규칙을 작성하거나 수정할 때는 다음 순서로 진행합니다:

1. 현재 규칙 내용을 가져와 임시 파일로 저장:

```bash
sppt product rules  # 내용 확인
```

2. Write/Edit 툴로 `/tmp/product-rules.md`에 수정 내용 작성

3. 클라우드에 반영:

```bash
sppt product rules set /tmp/product-rules.md
```

오프라인 환경에서 로컬 파일만 수정할 때는 `--local` 플래그를 사용합니다.

### 3. 상품명 해체

`sppt product unmapped`의 결과 목록을 가져온 후, 분류 규칙을 참고하여 각 상품명을 해체합니다.

#### 해체 결과 구조

각 상품명에서 다음을 추출합니다:

| 필드 | 설명 | 예시 |
|------|------|------|
| sku | 정규 SKU 코드 | AB-MC20 |
| brand | 브랜드명 | 알텐바흐 |
| productLine | 제품 라인 | 316Ti |
| category | 카테고리 | 냄비 |
| productName | 정규 상품명 | 멀티쿠커 편수 |
| option | 옵션/사이즈 | 20cm |

#### 해체 원칙

1. **규칙 우선**: `product-rules.md`에 명시된 분류 규칙을 최우선으로 적용
2. **MECE**: 모든 상품이 빠짐없이 하나의 카테고리에 정확히 배정되어야 함
3. **모호한 경우 질문**: 규칙으로 판단 불가능한 경우, 자의적 분류 대신 사용자에게 질문
4. **세트 상품 분해**: 세트/번들 상품은 구성 단품 SKU로 분해 (규칙에 명시된 경우)

#### SKU 코드 생성 규칙

사용자가 별도 SKU 체계를 가지고 있으면 그것을 따릅니다.
없으면 다음 패턴으로 자동 생성:

```
{브랜드 약어}-{제품라인 약어}-{옵션코드}
예: AB-DF-FP28 (알텐바흐-디피니티-프라이팬28cm)
```

### 4. 결과 저장

해체 결과를 카탈로그에 저장합니다.

엔트리 배열을 `/tmp/product-catalog-entries.json`에 저장한 뒤 CLI로 임포트합니다:

```bash
sppt product catalog import /tmp/product-catalog-entries.json
```

파일 형식 (JSON 배열):

```json
[
  {
    "sku": "AB-MC20",
    "brand": "알텐바흐",
    "productLine": "316Ti",
    "category": "냄비",
    "productName": "멀티쿠커 편수",
    "option": "20cm",
    "rawNames": ["[알텐바흐] 316Ti 올인원 멀티쿠커 편수 20cm"],
    "confidence": "llm"
  }
]
```

### 5. 검증

```bash
sppt product catalog
```

저장된 카탈로그를 조회하여 결과를 확인합니다.

다시 `sppt product unmapped`를 실행하여 미매핑 상품이 0건인지 확인합니다.

### 6. 분석 활용

카탈로그가 구축되면 대시보드에서 다음 분석이 가능합니다:

- SKU별 판매량/매출 집계
- 제품라인별 비교 (어떤 라인이 잘 팔리나)
- 카테고리별 매출 비중
- 옵션/사이즈별 선호도

## Tips

- 규칙 파일은 자연어로 자유롭게 작성하세요. 이 스킬이 읽고 해석합니다.
- 한번 매핑된 상품명은 캐시되어 다음에는 자동 적용됩니다.
- 새로운 상품이 추가되면 `sppt product unmapped`로 확인 후 추가 해체합니다.
- 잘못된 매핑은 다시 해체하거나 `sppt product catalog edit <sku>`로 수정할 수 있습니다.
