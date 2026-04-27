---
name: sppt-status-check
description: |
  전채널 연결 상태 + API 건강 체크 + 스토어 브리핑.
  Use when asked to "상태 확인", "채널 연결 돼있어?", "헬스체크", "지금 상황 알려줘".
allowed-tools:
  - Bash
  - Read
  - Glob
  - Grep
---

# status-check

전체 채널 연결 상태를 점검하고 스토어 운영 현황을 브리핑하는 워크플로우.

## When to use

- "지금 상태 어때?", "채널 다 연결돼있어?", "헬스체크 해줘"
- 작업 시작 전 환경 점검
- 오류 발생 후 연결 상태 진단

## Steps

### 1. 채널 연결 상태

```bash
sppt status
```

각 판매/소싱 채널의 연결 상태, 토큰 만료 여부, API 레이턴시를 확인합니다.

### 2. 스토어 브리핑

```bash
sppt context
```

AI용 스토어 정보를 확인합니다:
- 스토어 프로필 (브랜드, 포지셔닝)
- 상품 분류 규칙
- 판매 인사이트

### 3. API 기능 현황

```bash
sppt cap
```

채널별로 어떤 API 기능이 사용 가능한지 확인합니다.

### 4. API 사용량

```bash
sppt budget
```

채널별 API 호출량과 한도를 확인합니다.

### 5. 진단 결과 요약

```
🏥 shopport 헬스체크
────────────────────────────────────
채널          상태    토큰     레이턴시
cafe24        ✅     유효     120ms
coupang       ✅     유효     85ms
smartstore    ⚠️     만료임박  200ms
shopify       ❌     미설정   -
────────────────────────────────────
소싱 채널: domeggook ✅ | specialoffer ✅
```

문제가 있는 채널은 해결 방법을 안내합니다:
- 토큰 만료 → `sppt auth <channel>`
- 미설정 → `sppt init <channel>`
- 레이턴시 높음 → API 상태 모니터링 권장

## Notes

- 작업 시작 전 `sppt status` 습관화 권장
- cafe24 토큰은 2시간마다 자동 갱신됩니다
- `--json` 플래그로 프로그래밍적 활용 가능
