---
name: sppt-order-fulfill
description: |
  신규 주문 확인 → 소싱 발주 → 송장 등록까지 전체 주문 처리 워크플로우.
  Use when asked to "주문 처리해줘", "송장 넣어줘", "발주 확인해줘".
allowed-tools:
  - Bash
  - Read
  - Edit
  - Glob
  - Grep
---

# order-fulfill

신규 주문 확인 → 소싱 발주 → 송장 등록까지의 주문 처리 워크플로우.

## When to use

- 사용자가 "새 주문 처리해줘", "주문 확인하고 송장 넣어줘" 같은 요청을 할 때
- 주문 확인부터 배송 처리까지 전체 플로우가 필요할 때

## Prerequisites

- 최소 1개 판매 채널 설정 완료
- `sppt status`로 채널 연결 상태 확인

## Steps

### 1. 신규 주문 조회

```bash
sppt order list <channel>
```

기본적으로 최근 7일간 주문을 조회합니다.

채널별 제약:
- Coupang: 최대 31일 윈도우
- SmartStore: 최대 24시간 윈도우 (자주 확인 필요)

여러 채널의 주문을 모두 확인하려면 각 채널별로 실행합니다.

### 2. 주문 상세 확인

```bash
sppt order get <channel> <orderId>
```

구매자 정보, 배송지, 주문 상품, 옵션을 확인합니다.

### 3. 발주 확인 (주문 접수)

```bash
sppt order confirm <channel> <orderId>
```

판매 채널에 발주 확인(주문 수락)을 전송합니다.
Coupang은 발주 확인 전에만 취소가 가능합니다.

### 4. 소싱 발주

현재 CLI에서 소싱 채널 자동 발주는 미지원입니다.
사용자에게 소싱 채널에서 수동 발주를 안내합니다:

- 주문 상품의 소싱 채널 + 상품 ID 확인
- 배송지: 주문의 수령인 정보 전달
- 발주 후 택배사 코드 + 운송장 번호를 확보

### 5. 송장 등록

```bash
sppt invoice register <channel> <orderId> <carrierCode> <trackingNumber>
```

택배사 코드:
- `cj` — CJ대한통운
- `hanjin` — 한진택배
- `lotte` — 롯데택배
- `post` — 우체국택배
- `logen` — 로젠택배

채널별로 택배사 코드가 자동 매핑됩니다.

### 6. 처리 확인

```bash
sppt order get <channel> <orderId>
```

상태가 `shipped`로 변경되었는지 확인합니다.

## Error handling

- **주문 조회 실패**: `sppt status`로 채널 연결 확인, 토큰 만료 시 `sppt auth <channel>`
- **발주 확인 실패**: 이미 확인된 주문이거나 취소된 주문일 수 있음
- **송장 등록 실패**: 택배사 코드 오타 확인, 운송장 번호 형식 확인

## Notes

- SmartStore는 주문 변경 폴링이 24시간 윈도우이므로 하루에 한 번 이상 확인 권장
- Coupang은 발주 확인 후 취소가 불가능하므로 신중하게 처리
- 여러 주문을 한 번에 처리할 때는 각 주문을 순차적으로 처리합니다
