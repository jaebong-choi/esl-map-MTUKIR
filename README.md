# 어학연수 학교·기숙사 지도 (몰타·영국·아일랜드)

종로유학원 상담용 어학연수 학교 안내 지도. **구글맵(Maps JavaScript API)** 기반 단일 HTML 사이트로,
8개 브랜드(Kaplan, EF, St Giles, Kings, LSI, Bayswater, EC, English Path)의 센터와 기숙사 위치를 보여줍니다.

- **배포:** GitHub Pages → https://jaebong-choi.github.io/esl-map-MTUKIR/
- **진입점:** `index.html` (로고 내장, 지도는 Google Maps JS API — 한글 지명 `language=ko`)
- **데이터 규모:** 도시 16 · 브랜드 8 · 센터 50 · 기숙사 132 (마스터시트 v3.10 기준)

## 파일 구성

| 파일 | 역할 |
|------|------|
| `index.html` | 최종 지도 사이트 (배포 대상) |
| `build_map.py` | 마스터시트(xlsx) → `index.html`의 `const D` 데이터 블록 주입 |
| `geocode_v3.py` | 주소 → 좌표 변환 (구글 Geocoding API, 영국/아일랜드/몰타 자동 인식) |
| `어학연수_지도사이트_마스터시트_v3.10.xlsx` | 데이터 원본 (cities / schools / centers / residences 4시트) |
| `README_인수인계.md` | 운영·개발 상세 인수인계 문서 |

## 작업 흐름

1. 데이터 수정은 **마스터시트 xlsx**에서만 (ID는 절대 변경 금지 — 참조 무결성).
2. 새 좌표가 필요하면 `python geocode_v3.py` (API 키를 파일 상단에 입력 후 실행).
3. `python build_map.py` → `index.html` 갱신 (이전 버전은 `map_backup.html`로 자동 백업).
4. 커밋 & push → GitHub Pages 자동 재배포.

> `index.html` 안의 `const D` ~ `const CONSULT` 사이 블록은 `build_map.py`가 관리합니다. 직접 수정하지 마세요.

## 보안 주의

- **지도용 키** (`index.html`의 구글맵 로더): `https://jaebong-choi.github.io/*` 도메인 + Maps JavaScript API로 제한되어 있어
  공개 리포에 있어도 안전합니다. 도메인이 바뀌면 구글 클라우드 콘솔에서 제한 목록을 갱신하세요.
- **지오코딩용 키** (`geocode_v3.py`의 `API_KEY`): 자리표시자입니다. 웹사이트 제한이 걸린 지도용 키는
  파이썬 스크립트에서 작동하지 않으므로, 좌표 변환 시에는 **별도의 서버용(제한 없는/IP 제한) 키**를 로컬에서만 넣어 쓰고
  커밋 전 자리표시자로 되돌리세요.
