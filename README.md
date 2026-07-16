# 어학연수 학교·기숙사 지도 (몰타·영국·아일랜드)

종로유학원 상담용 어학연수 학교 안내 지도. Leaflet(오픈스트리트맵) 기반 단일 HTML 사이트로,
8개 브랜드(Kaplan, EF, St Giles, Kings, LSI, Bayswater, EC, English Path)의 센터와 기숙사 위치를 보여줍니다.

- **배포:** GitHub Pages → https://jaebong-choi.github.io/esl-map-MTUKIR/
- **진입점:** `index.html` (라이브러리·로고 내장, 외부 의존은 지도 타일뿐)
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

`geocode_v3.py`의 `API_KEY`는 자리표시자입니다. **실제 구글 API 키를 이 공개 리포에 커밋하지 마세요.**
로컬에서 실행할 때만 키를 넣고, 커밋 전 자리표시자로 되돌리거나 키에 HTTP 리퍼러/API 제한을 걸어 두세요.
