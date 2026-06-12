# 네이버 검색 뉴스 API 명세서

본 문서는 네이버 검색 오픈API 중 뉴스 검색 서비스의 사양을 정리한 문서입니다.

## 1. 개요
네이버 뉴스 검색 채널에서 제공하는 검색 기사 목록을 가져올 수 있는 API입니다. 쿼리 키워드를 기준으로 검색 결과 매칭 여부, 기사의 작성 시간 등의 정렬 방식을 지정해 관련 뉴스 기사 제목, 링크, 요약(본문 일부), 언론사 표기 링크 및 발행 일자 등을 반환합니다.

---

## 2. API 엔드포인트 및 호출 정보
- **요청 URL**: `https://openapi.naver.com/v1/search/news.json` (XML 형식을 원할 경우 `news.xml` 호출 가능)
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
GET https://openapi.naver.com/v1/search/news.json?query=%EC%9D%B8%EA%B3%B5%EC%A7%80%EB%8A%A5&display=5&start=1&sort=date HTTP/1.1
Host: openapi.naver.com
X-Naver-Client-Id: YOUR_CLIENT_ID
X-Naver-Client-Secret: YOUR_CLIENT_SECRET
```

---

## 4. 응답 구조 (Response Body JSON)

응답 데이터는 뉴스 검색 결과 메타 데이터와 개별 기사 정보 목록으로 구성됩니다.

### 응답 데이터 필드 설명
- `lastBuildDate`: 검색 결과를 생성한 시간 (RFC 822 형식)
- `total`: 전체 검색 결과 개수
- `start`: 시작 위치
- `display`: 한 번에 반환되는 검색 결과 개수
- `items`: 개별 뉴스 검색 결과 리스트 (배열)
  - `title`: 뉴스 기사의 제목 (검색 키워드는 `<b>` 태그로 강조 표시됨)
  - `originallink`: 해당 언론사의 원본 기사 주소 URL
  - `link`: 네이버 뉴스 서비스에서 제공하는 기사 주소 URL (제공되지 않는 기사의 경우 `originallink`와 동일하거나 공백)
  - `description`: 뉴스 기사의 본문 요약 내용 (검색 키워드는 `<b>` 태그로 강조 표시됨)
  - `pubDate`: 뉴스 기사가 발행되거나 제공된 시간 (RFC 822 형식)

### 응답 예시 (JSON)
```json
{
  "lastBuildDate": "Mon, 08 Jun 2026 20:15:00 +0900",
  "total": 985600,
  "start": 1,
  "display": 5,
  "items": [
    {
      "title": "국내 IT 기업, 자체 생성형 <b>인공지능</b> 모델 전격 공개",
      "originallink": "https://www.itnews.co.kr/news/123456",
      "link": "https://n.news.naver.com/mnews/article/001/123456789",
      "description": "최근 국내외 주요 기술 업계가 <b>인공지능</b>(AI) 비즈니스 솔루션 상용화에 속도를 내면서, 이번 모델 발표를 통해 본격적인 시장...",
      "pubDate": "Mon, 08 Jun 2026 18:30:00 +0900"
    }
  ]
}
```
