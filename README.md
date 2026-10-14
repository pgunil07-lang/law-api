# law-api — 법제처 법령정보 Open API 수집 (W6)

법제처 국가법령정보 Open API로 **검색 → 법령ID → 본문 → FastAPI 서버 → SQLite 캐시** 파이프라인을 만든 실습.

| 파일 | 역할 |
|---|---|
| `step1_search.py` | 목록조회(`lawSearch.do`)로 법령 검색 → 법령ID |
| `step2_detail.py` | 본문조회(`lawService.do`)로 법령ID의 현행 조문 조회 |
| `law_client.py` | 검색·본문조회 재사용 함수 (`search_law`, `get_law_detail`), 인증키는 `.env`에서 읽음 |
| `explore.py` | 응답 JSON을 들여쓰기로 출력해 구조 확인 |
| `main.py` | FastAPI 서버 (`/search`, `/detail/{law_id}`) + DB 캐시 |
| `db.py` | SQLite `laws`·`articles` 테이블 생성·저장·조회 |

실행: `.env`에 `LAW_OC=본인OC` 한 줄을 넣고 `uvicorn main:app --reload` → http://127.0.0.1:8000/docs

## ① 결과 확인

- `search_law("전자금융거래법")` → `전자금융거래법 / 010199`, `전자금융거래법 시행령 / 010366`
- `get_law_detail("010199")` → `제1장 총칙`, `제1조(목적) 이 법은 전자금융거래의 …`, `제2조(정의) …` 순으로 조문 80개
- explore 출력에서 `"법령일련번호": "290247"`과 `"법령ID": "010199"`가 서로 다른 값으로 따로 있음을 확인
- `/detail/010366`을 두 번 호출
  - 1회차: `"source": "api(law.go.kr)"`, `article_count: 54`
  - 2회차: `"source": "db(cache)"`, `fetched_at` 표시 → 법제처를 다시 부르지 않고 DB에서 응답

## ② 핵심 개념 되새김

- 인증키(OC)가 하는 일:
- 목록조회 → 본문조회 2단 호출 구조:
- 받은 데이터를 DB에 캐시하는 이유:

## ③ 자유 로그

- 오늘 배운 것:
- 막힌 곳과 푼 과정:
- 아직 안 풀린 것:
- AI 활용: Claude Code에게 실습 코드 실행 검증(단계 1~5)과 git 커밋·GitHub 업로드를 맡겼고, 각 단계 출력이 워크북의 확인 기준과 일치하는지 비교해 검증했다.
