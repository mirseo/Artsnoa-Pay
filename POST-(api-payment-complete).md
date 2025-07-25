
# Payment API 문서

## 개요

Payment API는 PortOne 결제 시스템과의 연동을 통해 결제 완료, 검증, 상세 정보 조회 기능을 제공합니다. 클라이언트에서 결제가 시작된 후, 서버에서 안전하게 결제 상태를 확인하고 처리하는 역할을 담당합니다.

---

## API 엔드포인트

### **POST /api/payment/complete**

클라이언트에서 결제가 완료된 후, 서버에 결제 완료 사실을 알리고 기본적인 검증을 수행합니다.

- **요청:**
  - **메서드:** `POST`
  - **바디:**
    ```json
    {
      "orderId": "order_12345",
      "imp_uid": "imp_abcdef123456",
      "wri": "https://example.com/payment/success" // 선택 사항
    }
    ```
- **응답 (성공 시):**
  - **상태 코드:** `200 OK`
  - **바디:**
    ```json
    {
      "success": true,
      "message": "결제가 성공적으로 완료되었습니다.",
      "payment": {
        "paymentId": "imp_abcdef123456",
        "orderId": "order_12345",
        "amount": 50000,
        "status": "PAID",
        "paidAt": "2025-07-26T10:00:00Z",
        "method": "CARD"
      },
      "redirectUrl": "https://example.com/payment/success" // wri가 제공된 경우
    }
    ```
- **응답 (실패 시):**
  - **상태 코드:** `400 Bad Request` - 필수 파라미터 누락
  - **상태 코드:** `404 Not Found` - PortOne에서 결제 정보를 찾을 수 없음

### **POST /api/payment/verify**

결제의 최종 검증을 수행하고, 데이터베이스에 주문 정보를 생성합니다. 금액 비교 등 중요한 검증 로직이 포함됩니다.

- **요청:**
  - **메서드:** `POST`
  - **바디:**
    ```json
    {
      "paymentId": "imp_abcdef123456",
      "orderId": "order_12345",
      "planId": "plan_premium",
      "billingCycle": "monthly",
      "amount": 50000,
      "customerInfo": {
        "fullName": "홍길동",
        "email": "gildong@example.com",
        "phoneNumber": "010-1234-5678"
      },
      "planName": "프리미엄 플랜",
      "wri": "https://example.com/payment/success" // 선택 사항
    }
    ```
- **응답 (성공 시):**
  - **상태 코드:** `200 OK`
  - **바디:**
    ```json
    {
      "success": true,
      "message": "결제가 성공적으로 검증되었습니다.",
      "order": { /* 생성된 주문 정보 객체 */ },
      "redirectUrl": "https://example.com/payment/success" // wri가 제공된 경우
    }
    ```
- **응답 (실패 시):**
  - **상태 코드:** `400 Bad Request` - 결제가 완료되지 않았거나 금액이 불일치

### **GET /api/payment/details**

특정 결제의 상세 정보를 조회합니다.

- **요청:**
  - **메서드:** `GET`
  - **쿼리 파라미터:**
    - `paymentId`: 조회할 결제의 PortOne ID (`imp_uid`)
- **응답 (성공 시):**
  - **상태 코드:** `200 OK`
  - **바디:**
    ```json
    {
      "paymentId": "imp_abcdef123456",
      "orderId": "order_12345",
      "planName": "프리미엄 플랜",
      "amount": "50,000",
      "paymentMethod": "신용카드",
      "status": "PAID",
      "paidAt": "2025. 07. 26. 19:00:00"
    }
    ```
- **응답 (실패 시):**
  - **상태 코드:** `400 Bad Request` - `paymentId` 누락
  - **상태 코드:** `404 Not Found` - 결제 정보를 찾을 수 없음

---

## 데이터 모델

### **PaymentInfo (Complete 응답)**

| 필드          | 타입   | 설명                               |
| ------------- | ------ | ---------------------------------- |
| `paymentId`   | string | PortOne 결제 ID (`imp_uid`)        |
| `orderId`     | string | 가맹점 주문 ID                     |
| `amount`      | number | 결제 금액                          |
| `status`      | string | 결제 상태 (e.g., `PAID`)           |
| `paidAt`      | string | 결제 완료 시간 (ISO 8601)          |
| `method`      | string | 결제 수단 코드 (e.g., `CARD`)      |

### **PaymentDetails (Details 응답)**

| 필드            | 타입   | 설명                               |
| --------------- | ------ | ---------------------------------- |
| `paymentId`     | string | PortOne 결제 ID (`imp_uid`)        |
| `orderId`       | string | 가맹점 주문 ID                     |
| `planName`      | string | 플랜 이름                          |
| `amount`        | string | 포맷팅된 결제 금액 (e.g., "50,000") |
| `paymentMethod` | string | 한글 결제 수단 (e.g., "신용카드")  |
| `status`        | string | 결제 상태 (e.g., `PAID`)           |
| `paidAt`        | string | 포맷팅된 결제 완료 시간            |
