
# Contact API 문서

## 개요

Contact API는 웹사이트의 문의하기 폼을 통해 사용자의 문의를 접수하고, 관리자가 해당 문의 목록을 조회하는 기능을 제공합니다.

---

## API 엔드포인트

### **POST /api/contact**

사용자가 제출한 문의 내용을 시스템에 저장합니다.

- **요청 바디:**
  ```json
  {
    "name": "홍길동",
    "email": "gildong@example.com",
    "subject": "결제 관련 문의드립니다.",
    "message": "프리미엄 플랜으로 결제했는데, 서비스가 활성화되지 않았습니다. 확인 부탁드립니다."
  }
  ```
- **주요 검증 로직:**
  - 필수 필드(`name`, `email`, `subject`, `message`) 확인
  - 이메일 형식 유효성 검사
  - 각 필드의 최대 길이 제한 (이름: 100, 제목: 200, 내용: 2000자)
  - 요청자의 IP 주소와 User-Agent 정보 수집

- **응답 (성공 시):**
  - **상태 코드:** `200 OK`
  - **바디:**
    ```json
    {
      "success": true,
      "inquiryId": 123,
      "message": "Contact inquiry submitted successfully"
    }
    ```

- **응답 (실패 시):**
  - `400 Bad Request`: 필수 필드 누락, 이메일 형식 오류, 필드 길이 초과 등

### **GET /api/contact**

관리자가 접수된 모든 문의 목록을 조회합니다. (관리자용)

- **인증:**
  - **헤더:** `Authorization: Bearer admin-token`
  - 현재는 고정된 토큰을 사용한 간단한 인증 방식입니다. 실제 운영 환경에서는 더 강력한 인증 메커니즘이 필요합니다.

- **응답 (성공 시):**
  - **상태 코드:** `200 OK`
  - **바디:**
    ```json
    {
      "success": true,
      "inquiries": [ /* ContactInquiry 객체 배열 */ ]
    }
    ```
- **응답 (실패 시):**
  - `401 Unauthorized`: 인증 토큰이 없거나 유효하지 않음

---

## 데이터 모델

### **ContactInquiry**

| 필드        | 타입   | 설명                                         |
| ----------- | ------ | -------------------------------------------- |
| `id`        | number | 문의 고유 ID (자동 증가)                     |
| `name`      | string | 문의자 이름                                  |
| `email`     | string | 문의자 이메일                                |
| `subject`   | string | 문의 제목                                    |
| `message`   | string | 문의 내용                                    |
| `ip`        | string | 요청자 IP 주소                               |
| `user_agent`| string | 요청자 User-Agent                            |
| `created_at`| string | 문의 접수 시간 (ISO 8601)                    |
