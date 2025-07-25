# ArtsnoaPay API Documentation

ArtsnoaPay는 구독 관리 플랫폼을 위한 RESTful API를 제공합니다. 이 API를 통해 결제 처리, 사용자 인증, 구독 관리, 쿠폰 시스템 등의 기능을 이용할 수 있습니다.

## 📋 목차

- [시작하기](#시작하기)
- [인증](#인증)
- [API 엔드포인트](#api-엔드포인트)
- [에러 코드](#에러-코드)
- [지원](#지원)

## 🚀 시작하기

### 기본 정보

- **Base URL**: `https://pay.artsnoa.com`
- **API 버전**: v1
- **Content-Type**: `application/json`
- **인코딩**: UTF-8

### 요구사항

- HTTPS 연결 필수
- API 키 또는 유효한 세션 토큰
- 올바른 Content-Type 헤더

## 🔐 인증

ArtsnoaPay API는 여러 인증 방식을 지원합니다:

### 1. 세션 기반 인증
```http
Cookie: user_session={sessionToken}
```

### 2. API 키 인증
```http
Authorization: Bearer {apiKey}
```

### 3. 관리자 인증
- Google OAuth (`@artsnoa.com` 도메인만 허용)
- 환경 변수 기반 관리자 계정

## 📡 API 엔드포인트

### 🔑 인증 (Auth)
- **NextAuth.js 기반**
- Google OAuth 및 관리자 인증 지원
- 자세한 내용: [auth_api.md](./auth_api.md)

### 💳 결제 (Payment)
- **PortOne 연동**
- 결제 완료, 검증, 상세 조회
- 자세한 내용: [payment_api.md](./payment_api.md)

### 👤 사용자 (User)
- 구독 정보 조회
- 사용자 대시보드 데이터
- 자세한 내용: [user_api.md](./user_api.md)

### 🎟️ 쿠폰 (Admin Coupons)
- 쿠폰 생성, 수정, 삭제
- 쿠폰 사용 통계 조회
- 자세한 내용: [admin_coupons_api.md](./admin_coupons_api.md)

### 📧 이메일 알림 (Notifications)
- 자동 이메일 발송
- 일일 알림 크론 작업
- 자세한 내용: [auto_email_notifications_api.md](./auto_email_notifications_api.md), [cron_daily_notifications_api.md](./cron_daily_notifications_api.md)

### 🔗 매직 링크 (Magic Link)
- 비밀번호 없는 로그인
- 보안 링크 생성 및 검증
- 자세한 내용: [magic_link_api.md](./magic_link_api.md)

### 💱 환율 (Exchange)
- 실시간 환율 조회
- 다중 통화 지원
- 자세한 내용: [exchange_api.md](./exchange_api.md)

### 📞 문의 (Contact)
- 고객 문의 접수
- 문의 관리 시스템
- 자세한 내용: [contact_api.md](./contact_api.md)

## 📊 주요 기능

### 🌍 국제화
- **지원 언어**: 한국어, 영어, 일본어, 중국어
- **자동 감지**: IP 기반 언어/지역 감지
- **통화 변환**: 실시간 환율 적용

### 🛡️ 보안
- **API 키 인증**: `/api/verify-sub` 엔드포인트
- **속도 제한**: 시간당 100 요청 제한
- **도메인 검증**: 신뢰할 수 있는 도메인만 리다이렉트 허용
- **로깅**: 모든 API 접근 기록

### 💰 결제 시스템
- **PortOne 연동**: 카드, KakaoPay, Toss, NaverPay 지원
- **이중 검증**: 클라이언트 + 서버 검증
- **결제 흐름**: 
  ```
  플랜 선택 → 결제 페이지 → PortOne 처리 → 서버 검증 → 성공/리다이렉트
  ```

## ❌ 에러 코드

### HTTP 상태 코드
- `200` - 성공
- `400` - 잘못된 요청
- `401` - 인증 실패
- `403` - 권한 없음
- `404` - 리소스 없음
- `429` - 속도 제한 초과
- `500` - 서버 내부 오류

### 에러 응답 형식
```json
{
  "success": false,
  "error": "에러 메시지",
  "code": "ERROR_CODE",
  "timestamp": "2025-01-01T00:00:00Z"
}
```

## 🔍 구독 검증 API

외부 서비스에서 사용자 구독 상태를 확인할 수 있는 API:

```http
GET /api/verify-sub?email={email}&subId={subId}&apiKey={apiKey}
```

- **인증**: `ARTSNOA_API_KEY` 필수
- **속도 제한**: IP당 시간당 100회
- **용도**: 외부 서비스 구독 검증

## 📝 예제 코드

### JavaScript/Node.js
```javascript
const response = await fetch('https://pay.artsnoa.com/api/user/subscriptions', {
  method: 'GET',
  headers: {
    'Content-Type': 'application/json',
    'Cookie': 'user_session=your_session_token'
  }
});

const data = await response.json();
console.log(data);
```

### Python
```python
import requests

headers = {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer your_api_key'
}

response = requests.get('https://pay.artsnoa.com/api/user/subscriptions', headers=headers)
data = response.json()
print(data)
```

## 🔄 Webhook

결제 완료, 구독 변경 등의 이벤트에 대한 웹훅을 지원합니다:

- **URL**: `POST https://your-domain.com/webhook/artsnoa`
- **서명**: HMAC-SHA256 검증
- **재시도**: 최대 3회, 지수 백오프

## 🛠️ 개발 환경

### 환경 변수
```env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key
SUPABASE_SERVICE_ROLE_KEY=your_supabase_service_role_key

# PortOne 결제
NEXT_PUBLIC_PORTONE_STORE_ID=your_store_id
PORTONE_API_SECRET=your_api_secret

# API 보안
ARTSNOA_API_KEY=your_api_key

# 관리자 인증
ADMIN_EMAIL=admin@artsnoa.com
ADMIN_PASSWORD=your_admin_password
```

### 테스트 계정
- **관리자**: `mirseo@artsnoa.com` (특별 처리)
- **테스트 환경**: 실제 이메일 발송, 구독 데이터 제공

## 📞 지원

### 문의
- **이메일**: help@artsnoa.com
- **회사**: 아트노아 페이
- **사업자등록번호**: 826-19-01951

### 개발자 리소스
- **GitHub**: https://github.com/mirseo/pay-artsnoa.com
- **문서**: https://pay.docs.artsnoa.com
- **대시보드**: https://pay.artsnoa.com/accounts

### 상태 페이지
실시간 API 상태 확인: https://status.artsnoa.com

---

## 📄 라이선스

이 API는 ArtsnoaPay의 독점 소프트웨어입니다. 사용 전 이용약관을 확인해주세요.

© 2025 ArtsnoaPay. All rights reserved.
