# 어학연수 지도 사이트 — 운영·개발 인수인계 (Claude Code용)

> 이 문서는 프로토타입 유지보수를 Claude Code로 이어가기 위한 기준 문서입니다.
> 작업 폴더에 이 파일을 두면 Claude Code가 읽고 맥락을 파악합니다. (2026-07-16 기준)

## 1. 프로젝트 개요
- 종로유학원 상담용 어학연수 학교 안내 지도. 몰타·영국·아일랜드, 8개 브랜드(Kaplan, EF, St Giles, Kings, LSI, Bayswater, EC, English Path).
- 단일 HTML 파일(오픈스트리트맵/Leaflet, 라이브러리·로고 내장) — 외부 의존은 지도 타일뿐.
- 배포: Netlify에 HTML 업로드(또는 GitHub 연동 자동 배포 전환 가능).
- 데이터 원본: 마스터시트 xlsx (cities / schools / centers / residences 4시트).

## 2. 권장 폴더 구성
```
지도사이트/
├─ map.html                  ← 현재 v8.3 파일명을 이걸로 변경해 사용
├─ build_map.py              ← 마스터시트 → map.html 데이터 반영
├─ geocode_v3.py             ← 주소 → 좌표 (구글 API 키 필요, 국가 자동 인식)
├─ 어학연수_지도사이트_마스터시트_v3.10.xlsx   ← 데이터 원본 (현재 최신)
└─ README.md                 ← 이 문서
```
git 초기화 권장: 수정 전 상태로 언제든 복구 가능. (`git init` 후 커밋)

## 3. 일상 작업 흐름

### A. 학교·기숙사 추가 (신규 브랜드 or 지점)
1. 클로드 프로젝트에서 브로셔 PDF 추출 → 추출본 xlsx
2. Claude Code에 요청: "추출본을 마스터시트에 병합해줘 — 아래 §5 병합 규칙 따라서"
3. 좌표 채우기: 영국 주소는 우편번호 기반 자동 가능, 그 외는 `python geocode_v3.py`
   (INPUT/OUTPUT 파일명만 최신으로 수정)
4. `python build_map.py` → map.html 갱신 → Netlify 재업로드

### B. 데이터 수정 (이름 변경·폐쇄·정보 갱신)
- 마스터시트에서 직접 수정 → `python build_map.py`
- 이름 변경: name_en만 수정, **ID는 절대 변경 금지** (참조가 끊어짐)
- 센터 폐쇄: 행 삭제 대신 status를 '폐쇄'로 (지도에서 자동 제외)

### C. 디자인·기능 수정
- map.html을 Claude Code로 직접 수정. 데이터(`const D = …`)와
  코드가 한 파일에 있으니, **`const D` ~ `const CONSULT` 사이는 건드리지 말 것**
  (그 블록은 build_map.py가 관리).

## 4. 데이터 규칙 (요약)
- ID: 소문자·하이픈. school_id(kaplan…) / city_id={국가}-{도시}(uk-london, ie-dublin, mt-stjulians)
  / center_id={school}-{city}-{축약} / residence_id 동일 패턴
- 비용·가격 정보는 데이터에 넣지 않음
- 홈스테이·개별 배정형 아파트(고정 주소 없음)는 지도 데이터 제외
- 자료에 없는 값은 '미확인', 일반 지식 보완은 [추정] 표기
- 좌표(lat/lng) 비어 있으면 지도에 점이 안 생길 뿐 오류 아님
- 후기·유튜브는 schools 시트(브랜드 단위), 기숙사 상세는 residences.link

## 5. 병합 규칙 (추출본 → 마스터)
1. cities/schools는 ID 중복 시 건너뜀(기존 유지), centers/residences는 ID 중복이면 오류로 보고
2. source_year·min_age가 숫자로 들어오면 텍스트로 변환 (2026.0 → "2026")
3. 병합 후 검증: 모든 city_id/school_id/linked_center가 실제 존재하는지 (참조 무결성)
4. 신규 도시는 cities에 좌표(도시 중심) 직접 입력
5. school_id가 소문자-하이픈 형식이 아닌 행(메모 행)은 건너뜀

## 6. 현재 상태 (2026-07-16)
- 마스터 v3.10 / 지도 v8.3: 도시 16, 브랜드 8, 센터 50, 기숙사 132
- 지도 미표시 3곳(주소 확인 대기): St Giles 브라이튼 iQ(브라이튼 iQ 3개 지점 중 특정 필요),
  EC 케임브리지 Anglia House, EP 더블린 하우스쉐어(자료 상충)
- 몰타 세인트줄리언스 5곳: 동네 수준 좌표(자동 분산 표시 중) — 정밀 좌표 확인 예정
- 남은 작업: IH·CES 브랜드 추출(+로고), 학교별 유튜브 링크 입력(schools.youtube_urls),
  첫 화면 인트로(국가별 장단점 — 국가 탭별로), 본 구축(Next.js+Supabase+구글맵) 전환

## 7. Claude Code 시작 프롬프트 예시
- "README 읽고 프로젝트 파악해줘. git 초기화하고 현재 상태 커밋해줘."
- "이 폴더의 추출본 IH_추출.xlsx를 §5 규칙대로 마스터에 병합하고 build_map.py 돌려줘."
- "센터 상세 화면에 schools 시트의 youtube_urls 영상을 썸네일로 보여주는 기능 추가해줘."
- "국가 탭 위에 인트로 화면을 추가해줘 — 내용은 intro_uk.md / intro_mt.md 참고."
- "Netlify CLI로 배포 자동화 세팅해줘."
