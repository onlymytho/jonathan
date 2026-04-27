---
name: sppt
description: |
  shopport CLI 도움말 + 사용 가능한 스킬 목록.
  Use when asked to "sppt", "shopport 뭐 할 수 있어?", "스킬 목록", "커맨드 알려줘".
allowed-tools:
  - Bash
  - Read
  - Glob
---

# shopport (sppt)

> AI를 위한 이커머스 운영도구 — 사장님이 뽑으려던 운영 담당자, shopport가 대신합니다.

## Quick Start

```bash
sppt --help          # CLI 전체 도움말
sppt status          # 채널 연결 상태 확인
sppt context         # AI용 스토어 브리핑
```

## 사용 가능한 스킬

| 스킬 | 설명 |
|---|---|
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
| `/sppt-dashboard` | 운영 대시보드 띄우기 |
| `/sppt-status-check` | 전채널 연결 + 건강 체크 |

## CLI 커맨드 요약

### 설정
- `sppt init [channel]` — 채널 온보딩
- `sppt auth <channel>` — OAuth 인증
- `sppt config list|get|set|remove` — 설정 관리

### 상품
- `sppt product list|get|register|update|delete <channel>` — 상품 CRUD
- `sppt product keyword|seo|image <channel>` — SEO/이미지 관리
- `sppt product sync-batch` — 배치 복사

### 주문/송장
- `sppt order list|get|confirm|cancel|return <channel>` — 주문 관리
- `sppt invoice register <channel> <orderId> <carrier> <tracking>` — 송장

### 소싱
- `sppt source search|detail|categories|route|score` — 소싱 채널

### 정산
- `sppt settlement calc|list|summary` — 정산 조회
- `sppt settlement fee list|set|remove` — 수수료 규칙

### 가격/쿠폰
- `sppt price set|variant` — 가격 변경
- `sppt coupon create|issue` — 쿠폰

### 분석
- `sppt trend keyword|compare|platform` — 트렌드
- `sppt report revenue|--by <dimension>` — 매출 리포트
- `sppt dashboard` — 웹 대시보드

### 데이터
- `sppt context` — AI용 스토어 브리핑
- `sppt status` — 헬스체크
- `sppt log` — 작업 이력
- `sppt import <file>` — CSV/엑셀 임포트

## 판매 채널
cafe24, coupang, smartstore, shopify

## 소싱 채널
domemae (위탁), domeggook (사입), specialoffer, cjdropshipping
