# 네이버 검색 카페글 API 명세서

본 문서는 네이버 검색 오픈API 중 카페글 검색 서비스의 사양을 정리한 문서입니다.

## 1. 개요
네이버 카페에 게시된 공개 글 중 검색어에 매칭되는 게시글 정보를 조회하는 API입니다. 쿼리 키워드를 기반으로 연관도 혹은 작성일 기준의 정렬 방식에 따라 카페 게시물 제목, 본문 일부 요약, 링크, 카페 이름 및 카페 주소 등을 검색 결과로 제공합니다.

---

## 2. API 엔드포인트 및 호출 정보
- **요청 URL**: `https://openapi.naver.com/v1/search/cafearticle.json` (XML 형식을 원할 경우 `cafearticle.xml` 호출 가능)
- **HTTP 메서드**: `GET`
- **콘텐츠 타입(Content-Type)**: `application/json` (기본값)

---

## 3. 요청 변수 (Query Parameter)

API 호출 시 URL 쿼리 스트링을 통해 아래 파라미터들을 지정해야 합니다.

| 파라미터명 | 타입 | 필수 여부 | 기본값 | 허용 범위 | 설명 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `query` | string | Y | - | - | 검색어 (UTF-8 URL 인코딩 필요) |
| `display` | integer | N | 10 | 10 ~ 100 | 한 번에 표시할 검색 결과 개수 |
| `start` | integer | N | 1 | 1 ~ 1000 | 검색 시작 위치 (최대 1000까지 허용) |
| `sort` | string | N | `sim` | `sim`, `date` | 검색 결과 정렬 방식 <br> * `sim`: 유사도순 (기본값) <br> * `date`: 날짜순 (최신순) |

### HTTP 요청 예시
```http
GET https://openapi.naver.com/v1/search/cafearticle.json?query=%EC%BA%A0%ED%95%91&display=5&start=1&sort=sim HTTP/1.1
Host: openapi.naver.com
X-Naver-Client-Id: YOUR_CLIENT_ID
X-Naver-Client-Secret: YOUR_CLIENT_SECRET
```

---

## 4. 응답 구조 (Response Body JSON)

응답 데이터는 카페글 검색에 대한 결과 수 및 개별 게시글 리스트로 구성되어 있습니다.

### 응답 데이터 필드 설명
- `lastBuildDate`: 검색 결과를 생성한 시간 (RFC 822 형식)
- `total`: 전체 검색 결과 개수
- `start`: 시작 위치
- `display`: 한 번에 반환되는 검색 결과 개수
- `items`: 개별 카페글 검색 결과 리스트 (배열)
  - `title`: 카페 게시글의 제목 (검색 키워드는 `<b>` 태그로 강조 표시됨)
  - `link`: 카페 게시글의 대표 URL
  - `description`: 카페 게시글 내용의 요약문 (검색 키워드는 `<b>` 태그로 강조 표시됨)
  - `cafename`: 해당 카페 게시물이 작성된 네이버 카페의 이름
  - `cafeurl`: 해당 카페의 메인 홈 주소

### 응답 예시 (JSON)
```json
{
  "lastBuildDate": "Mon, 08 Jun 2026 20:12:00 +0900",
  "total": 354020,
  "start": 1,
  "display": 5,
  "items": [
    {
      "title": "초보자를 위한 감성 <b>캠핑</b> 준비물 리스트 공유",
      "link": "https://cafe.naver.com/camping_example/987654",
      "description": "이제 본격적인 <b>캠핑</b> 시즌이 돌아왔네요. 카페 회원님들을 위해 필수 준비물과 장비 고르는 방법을 정리해 보았습니다...",
      "cafename": "캠핑을 사랑하는 사람들",
      "cafeurl": "https://cafe.naver.com/camping_example"
    }
  ]
}
```
