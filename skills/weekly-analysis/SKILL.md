---
name: sppt-weekly-analysis
version: 0.3.0
description: |
  전주 대비 매출 변화 원인 분석 + 채널 수수료 최적화 컨설팅 워크플로우.
  Use when asked to "주간 분석", "왜 매출이 줄었어", "채널별 원인 분석", "지난 주 비교",
  "매출이익률 분석", "수수료율 변화", "원인 분석해줘".
triggers:
  - 주간 분석
  - 원인 분석
  - 왜 매출이
  - 전주 대비
  - 채널 분포 변화
  - 수수료율 변화
  - 매출이익률 분석
  - weekly analysis
allowed-tools:
  - Bash
  - Read
---

# weekly-analysis

## 채널 분류 컨텍스트

분석 시작 전 채널 메타정보를 조회해 특성을 파악한다:

```bash
sppt channel meta --json
```

각 채널에는 `type`(own-mall/open-market/vertical-specialist), `entryRequirement`(open/contract), `hasChannelBrandValue`가 정의되어 있다.

**수수료 최적화 시 주의:**
- `entryRequirement: "contract"` 채널은 계약 관계이므로 단순 수수료 비교로 비중 축소를 권고하면 안 된다.
- `hasChannelBrandValue: true` 채널은 높은 수수료에 마케팅·브랜딩·노출 가치가 포함된다.
- 채널 간 이동보다 **같은 채널 내 최적화**(유입경로, 단가, 프로모션)가 실행 가능성이 높다.

## Data Collection

```bash
sppt channel meta --json               # 채널 특성 (type/entryRequirement/hasChannelBrandValue)
sppt channel fees --json               # 채널별 수수료 전체 (유입경로별 포함)
# sppt channel fees --compare <rate>  # 특정 수수료율 이하·근방 경로 비교 (필요시)
sppt report compare --by channel --period 7d --json
sppt report compare --by product --period 7d --top 10 --json
sppt settlement summary --json        # 기본값 30일
sppt settlement fee list smartstore   # 유입경로별 수수료 규칙 확인
sppt cost list --json                  # 없으면 Step 4 skip
```

## Analysis Steps

### Step 1: 채널 수수료율 분석

1. **채널별 수수료율 랭킹** — 채널 분류(자사몰/오픈마켓/버티컬)와 함께 표시. 표본 < 5건은 신뢰도 낮음 표기.
2. **실효수수료율** = Σ(채널_비중 × 채널_수수료율), 오픈마켓 채널 간 비중과 버티컬 채널 비중을 분리해서 보여줌.
3. **스마트스토어 유입경로 분석** — `fee list smartstore` 결과로 마케팅링크 유입 비중이 실효율에 미치는 영향 추정. 규칙 미등록 시 annualRevenue/브랜드스토어 여부에 따른 오차 가능성 언급.
4. **오픈마켓 내 믹스 최적화** — smartstore ↔ cafe24 비중 이동 시 절감 효과. 버티컬 채널은 별도 분석.
5. **집중 리스크** — 단일 채널 비중 > 70% 경고. 단, 버티컬 채널의 비중 감소는 리스크가 아닌 채널 특성으로 해석.
6. **전주 대비 변화** (데이터 있을 때만) — 채널믹스 효과 vs 채널내 수수료율 변화로 분해.

### Step 2: 채널별 매출 변화 분해

`report compare --by channel` diff 배열에서 직접 읽는다.

- 채널별 매출 증감, volumeEffect / priceEffect 요약
- 채널 점유율 변화 (이전주 비중 → 이번주 비중)

### Step 3: Top Mover 상품 귀속

`report compare --by product` diff 배열 상위 항목을 채널 변화에 귀속시킨다.

- 매출 변화 상위 5개 상품: revenueChange, volumeDiff%, priceDiff%
- 특이값 (신규 등장 또는 완전 소멸 상품) 별도 언급

### Step 4: 공헌이익률 분석 (원가 있을 때만)

- 이번주 vs 지난주 공헌이익률 변화
- 상품 믹스 효과 vs 단가 변화 효과로 분해

## AI Interpretation

위 데이터를 바탕으로 각 변화에 대해:
1. 가능한 원인 (내부/외부 구분)
2. 우선순위 조치 (즉각 / 단기 / 검토)
3. 수수료 최적화: 채널 특성과 진입 가능성을 고려한 실행 가능한 방안만 제안. "버티컬 채널 축소" 같은 계약 외 조치는 제안하지 않는다.

## Error Handling

- **이번주 매출이 이전주 대비 -80% 이상**: 데이터 수집 공백 가능성 → `sppt order pull` 재실행 안내 후 분석
- **원가 미등록**: Step 4 skip
- **채널 1개만**: 수수료 비교 skip, 단가/볼륨 분석 집중
- **settlement 표본 부족** (채널별 < 5건): 수수료율 신뢰도 낮음 표기, `--days 90` 권장
- **smartstore fee rule 미등록**: 실효율 신뢰도 낮음 표기. `sppt settlement hints smartstore`로 annualRevenue 설정 권장.
