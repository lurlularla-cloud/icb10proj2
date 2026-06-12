# 네이버 오픈API 사용 가이드

본 문서는 네이버 오픈API를 사용하기 위한 공통 가이드라인을 정리한 문서입니다.

## 1. 개요
네이버 오픈API는 외부 개발자나 기업이 네이버의 다양한 서비스 기능과 데이터를 프로그램에서 직접 호출하여 사용할 수 있도록 제공하는 인터페이스입니다. 
기본적으로 RESTful API 방식으로 제공되며, 데이터 포맷으로는 JSON 또는 XML을 사용합니다. (현재 대부분의 API는 JSON 형식을 표준으로 지원합니다.)

---

## 2. API 이용 등록 및 키 발급
네이버 오픈API를 호출하기 위해서는 먼저 네이버 개발자 센터에서 애플리케이션을 등록하고 인증키(Client ID 및 Client Secret)를 발급받아야 합니다.

1. **네이버 개발자 센터 로그인**: 네이버 계정으로 로그인합니다.
2. **애플리케이션 등록**: [Application] -> [애플리케이션 등록] 메뉴로 이동합니다.
3. **정보 입력**:
   - **애플리케이션 이름**: 프로젝트 식별용 이름 설정
   - **사용 API 선택**: 서비스 환경에 따라 사용할 API 서비스(예: 검색, 데이터랩 등)를 선택합니다.
   - **로그인 오픈 API 서비스 환경 설정**: 웹, Android, iOS 등 호출 환경을 선택하고 도메인 또는 패키지 이름을 등록합니다.
4. **발급 완료**: 등록 완료 시 `Client ID`와 `Client Secret`이 발급됩니다. 이 값들은 외부로 노출되지 않도록 주의해야 합니다.

---

## 3. API 요청 및 인증 헤더 구성
네이버 오픈API를 호출할 때는 HTTP 요청 헤더에 발급받은 클라이언트 인증 정보를 반드시 포함해야 합니다.

* **요청 헤더(Header)**:
  - `X-Naver-Client-Id`: 발급받은 Client ID
  - `X-Naver-Client-Secret`: 발급받은 Client Secret

### HTTP 요청 예시 (Curl)
```bash
curl -X GET "https://openapi.naver.com/v1/search/blog.json?query=테스트" \
     -H "X-Naver-Client-Id: YOUR_CLIENT_ID" \
     -H "X-Naver-Client-Secret: YOUR_CLIENT_SECRET"
```

---

## 4. 공통 에러 코드
API 호출 시 오류가 발생하는 경우 응답 바디에 에러 코드(`errorCode`)와 에러 메시지(`errorMessage`)가 반환됩니다.

| HTTP 상태 코드 | 에러 코드 | 의미 / 원인 | 해결 방안 |
| :--- | :--- | :--- | :--- |
| **400** | SE01 | 잘못된 쿼리 요청 (Invalid query) | 요청 변수(Query Parameter)의 형식이나 필수 여부를 확인합니다. |
| **401** | AE01 | 인증 실패 (Authentication failed) | `X-Naver-Client-Id`와 `X-Naver-Client-Secret` 헤더 값이 올바른지 확인합니다. |
| **403** | AE02 | 권한 없음 (Authorization failed) | 개발자 센터 애플리케이션 설정에서 해당 API가 사용 API로 선택되어 있는지 확인합니다. |
| **404** | - | 호출 URL 에러 (Not Found) | 호출하고자 하는 API의 엔드포인트 URL이 맞는지 확인합니다. |
| **429** | - | 호출 빈도 초과 (Quota Exceeded) | API별 일일 허용 호출 쿼터를 초과한 경우로, 호출 주기를 조절하거나 추가 권한을 요청합니다. |
| **500** | SE99 | 서버 내부 오류 (System Error) | 네이버 API 서버 시스템 오류입니다. 잠시 후 재시도하거나 개발자 센터로 문의합니다. |

---

## 5. 유의 사항 및 호출 쿼터
- 네이버 오픈API는 각 계정 및 애플리케이션별로 일일 호출 한도(Quota)가 정해져 있습니다. 
- 한도는 매일 00:00(KST) 기준으로 초기화됩니다. 
- API 사용 시 서비스 약관 및 정책에 명시된 비허용 행위(예: 데이터 무단 전재, 과도한 트래픽 유발 등)를 준수해야 합니다.
