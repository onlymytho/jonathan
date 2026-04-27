---
name: sppt-dashboard
description: |
  shopport 운영 대시보드를 브라우저에서 띄우는 스킬.
  Use when asked to "대시보드", "dashboard", "현황판 보여줘".
allowed-tools:
  - Bash
  - Read
---

# dashboard

shopport 웹 대시보드를 로컬에서 띄워 브라우저로 확인하는 워크플로우.

## When to use

- "대시보드 띄워줘", "현황 보고 싶어", "dashboard" 요청 시
- 주문/상품/재고 현황을 시각적으로 확인하고 싶을 때

## Steps

### 1. 대시보드 실행

```bash
sppt dashboard
```

기본 포트 3847에서 대시보드가 실행되고 브라우저가 자동으로 열립니다.

포트를 변경하려면:

```bash
sppt dashboard --port 8080
```

### 2. 대시보드 기능

대시보드에서 확인 가능한 항목:
- 세션별 CLI 명령어 실행 현황 + 진행률
- 주문 조회 결과
- 상품 목록/상세
- 작업 로그

### 3. 종료

대시보드는 CLI 프로세스가 종료되면 함께 종료됩니다.
터미널에서 `Ctrl+C`로 종료합니다.

## Notes

- 대시보드는 로컬 전용 (외부 접근 불가)
- 주요 커맨드(order, product, inventory 등) 실행 시 세션 대시보드가 자동으로 열립니다
- `--no-dashboard` 플래그로 자동 열기를 비활성화할 수 있습니다
