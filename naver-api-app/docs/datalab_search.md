# 네이버 데이터랩 통합 검색어 트렌드 API 명세서

본 문서는 네이버 데이터랩 통합 검색어 트렌드 조회 API의 사양을 정리한 문서입니다.

## 1. 개요
네이버 검색에서 특정 검색어 그룹의 검색 추이(트렌드) 정보를 날짜별로 제공받을 수 있는 API입니다. 최대 5개의 검색어 그룹을 지정하여 그룹 간 상대적인 검색어 트렌드 수치를 비교 분석할 수 있습니다.

---

## 2. API 엔드포인트 및 호출 정보
- **요청 URL**: `https://openapi.naver.com/v1/datalab/search`
- **HTTP 메서드**: `POST`
- **콘텐츠 타입(Content-Type)**: `application/json`

---

## 3. 요청 변수 (Request Body JSON)

HTTP POST 방식으로 요청 시 Body에 JSON 포맷으로 아래 파라미터들을 지정해야 합니다.

| 파라미터명 | 타입 | 필수 여부 | 설명 |
| :--- | :--- | :--- | :--- |
| `startDate` | string | Y | 조회 시작 날짜 (YYYY-MM-DD 형식, 2016-01-01부터 조회 가능) |
| `endDate` | string | Y | 조회 종료 날짜 (YYYY-MM-DD 형식) |
| `timeUnit` | string | Y | 구간 단위 (`date`: 일간, `week`: 주간, `month`: 월간) |
| `keywordGroups` | array | Y | 비교할 키워드 그룹 배열 (최대 5개 그룹 지정 가능) |
| `keywordGroups.groupName` | string | Y | 주제어 그룹명 (예: "아이폰", "갤럭시" 등) |
| `keywordGroups.keywords` | array | Y | 주제어 그룹에 포함시킬 세부 키워드 목록 (최대 20개) |
| `device` | string | N | 기기 유형 설정 (설정하지 않으면 전체, `pc`: PC, `mo`: 모바일) |
| `gender` | string | N | 성별 필터링 (설정하지 않으면 전체, `m`: 남성, `f`: 여성) |
| `ages` | array | N | 연령대 필터링 (설정하지 않으면 전체, `1`~`11` 구간 코드 배열) <br> * `1`: 0~12세, `2`: 13~18세, `3`: 19~24세, `4`: 25~29세, `5`: 30~34세, `6`: 35~39세, `7`: 40~44세, `8`: 45~49세, `9`: 50~54세, `10`: 55~59세, `11`: 60세 이상 |

### 요청 예시 (JSON)
```json
{
  "startDate": "2023-01-01",
  "endDate": "2023-01-31",
  "timeUnit": "date",
  "keywordGroups": [
    {
      "groupName": "스마트폰",
      "keywords": ["아이폰", "iPhone", "갤럭시", "Galaxy"]
    }
  ],
  "device": "pc",
  "ages": ["3", "4"]
}
```

---

## 4. 응답 구조 (Response Body JSON)

응답 데이터는 설정한 기간 내에서 지정한 그룹의 상대적 검색어 비중을 나타냅니다. (기간 내 최대 검색량을 100으로 설정한 상대 수치)

### 응답 데이터 필드 설명
- `startDate`: 조회 시작 날짜
- `endDate`: 조회 종료 날짜
- `timeUnit`: 구간 단위
- `results`: 키워드 그룹별 트렌드 결과 목록
  - `title`: 주제어 그룹명
  - `keywords`: 주제어 그룹에 포함된 세부 키워드 배열
  - `data`: 날짜별 트렌드 수치 배열
    - `period`: 해당 구간 시작일 (YYYY-MM-DD)
    - `ratio`: 검색 추이 비율 (0 ~ 100 사이의 소수)

### 응답 예시 (JSON)
```json
{
  "startDate": "2023-01-01",
  "endDate": "2023-01-31",
  "timeUnit": "date",
  "results": [
    {
      "title": "스마트폰",
      "keywords": [
        "아이폰",
        "iPhone",
        "갤럭시",
        "Galaxy"
      ],
      "data": [
        {
          "period": "2023-01-01",
          "ratio": 78.43
        },
        {
          "period": "2023-01-02",
          "ratio": 100.0
        }
      ]
    }
  ]
}
```
