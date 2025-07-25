
# User API 문서

## 개요

User API는 사용자의 구독 정보와 관련된 기능을 제공합니다. 사용자는 자신의 구독 목록을 조회하고, 각 구독의 상세 정보를 확인할 수 있습니다.

---

## API 엔드포인트

### **GET /api/user/subscriptions**

현재 로그인된 사용자의 모든 구독 정보를 조회합니다.

- **요청:**
  - **메서드:** `GET`
  - **헤더:**
    - `Cookie`: `user_session={sessionToken}` - 사용자의 세션 토큰을 포함해야 합니다.
- **응답 (성공 시):**
  - **상태 코드:** `200 OK`
  - **바디:**
    ```json
    {
      "email": "user@example.com",
      "subscriptions": [
        {
          "orderId": "order_12345",
          "planName": "프리미엄 플랜",
          "planTitle": "프리미엄 플랜",
          "planDescription": "모든 기능을 무제한으로 사용하세요.",
          "planImage": "/images/premium-plan.png",
          "amount": 50000,
          "status": "active",
          "nextBillingDate": "2025-08-25",
          "billingCycle": "monthly",
          "autoRenewal": true,
          "paymentMethod": "credit_card",
          "startDate": "2025-07-25",
          "endDate": null,
          "subscriptionId": "sub_abcdef"
        }
      ]
    }
    ```
- **응답 (실패 시):**
  - **상태 코드:** `401 Unauthorized` - 인증되지 않은 사용자
    ```json
    { "error": "인증이 필요합니다." }
    ```
  - **상태 코드:** `500 Internal Server Error` - 서버 내부 오류
    ```json
    { "error": "구독 정보 조회에 실패했습니다." }
    ```

### **GET /api/user/subscriptions/{id}**

특정 구독의 상세 정보를 조회합니다.

- **요청:**
  - **메서드:** `GET`
  - **경로 변수:**
    - `id`: 조회할 구독의 고유 ID
  - **헤더:**
    - `Cookie`: `user_session={sessionToken}` - 사용자의 세션 토큰을 포함해야 합니다.
- **응답 (성공 시):**
  - **상태 코드:** `200 OK`
  - **바디:**
    ```json
    {
      "orderId": "order_12345",
      "planName": "프리미엄 플랜",
      // ... (위와 동일한 구독 상세 정보)
    }
    ```
- **응답 (실패 시):**
  - **상태 코드:** `401 Unauthorized`
  - **상태 코드:** `404 Not Found` - 해당 ID의 구독을 찾을 수 없음
  - **상태 코드:** `500 Internal Server Error`

---

## 데이터 모델

### **Subscription**

| 필드                | 타입    | 설명                                     |
| ------------------- | ------- | ---------------------------------------- |
| `orderId`           | string  | 주문 ID                                  |
| `planName`          | string  | 플랜 이름                                |
| `planTitle`         | string  | 플랜 제목 (상세)                         |
| `planDescription`   | string  | 플랜 설명                                |
| `planImage`         | string  | 플랜 이미지 URL                          |
| `amount`            | number  | 결제 금액                                |
| `status`            | string  | 구독 상태 (e.g., `active`, `inactive`)   |
| `nextBillingDate`   | string  | 다음 결제일 (YYYY-MM-DD)                 |
| `billingCycle`      | string  | 결제 주기 (e.g., `monthly`, `yearly`)    |
| `autoRenewal`       | boolean | 자동 갱신 여부                           |
| `paymentMethod`     | string  | 결제 수단                                |
| `startDate`         | string  | 구독 시작일 (YYYY-MM-DD)                 |
| `endDate`           | string  | 구독 종료일 (YYYY-MM-DD, `null` 가능)    |
| `subscriptionId`    | string  | 구독 고유 ID                             |

