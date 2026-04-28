<div align="center">

```
 ███████╗██╗  ██╗ ██████╗ ██████╗ ██████╗  ██████╗ ██████╗ ████████╗
 ██╔════╝██║  ██║██╔═══██╗██╔══██╗██╔══██╗██╔═══██╗██╔══██╗╚══██╔══╝
 ███████╗███████║██║   ██║██████╔╝██████╔╝██║   ██║██████╔╝   ██║   
 ╚════██║██╔══██║██║   ██║██╔═══╝ ██╔═══╝ ██║   ██║██╔══██╗   ██║   
 ███████║██║  ██║╚██████╔╝██║     ██║     ╚██████╔╝██║  ██║   ██║   
 ╚══════╝╚═╝  ╚═╝ ╚═════╝ ╚═╝     ╚═╝      ╚═════╝ ╚═╝  ╚═╝   ╚═╝   
```

### AI를 위한 이커머스 운영도구

**사장님이 뽑으려던 이커머스 운영 담당자, shopport가 대신합니다.**

쿠팡 · 스마트스토어 · 카페24 · Shopify

</div>

```sh
curl -fsSL https://shopport.vercel.app/api/v2/cli/install | bash; exec $SHELL
```

macOS · Linux 지원 · bun / npm 자동 감지

---

## 이런 분을 위해 만들었습니다

- AI를 이미 사용하고 있는데, 이커머스 운영은 아직도 대시보드 솔루션을 쓴다면
- "이번 달 OO카테고리 매출이 얼마야?" 라는 질문에 바로 답하기 어렵다면
- 쿠팡·스마트스토어·카페24 정산 내역을 각각 다운받아 엑셀로 합치고 있다면
- 신상품 등록할 때마다 채널마다 같은 내용을 반복 입력하고 있다면
- 주문 CSV를 취합하는 데 매일 30분을 쓰고 있다면

#### 이제 여러분이 사용하던 AI Agent가 shopport로 모든 걸 대신할 수 있습니다.

