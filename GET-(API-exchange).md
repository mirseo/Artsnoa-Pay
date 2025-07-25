
# Exchange API 문서

## 개요

Exchange API는 한국수출입은행의 환율 정보를 이용하여 원화(KRW) 금액을 다른 통화로 변환하는 기능을 제공합니다. 개별 금액 변환과 여러 상품 가격의 일괄 변환, 두 가지 모드를 지원하며, 효율적인 처리를 위해 캐싱 및 요청 제한(Rate Limiting) 기능이 포함되어 있습니다.

---

## API 엔드포인트

### **GET /api/exchange**

단일 금액을 지정된 통화로 변환합니다.

- **쿼리 파라미터:**
  - `amount` (필수): 변환할 원화(KRW) 금액 (숫자)
  - `to` (필수): 변환될 목표 통화 코드 (e.g., `USD`, `EUR`, `JPY`)

- **응답 (성공 시):**
  - **상태 코드:** `200 OK`
  - **바디:**
    ```json
    {
      "success": true,
      "originalAmount": 10000,
      "originalCurrency": "KRW",
      "convertedAmount": 7.25,
      "targetCurrency": "USD",
      "exchangeRate": 1379.5,
      "currencyName": "미국 달러",
      "lastUpdated": "2025-07-26T10:00:00.000Z",
      "source": "한국수출입은행",
      "cached": true,
      "rateLimitRemaining": 40
    }
    ```

- **응답 (실패 시):**
  - `400 Bad Request`: 필수 파라미터(`amount`, `to`) 누락
  - `404 Not Found`: 지원하지 않는 통화 코드 요청 시
  - `429 Too Many Requests`: 시간당 요청 한도 초과
  - `500 Internal Server Error`: 환율 정보 조회 실패 등

### **GET /api/exchange/batch**

미리 정의된 여러 상품의 원화 가격을 지정된 통화로 일괄 변환합니다.

- **쿼리 파라미터:**
  - `currency` (필수): 변환될 목표 통화 코드 (e.g., `USD`, `EUR`, `JPY`)

- **응답 (성공 시):**
  - **상태 코드:** `200 OK`
  - **바디:**
    ```json
    {
      "success": true,
      "currency": "USD",
      "prices": {
        "1": { "price": "10.80", "originalPrice": "14.43" },
        "2": { "price": "216.75" }
      },
      "cached": false,
      "lastUpdated": "2025-07-26T10:00:00.000Z"
    }
    ```

- **응답 (실패 시):**
  - `400 Bad Request`: `currency` 파라미터 누락
  - `429 Too Many Requests`: 시간당 요청 한도 초과
  - `500 Internal Server Error`: 환율 정보 조회 실패 등

---

## 주요 기능 및 특징

- **데이터 소스:** 한국수출입은행(Korea Eximbank)에서 제공하는 공공 API를 사용합니다.
- **캐싱:** 환율 정보는 1시간 동안 메모리에 캐시되어 반복적인 외부 API 호출을 최소화합니다.
- **요청 제한 (Rate Limiting):** IP 주소 기반으로 시간당 API 호출 횟수를 제한하여 무분별한 사용을 방지합니다.
  - `/api/exchange`: 시간당 41회
  - `/api/exchange/batch`: 시간당 5회
- **통화 지원:** USD, EUR, JPY, CNY, GBP 등 주요 통화를 지원합니다. 지원되지 않는 통화 요청 시 에러와 함께 사용 가능한 통화 목록을 안내합니다.
