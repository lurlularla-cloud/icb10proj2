# 네이버 검색 쇼핑 API 명세서

본 문서는 네이버 검색 오픈API 중 쇼핑 검색 서비스의 사양을 정리한 문서입니다.

## 1. 개요
네이버 쇼핑 서비스에서 판매 중인 상품 목록을 검색하여 해당 정보를 조회할 수 있는 API입니다. 특정 키워드에 부합하는 상품들의 상품명, 이미지 URL, 최저가/최고가 가격 정보, 브랜드 및 쇼핑몰 정보, 상품 종류 구분 코드(네이버페이 여부 등)를 반환합니다.

---

## 2. API 엔드포인트 및 호출 정보
- **요청 URL**: `https://openapi.naver.com/v1/search/shop.json` (XML 형식을 원할 경우 `shop.xml` 호출 가능)
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
| `sort` | string | N | `sim` | `sim`, `date`, `asc`, `dsc` | 검색 결과 정렬 방식 <br> * `sim`: 유사도순 (기본값) <br> * `date`: 날짜순 (등록일순) <br> * `asc`: 가격 오름차순 (낮은 가격순) <br> * `dsc`: 가격 내림차순 (높은 가격순) |

### HTTP 요청 예시
```http
GET https://openapi.naver.com/v1/search/shop.json?query=%EB%AC%B4%EC%84%A0%EC%9D%B4%EC%96%B4%ED%8F%B0&display=5&sort=asc HTTP/1.1
Host: openapi.naver.com
X-Naver-Client-Id: YOUR_CLIENT_ID
X-Naver-Client-Secret: YOUR_CLIENT_SECRET
```

---

## 4. 응답 구조 (Response Body JSON)

응답 데이터는 쇼핑 상품 검색 메타 정보 및 개별 상품 상세 정보로 구성됩니다.

### 응답 데이터 필드 설명
- `lastBuildDate`: 검색 결과를 생성한 시간 (RFC 822 형식)
- `total`: 전체 검색 결과 개수
- `start`: 시작 위치
- `display`: 한 번에 반환되는 검색 결과 개수
- `items`: 개별 상품 검색 결과 리스트 (배열)
  - `title`: 상품명 (검색 키워드는 `<b>` 태그로 강조 표시됨)
  - `link`: 상품 구매 페이지 또는 네이버 쇼핑 상세 페이지 URL
  - `image`: 상품 썸네일 이미지의 인터넷 주소 URL
  - `lprice`: 최저가 가격 정보 (단위: 원, 정보가 없을 경우 공백)
  - `hprice`: 최고가 가격 정보 (단위: 원, 정보가 없을 경우 공백)
  - `mallName`: 상품을 판매하는 쇼핑몰 이름
  - `productId`: 네이버 쇼핑 상품 ID
  - `productType`: 상품 종류 타입 구분 (1: 일반상품, 2: 중고상품, 3: 단종상품, 4: 대여상품 등)
  - `brand`: 상품의 브랜드명
  - `maker`: 상품의 제조사명
  - `category1` ~ `category4`: 네이버 쇼핑 대/중/소/세부 카테고리 정보

### 응답 예시 (JSON)
```json
{
  "lastBuildDate": "Mon, 08 Jun 2026 20:16:00 +0900",
  "total": 451000,
  "start": 1,
  "display": 5,
  "items": [
    {
      "title": "노이즈캔슬링 <b>무선이어폰</b> 프로",
      "link": "https://search.shopping.naver.com/catalog/1234567890",
      "image": "https://shopping-phinf.pstatic.net/main_123456/1234567890.jpg",
      "lprice": "159000",
      "hprice": "189000",
      "mallName": "네이버",
      "productId": "1234567890",
      "productType": "1",
      "brand": "가상의브랜드",
      "maker": "가상의제조사",
      "category1": "디지털/가전",
      "category2": "음향가전",
      "category3": "이어폰",
      "category4": "무선이어폰"
    }
  ]
}
```
