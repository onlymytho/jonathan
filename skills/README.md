# shopport Skills

> Claude Code가 읽고 실행하는 커머스 운영 워크플로우 스킬

## 설치

### 자동 설치 (npm 설치 시 자동 등록)

```bash
npm install -g shopport
```

설치 완료 후 `~/.claude/skills/`에 스킬이 자동 등록됩니다.

### 수동 등록

```bash
sppt setup-skills           # 스킬 등록
sppt setup-skills uninstall # 스킬 제거
```

## 스킬 목록

### 도움말

| 스킬 | 설명 |
|---|---|
| `/sppt` | shopport 전체 도움말 + 스킬 목록 |
| `/shopport` | `/sppt`와 동일 (별칭) |

### 주문

| 스킬 | 설명 |
|---|---|
| `/sppt-order-collect` | 멀티채널 주문 한번에 모아보기 |
| `/sppt-order-fulfill` | 주문 확인 → 소싱 발주 → 송장 등록 |

### 소싱

| 스킬 | 설명 |
|---|---|
| `/sppt-source-to-register` | 소싱 검색 → 마진 계산 → 판매 채널 등록 |
| `/sppt-waterfall-source` | 3개 소싱채널 워터폴 가격 비교 |

### 상품

| 스킬 | 설명 |
|---|---|
| `/sppt-product-sync` | 소싱→판매 배치 상품 동기화 |
| `/sppt-product-parse` | 상품명 → SKU/카테고리/제품라인 해체 |

### 재고

| 스킬 | 설명 |
|---|---|
| `/sppt-inventory-sync` | 멀티채널 재고 분배 + 동기화 |

### 가격/정산

| 스킬 | 설명 |
|---|---|
| `/sppt-price-monitor` | 소싱가 변동 감지 → 마진 체크 → 판매가 조정 |
| `/sppt-settlement-report` | 정산 현황 조회 + 수익 요약 |
| `/sppt-cost-calc` | 원가/마진/수수료 계산 |

### 리서치

| 스킬 | 설명 |
|---|---|
| `/sppt-trend-research` | 트렌드 키워드 리서치 |

### 운영

| 스킬 | 설명 |
|---|---|
| `/sppt-dashboard` | 운영 대시보드 띄우기 |
| `/sppt-status-check` | 전채널 연결 + 건강 체크 |

## 전제 조건

- `sppt` CLI 설치 (`npm install -g shopport`)
- 최소 1개 판매 채널 + 1개 소싱 채널 설정 (`sppt init`)

## 스킬 구조

각 스킬은 `SKILL.md` 마크다운 파일 하나로 구성됩니다.
YAML frontmatter에 `name`, `description`, `triggers`, `allowed-tools`가 정의되어 있으며,
Claude Code가 `~/.claude/skills/`에서 자동으로 발견하여 슬래시 커맨드로 사용할 수 있습니다.

```
~/.claude/skills/
├── sppt/SKILL.md              → /sppt
├── shopport/SKILL.md          → /shopport
├── sppt-order-collect/SKILL.md → /sppt-order-collect
├── sppt-order-fulfill/SKILL.md → /sppt-order-fulfill
└── ...
```

## 지원 채널

- **판매**: cafe24, coupang, smartstore, shopify
- **소싱**: domemae (위탁), domeggook (사입), specialoffer, cjdropshipping
