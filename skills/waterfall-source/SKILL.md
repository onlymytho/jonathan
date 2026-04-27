---
name: sppt-waterfall-source
description: |
  3개 소싱 채널(도매매→specialoffer→cjdropshipping) 워터폴 가격 비교.
  Use when asked to "가장 싼 공급사 찾아줘", "소싱 비교", "최적 소싱처".
allowed-tools:
  - Bash
  - Read
  - Glob
  - Grep
---

# waterfall-source

3개 소싱 채널을 비용 낮은 순서대로 검색하는 워터폴 소싱 워크플로우.

## When to use

- 사용자가 "가장 싼 공급사 찾아줘", "최적 소싱처 검색", "소싱 비교해줘" 같은 요청을 할 때
- 여러 소싱 채널에서 동일 상품의 가격/조건을 비교할 때

## Prerequisites

- 최소 2개 이상 소싱 채널 설정
- `sppt config list`로 소싱 채널 확인

## Steps

### 1. 워터폴 검색 실행

`--waterfall` 플래그로 3채널을 자동으로 순서대로 검색합니다.

**비용 절약 모드 (기본)** — 첫 유효 결과에서 중단:

```bash
sppt source search domeggook "<keyword>" --waterfall --strategy first
```

**최적 품질 모드** — 3채널 전부 검색 후 스코어 비교:

```bash
sppt source search domeggook "<keyword>" --waterfall --strategy best
```

워터폴 순서: domeggook(도매매) → specialoffer → cjdropshipping (비용 오름차순)

결과에는 각 상품의 마진율과 종합 스코어(마진 40% + 재고유연성 30% + 공급사신뢰도 30%)가 표시됩니다.

### 2. 상세 조회

워터폴 결과에서 선택한 상품의 상세 정보를 확인합니다:

```bash
sppt source detail <channel> <productId>
```

### 3. 결과 요약

사용자에게 채널별 비교 결과를 표로 정리합니다:

```
워터폴 소싱 결과: "키워드"
검색 채널: 도매매, specialoffer, cjdropshipping  캐시 히트: 0  API 호출: 2  결과: 8건

채널           상품명         도매가     마진    스코어
도매매         상품A          8,500원    32%     78/100
specialoffer   상품B          9,200원    28%     71/100
cjdropshipping 상품C          7,150원    35%     65/100
─────────────────────────────────────
추천: 도매매 — 상품A (스코어 1위)
```

## Error handling

- **전체 채널 검색 실패**: 키워드를 변경하거나 영문으로 검색 시도
- **도매매 일일 한도 초과**: CLI가 자동으로 다음 채널로 진행
- **Rate limit**: CLI의 rate limiter가 자동 처리하므로 대기

## Notes

- `--strategy first` (기본): 첫 유효 결과에서 중단 — API 호출 최소화
- `--strategy best`: 전체 검색 후 스코어 기반 최적 선택
- 검색 결과는 2시간 캐시됨 — 같은 키워드 재검색 시 캐시 반환
- CJ Dropshipping USD 가격은 기본 환율(1,380원) 또는 `--usd-krw N`으로 지정
- 공급사 신뢰도 스코어는 `order score record`로 쌓인 실데이터 기반
