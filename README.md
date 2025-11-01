# Crawling_from_YahooFinance (NASDAQ-100)
## 🧩 함수 구성

### 1️⃣ get_nasdaq100_tickers()
- **기능:** Wikipedia에서 **NASDAQ-100 티커 목록**을 불러옴  
- **옵션:** `yahoo_style=True` 시 Yahoo Finance 검색 호환 형식으로 변환 (`BRK.B → BRK-B`)  
- **입력값:** `yahoo_style (bool)`  
- **출력값:** `list[str]` — 변환된 티커 문자열 리스트  
- **주요 로직:**  
  - `pandas.read_html()`로 Wikipedia 표 파싱  
  - ‘Ticker’ 또는 ‘Symbol’ 컬럼 자동 감지 후 추출  
  - 결측값 제거 및 문자열 변환  

---

### 2️⃣ get_href()
- **기능:** Yahoo Finance API를 통해 **특정 티커의 최신 뉴스 링크**를 가져옴  
- **입력값:**  
  - `query (str)` — 검색할 티커  
  - `count (int)` — 가져올 뉴스 개수 (기본값 20)  
- **출력값:** `list[str]` — 뉴스 기사 URL 리스트  
- **특징:**  
  - `query1.finance.yahoo.com/v1/finance/search` 엔드포인트 사용  
  - HTTP 응답 상태코드 출력 (`Status Code: 200` 등)  
  - JSON 파싱 후 `news.link` 필드만 추출  

---

### 3️⃣ update_csv()
- **기능:** Yahoo API로 가져온 새 뉴스 링크를 기존 CSV(`yahoo_news.csv`)에 업데이트  
- **입력값:**  
  - `query (str)` — 티커  
  - `csv_path (str)` — 뉴스 데이터 저장 경로  
  - `filtered_path (str)` — 필터링된 기사 저장 경로  
- **출력값:** `pd.DataFrame` — 업데이트된 전체 뉴스 목록  
- **내부 로직:**  
  - 기존 링크(`old_pairs`) 및 필터링 링크(`filtered_pairs`) 중복 제거  
  - 새로 수집된 링크만 병합  
  - 중복행 제거 후 자동 저장 (`UTF-8-SIG`)  

### 4️⃣ scrape_articles()
- **기능:** Selenium으적:**  
- 중복 저장 방지  

---

### 6️⃣ cleanup_csv()
- **기능:** `headline`, `pubdate`, `article`이 모두 비어있는 행을 제거  
- **입력값:** `csv_path (str)`  
- **출력값:** 없음 (정제된 CSV 저장)  

---

### 7️⃣ 실행부 (`__main__`)
- **기능:** 전체 크롤링 파이프라인 실행  
- **구성 흐름:**  
1. 기존 CSV 정리 (`cleanup_csv`)  
2. Headless Chrome 드라이버 실행  
3. NASDAQ-100 순회하면서 뉴스 업데이트 및 크롤링  
4. 각 티커별 랜덤 대기(3~6초)로 서버 과부하 방지  
5. 모든 크롤링 완료 후 브라우저 자동 종료  