| # | 담당자 | 대체하는 작업 |
|---|--------|-------------|
| 1 | [주문 처리 담당자](#1-주문-처리-담당자) | 채널별 주문 CSV 취합 · API 직연동 조회 |
| 2 | [정산 담당자](#2-정산-담당자) | 수수료 계산 · 입금 스케줄 추적 |
| 3 | [매출 리포터](#3-매출-리포터) | 채널·SKU·카테고리별 판매 분석 |
| 4 | [손익 분석가](#4-손익-분석가) | 원가 입력 → 마진율·공헌이익 계산 |
| 5 | [상품 등록 담당자](#5-상품-등록-담당자) | 멀티채널 상품 등록 · 가격·재고 동기화 *(Phase 2)* |

---

## 3가지 사용 방식

### 1. AI 어시스턴트 — Claude / Claude Code / Codex 에서 자연어로

설치 후 AI 어시스턴트에게 자연어로 사용을 부탁합니다.

```
"OO카테고리 이번 달 얼마 팔렸어?"    # sppt report --by category --period this-month
"이번 주 정산 예정금액 얼마야?"       # sppt settlement schedule
"오늘 매출 얼마야?"                   # sppt report revenue
"마진율 낮은 SKU 뽑아줘"             # sppt report --by sku --metric margin
```

MCP 없이 Direct API 호출 — 빠르고 토큰 효율적.

```sh
# AI Agent용 설치
curl -fsSL https://shopport.vercel.app/agent-install.txt
```

### 2. CLI — 명령어 하나로 바로 실행

반복 작업을 터미널 명령어 하나로 끝냅니다.

```sh
sppt order list          # 전 채널 주문 통합 조회
sppt settlement summary  # 채널별 정산 합계
sppt product sync        # 가격·재고 전 채널 동기화
```

### 3. 풀 에이전트 — OpenClaw, Hermes에게 스토어를 통째로 위임

사람 개입 없이 소싱부터 발주·송장 등록까지 에이전트가 직접 운영합니다.
```
"shopport로 내 커머스 운영 매니저가 되어줘"
```

---

## 자동화 파이프라인

```
① 트렌드 탐색         무신사·올리브영 랭킹, 네이버 검색량·경쟁강도
        │
        ▼
② 판매채널 관리       상품 등록·수정·가격 변경·재고 동기화
                      (쿠팡·스마트스토어·카페24·Shopify 멀티채널 일괄)
        │
        ▼
③ 주문 처리           주문 수집 (API 직연동·CSV 임포트)
        │
        ▼
④ 정산·매출·손익      채널별 정산 집계·수수료 자동 파싱·입금 예정 스케줄
                      SKU·카테고리·제품라인별 매출 리포트
                      원가 입력 → 채널·SKU별 마진율·공헌이익 자동 계산
```

수동 운영 3시간 → shopport 10분.

---

## 설치

```sh
# 가장 간편 — OS / 패키지 매니저 자동 감지
curl -fsSL https://shopport.vercel.app/api/v2/cli/install | bash; exec $SHELL

# npm
npm install -g shopport

# bun
bun add -g shopport
```

설정 파일은 `~/.shopport/config.json`에 로컬 저장됩니다.

---

## 빠른 시작

```sh
# 1. 채널 인증 설정 (5분)
sppt init

# 2. AI 스킬 설치 (Claude Code / Codex / OpenCode)
sppt setup-skills

# 3. 전 채널 주문 조회
sppt order list

# 4. 전체 대시보드
sppt dashboard

# 5. AI용 스토어 브리핑 (Claude Code 등에서 context 파악용)
sppt context
```

---

# 어떤 담당자를 대신하나요

| # | 담당자 | 대체하는 작업 |
|---|--------|-------------|
| 1 | [주문 처리 담당자](#1-주문-처리-담당자) | 채널별 주문 CSV 취합 · API 직연동 조회 |
| 2 | [정산 담당자](#2-정산-담당자) | 수수료 계산 · 입금 스케줄 추적 |
| 3 | [매출 리포터](#3-매출-리포터) | 채널·SKU·카테고리별 판매 분석 |
| 4 | [손익 분석가](#4-손익-분석가) | 원가 입력 → 마진율·공헌이익 계산 |
| 5 | [상품 등록 담당자](#5-상품-등록-담당자) | 멀티채널 상품 등록 · 가격·재고 동기화 *(Phase 2)* |

---

## 1. 주문 처리 담당자

**지금 하는 일**

채널별 주문 CSV 개별 다운로드 → 채널마다 다른 컬럼 양식 수동 정리. **매일 30분.**

**shopport로 바뀌는 것**

```sh
sppt order list <channel> [--days N]   # 전 채널 주문 통합 조회
sppt import <file>                     # CSV/XLS 자동 감지 → 채널 판별 후 임포트
```

| 채널 | API | CSV |
|------|:---:|:---:|
| 쿠팡 | ✅ | — |
| 스마트스토어 | ✅ | ✅ 주문 |
| 카페24 | ✅ | ✅ 주문·상품 |
| Shopify | ✅ | — |
| 마켓컬리 | — | ✅ 주문 |
| SSF (삼성패션) | — | ✅ 주문 |
| 브론테 | — | ✅ 주문 |
| EZ어드민 | — | ✅ 주문·재고 |

채널 API 실시간 직연동. 채널별 CSV는 다운받은 파일을 AI에게 던져 `sppt import`을 실행하면 채널을 자동 감지해 통합 처리합니다.

> *"쿠팡, 스마트스토어, 카페24 주문 취합하는 데 매일 30분 쓰고 계신가요? 명령 한 번으로 끝납니다."*

---

## 2. 정산 담당자

**지금 하는 일**

채널별 정산 내역 개별 다운로드 → 수수료 구조 파악 → 엑셀에서 직접 계산 → 입금 스케쥴 관리 **주 3~4시간.**

**shopport로 바뀌는 것**

```sh
sppt settlement summary [channel] [--days N]   # 채널별 정산 합계
sppt settlement list [channel]                 # 정산 목록
sppt settlement calc <channel> <orderId>       # 수수료·실수령액 계산
sppt settlement schedule                       # 입금 예정 스케줄
```

```
입금 예정 스케줄  2026-04-20 ~ 2026-07-19

  2026-04-20 (월)
    카페24          26,198,108원
    스마트스토어      36,413,929원
    합계            62,612,037원
  2026-05-03 (일)  컬리  2,160,100원
──────────────────────────────────────
  합계  64,772,137원 · 187건
```

쿠팡·스마트스토어·카페24 수수료를 자동 파싱해 실수령액을 계산합니다. 입금 예정일을 채널별로 미리 확인하고, 누락·미입금 정산은 자동으로 플래그.

> *"정산 엑셀 만지는 데 주 3~4시간 쓰고 계시면, 그거 shopport가 5분으로 줄여드립니다."*

---

## 3. 매출 리포터

**지금 하는 일**

채널별 매출 CSV 개별 다운로드 → 엑셀 피벗으로 일·주·월별 집계 → 채널/SKU/카테고리별 성과 비교 → 경영진 보고용 자료 재가공.

**shopport로 바뀌는 것**

```sh
sppt report revenue                                       # 오늘 전 채널 통합 매출
sppt report revenue --period this-month                   # 이번 달 전 채널 통합 매출
sppt report --by channel --period this-month            # 채널별 이번 달 매출
sppt report --by sku --period 7d --metric margin        # SKU별 마진 순위
sppt report --by category --period last-month --top 10  # 카테고리 TOP 10
sppt report --by productline --period 3m                # 제품라인별 3개월 추이
```

채널·SKU·카테고리·제품라인 단위로 기간을 지정해 판매량·매출·마진·공헌이익을 한 번에 뽑아냅니다.

상품 구조가 복잡할수록 강력합니다. 같은 상품이라도 채널마다 이름이 다르고, SKU가 얽혀 있는 경우 — `--by productline`으로 제품라인 단위로 묶어서 보거나, `--by sku`로 낱개 단위까지 쪼개 볼 수 있습니다. "이 아이템 실제로 얼마나 팔렸어?"에 즉시 답할 수 있게 됩니다.

> *"어느 채널에서 매출이 나오는지, 어느 상품이 잘 팔리는지 — 사장님이 직접 실시간으로 보실 수 있어요."*

---

## 4. 손익 분석가

**지금 하는 일**

원가·수수료·광고비·물류비 채널별 수동 입력 → 채널별 마진율 엑셀 수식 관리 → 수익성 낮은 채널·SKU 파악.

**shopport로 바뀌는 것**

```sh
sppt cost set <sku>    # SKU별 원가 등록
sppt cost list         # 채널·SKU별 공헌이익 현황
```

원가 데이터를 깔끔하게 정리하지 않아도 됩니다. 공급사에서 받은 엑셀 파일 그대로 AI에게 붙여넣으면, AI가 SKU를 매핑하고 원가를 파싱해 `cost set` 커맨드로 자동 업로드합니다.

채널 수수료는 정산 데이터에서 자동 연산. 원가만 들어오면 마진율 대시보드가 완성됩니다. 임계치 미달 상품은 자동 경고.

> *"어느 채널에서 실제로 남고, 어느 채널이 적자인지 숫자로 바로 보여드립니다."*

---

## 5. 상품 등록 담당자

**지금 하는 일**

신상품 쿠팡에 입력 → 스마트스토어에 또 입력 → 카페24에 또 입력 → 가격·재고 변경 시 채널마다 개별 수정.

**shopport로 바뀌는 것**

```sh
sppt product register --all-channels --file product.json   # 전 채널 일괄 등록
sppt product sync                                          # 가격·재고 동기화
sppt product seo set <channel> <id> --title <t>            # SEO 메타 관리
sppt product keyword set <channel> <id> --tags <kw>        # 검색 키워드 관리
```

상품 마스터 한 번 입력으로 채널별 양식을 자동 변환해 일괄 배포합니다.

채널마다 필수 입력 항목이 다르고, 브랜드명·용량·성분 같은 항목은 직접 찾아 입력하기 번거롭습니다. shopport는 AI가 상품 상세페이지 이미지를 직접 읽어 브랜드명·규격·고시정보 등 누락 항목을 자동으로 채워 넣습니다.

> *"한 번 입력하면 전 채널에 동시에 올라갑니다."*

---

## 지원 채널

### 판매 채널

| 채널 | 연동 | 주문 | 상품 | 가격 | 재고 | 정산 | 쿠폰 | 웹훅 |
|------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| <img src="https://www.google.com/s2/favicons?domain=coupang.com&sz=16" width="16"/> 쿠팡 | API | ⚠️ 폴링 | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ |
| <img src="https://www.google.com/s2/favicons?domain=shopping.naver.com&sz=16" width="16"/> 스마트스토어 | API | ⚠️ 폴링 | ✅ | ✅ | ⚠️ | — | ❌ | ❌ |
| <img src="https://www.google.com/s2/favicons?domain=cafe24.com&sz=16" width="16"/> 카페24 | API · CSV | ✅ 웹훅 | ✅ | ✅ | ⚠️ | ✅ | ✅ | ✅ |
| <img src="https://www.google.com/s2/favicons?domain=shopify.com&sz=16" width="16"/> Shopify | API | ✅ 웹훅 | ✅ | ✅ | ✅ | — | ✅ | ✅ |
| <img src="https://www.google.com/s2/favicons?domain=kurly.com&sz=16" width="16"/> 마켓컬리 | CSV | ✅ | — | — | — | — | — | — |
| <img src="https://www.google.com/s2/favicons?domain=ssfshop.com&sz=16" width="16"/> SSF (삼성패션) | CSV | ✅ | — | — | — | — | — | — |
| <img src="https://www.google.com/s2/favicons?domain=bronte.co.kr&sz=16" width="16"/> 브론테 | CSV | ✅ | — | — | — | — | — | — |
| EZ어드민 | CSV | ✅ | — | ✅ | — | — | — | — |

CSV 채널은 다운받은 파일을 `sppt import`에 던지면 자동 감지해 처리합니다.

### 트렌드 · 시장 데이터

| 채널 | 검색량 | 랭킹 | 광고 키워드 |
|------|:---:|:---:|:---:|
| <img src="https://www.google.com/s2/favicons?domain=datalab.naver.com&sz=16" width="16"/> 네이버 DataLab | ✅ | — | — |
| <img src="https://www.google.com/s2/favicons?domain=searchad.naver.com&sz=16" width="16"/> 네이버 SearchAD | ✅ | — | ✅ |
| <img src="https://www.google.com/s2/favicons?domain=musinsa.com&sz=16" width="16"/> 무신사 | — | ✅ | — |
| <img src="https://www.google.com/s2/favicons?domain=hwahae.com&sz=16" width="16"/> 화해 | — | ✅ | — |

---

## 전체 명령어

<details>
<summary>펼치기</summary>

```
── 설정
  init [channel]                채널 온보딩 설정
  auth <channel>                OAuth 인증 (브라우저)
  config list|get|set|remove    설정 관리

── 상태
  context [--json]              AI용 스토어 브리핑 (프로필·규칙·판매 인사이트)
  health [--suppliers|--check]  채널 연동 헬스체크
  cap [channel]                 채널별 API 기능 현황
  budget [set <channel> <N>]    API 사용량 / 한도

── 트렌드
  trend keyword <키워드>          검색량 + 트렌드 통합 조회
  trend compare <kw1> <kw2>      키워드 비교
  trend platform <platform>      전문몰 랭킹 (무신사·화해 등)

── 소싱
  source search <channel> <kw>   상품 검색 (--waterfall 지원)
  source detail <channel> <id>   상품 상세
  source categories <channel>    카테고리 목록
  source route <orderId>         공급사 자동 라우팅
  source score record            공급사 신뢰도 기록

── 상품
  product list <channel>
  product get <channel> <id>
  product register <channel> --file <path>
  product register --all-channels --file <path>
  product update <channel> <id> --file <path>
  product delete <channel> <id>
  product keyword get|set|support
  product seo get|set|support
  product image get|set|add|delete|support
  product sync

── 주문 / 송장
  order list <channel> [--days N]
  order get <channel> <orderId>
  order add <channel> --file <path>     수동 매출 입력
  order remove <channel> <orderId>      주문 삭제
  order confirm <channel> <orderId>
  order cancel <channel> <orderId>
  order return <channel> <orderId>
  invoice register <channel> <orderId> <carrier> <tracking>

── 정산
  settlement calc <channel> <orderId> --amount N
  settlement list [channel] [--days N]
  settlement summary [channel] [--days N]
  settlement schedule                    입금 예정 스케줄
  settlement fee list|set|remove

── 가격 / 쿠폰
  price set <channel> <id> <price>
  price variant shopify <variantId> <price>
  coupon create <channel> --name <이름> --type <타입> --value N
  coupon issue <channel> <couponId> --members <id,...>

── 리포트
  report revenue [--period <기간>]  전 채널 통합 매출 요약 (기본: 오늘)
  report --by <dimension> [옵션]   다차원 판매 리포트
    --by:     category|productline|product|channel|sku
    --period: today|7d|30d|this-month|last-month|3m|all

── 원가
  cost set <sku>                   SKU별 원가 등록
  cost list                        채널·SKU별 공헌이익 현황

── 데이터
  import <file>                    CSV/XLS 자동 감지 → 채널 판별 후 임포트
  mapping add|list                 상품 채널 매핑
  log [list]                       작업 이력 조회
  dashboard [--port N]             웹 대시보드 (기본 포트: 3847)

── 스킬
  setup-skills                     Claude Code 스킬 등록
  setup-skills uninstall           스킬 제거
```

</details>

---

## 로드맵

| Phase | 직무 커버리지 | 상태 |
|-------|-------------|------|
| **Phase 1** | 트렌드 탐색 · 주문 수집(API + CSV) · 정산 관리 · 매출 분석 · 손익 계산 · 상품 등록(부분) | ✅ 출시 |
| **Phase 2** | 상품 멀티채널 일괄 배포 · 재고 완전 동기화 | 🔜 개발 중 |
| **Phase 3** | 재고 모니터링 · 자동 발주 · SCM 연동 | 📋 예정 |

---


## License

MIT
