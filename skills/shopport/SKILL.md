---
name: shopport
version: 1.0.0
description: |
  shopport CLI 도움말 + 사용 가능한 스킬 목록. /sppt와 동일한 역할.
  Use when asked to "shopport", "shopport 도움말", "shopport 뭐야".
triggers:
  - shopport
  - shopport help
  - shopport 도움말
allowed-tools:
  - Bash
  - Read
  - Glob
---

# shopport

> `/sppt` 스킬과 동일합니다. shopport CLI 도움말 + 스킬 목록을 보려면 아래를 실행하세요.

먼저 CLI 도움말을 확인합니다:

```bash
sppt --help
```

## 사용 가능한 스킬

| 스킬 | 설명 |
|---|---|
| `/sppt` | shopport 전체 도움말 + 스킬 목록 |
| `/sppt-daily-report` | 매일 아침 매출/주문 브리핑 |
| `/sppt-order-collect` | 멀티채널 주문 한번에 모아보기 |
| `/sppt-order-fulfill` | 주문 확인 → 소싱 발주 → 송장 등록 |
| `/sppt-source-to-register` | 소싱 검색 → 마진 계산 → 판매 채널 등록 |
| `/sppt-waterfall-source` | 3개 소싱채널 워터폴 가격 비교 |
| `/sppt-product-sync` | 소싱→판매 배치 상품 동기화 |
| `/sppt-product-parse` | 상품명 → SKU/카테고리/제품라인 해체 |
| `/sppt-inventory-sync` | 멀티채널 재고 분배 + 동기화 |
| `/sppt-price-monitor` | 소싱가 변동 감지 → 마진 체크 → 판매가 조정 |
| `/sppt-weekly-analysis` | 전주 대비 매출 변화 원인 분석 + 채널 수수료 최적화 |
| `/sppt-settlement-report` | 정산 현황 조회 + 수익 요약 |
| `/sppt-cost-calc` | 원가/마진/수수료 계산 |
| `/sppt-trend-research` | 트렌드 키워드 리서치 |
| `/sppt-promo-research` | 경쟁 광고 분석 → 바이럴 체크 → 카피 생성 |
| `/sppt-dashboard` | 운영 대시보드 띄우기 |
| `/sppt-status-check` | 전채널 연결 + 건강 체크 |

## Quick Start

```bash
sppt init              # 채널 온보딩
sppt status            # 연결 상태 확인
sppt context           # AI용 스토어 브리핑
sppt order list cafe24 # 주문 조회
```

판매 채널: cafe24, coupang, smartstore, shopify
소싱 채널: domemae, domeggook, specialoffer, cjdropshipping
