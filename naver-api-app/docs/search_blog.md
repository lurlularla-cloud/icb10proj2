# 네이버 검색 블로그 API 명세서

본 문서는 네이버 검색 오픈API 중 블로그 검색 서비스의 사양을 정리한 문서입니다.

## 1. 개요
네이버 검색에서 블로그의 글을 검색하여 그 결과를 제공하는 API입니다. 쿼리 키워드를 기반으로 작성 시간, 키워드 연관도 등의 정렬 기준에 맞춰 블로그 글 제목, 링크, 요약 정보, 작성일 등을 검색 결과로 반환합니다.

---

## 2. API 엔드포인트 및 호출 정보
- **요청 URL**: `https://openapi.naver.com/v1/search/blog.json` (XML 형식을 원할 경우 `blog.xml` 호출 가능)
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
GET https://openapi.naver.com/v1/search/blog.json?query=%EB%84%A4%EC%9D%B4%EB%B2%84&display=10&start=1&sort=sim HTTP/1.1
Host: openapi.naver.com
X-Naver-Client-Id: YOUR_CLIENT_ID
X-Naver-Client-Secret: YOUR_CLIENT_SECRET
```

---

## 4. 응답 구조 (Response Body JSON)

응답 데이터는 검색 결과에 대한 메타 정보와 개별 블로그 글 정보(배열)로 구성됩니다.

### 응답 데이터 필드 설명
- `lastBuildDate`: 검색 결과를 생성한 시간 (RFC 822 형식)
- `total`: 전체 검색 결과 개수
- `start`: 시작 위치
- `display`: 한 번에 반환되는 검색 결과 개수
- `items`: 개별 블로그 검색 결과 리스트 (배열)
  - `title`: 블로그 포스트의 제목 (검색 키워드는 `<b>` 태그로 강조 표시됨)
  - `link`: 블로그 포스트의 대표 URL
  - `description`: 블로그 포스트 내용의 요약문 (검색 키워드는 `<b>` 태그로 강조 표시됨)
  - `bloggername`: 블로그 개설자의 이름 (닉네임 등)
  - `bloggerlink`: 블로그의 메인 홈 주소
  - `postdate`: 블로그 포스트 작성 날짜 (YYYYMMDD 형식)

### 응답 예시 (JSON)
```json
{
  "lastBuildDate": "Mon, 08 Jun 2026 20:10:00 +0900",
  "total": 1285040,
  "start": 1,
  "display": 10,
  "items": [
    {
      "title": "<b>네이버</b> 검색 활용법 및 꿀팁 정리",
      "link": "https://blog.naver.com/example_user/123456789",
      "description": "오늘은 많은 분들이 사용하고 계시는 <b>네이버</b> 검색 기능을 한층 더 효율적으로 사용할 수 있는 노하우를...",
      "bloggername": "개발하는 일상",
      "bloggerlink": "https://blog.naver.com/example_user",
      "postdate": "20260601"
    }
  ]
}
```
