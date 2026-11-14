# 화면 흐름

`app/`의 Expo Router 경로와 기능별 이동 코드를 기준으로 작성했습니다. 화면 안의 모달·바텀시트와 별도 라우트를 구분합니다.

## 1. 시작 및 주요 진입

```mermaid
flowchart TD
    Launch[앱 실행] --> Init[외부 설정 · 로컬 DB 초기화]
    Init --> Version{리소스 업데이트 필요?}
    Version -->|없음| Tabs[메인 탭]
    Version -->|선택 업데이트| Prompt[업데이트 확인 모달]
    Prompt -->|나중에| Tabs
    Prompt -->|진행| Sync[다운로드 · 검증 · DB 반영]
    Version -->|필수 또는 중단된 업데이트| Sync
    Sync --> Tabs
    Tabs --> Home["홈 /"]
    Tabs --> Save["보관함 /pill-save"]
    Tabs --> Pharmacy["주변 약국 /nearby-pharmacy"]
    Tabs --> Setting["설정 /setting"]
```

## 2. 의약품 검색 및 상세

```mermaid
flowchart TD
    Home[홈] --> Unified["통합 검색 /unified-search<br/>검색어 · 최근 검색어"]
    Home --> Identify["식별 검색 /pill-identification-search<br/>문자 · 제품 · 회사 · 외형 조건"]
    Home --> Image["이미지 검색 /pill-image-search"]
    Image --> Input["앞면과 뒷면 사진 등록<br/>앨범 · 파일 · 카메라"]
    Input --> Camera["촬영 /camera"]
    Camera --> Input
    Input --> Analyze[원격 AI 특징 추출]
    Analyze --> Candidates[로컬 후보 검색]
    Unified --> Results["검색 결과 /pill-search-result-list"]
    Identify --> Results
    Candidates --> Results
    Results --> More["추가 로드<br/>통합 검색 커서 · 로컬 검색 페이지"]
    More --> Results
    Results --> Research[검색 조건 확인 · 수정 · 재검색]
    Research --> Results
    Results --> Detail["의약품 상세 /pill-search-result-detail<br/>ITEM_SEQ"]
    Detail --> Find[상세 내 키워드 찾기]
    Detail --> Folder["폴더 선택 모달<br/>상세 화면에서 저장"]
    Detail --> Inquiry[재고 문의 안내 모달]
    Inquiry --> Pharmacy["약국 문의 /nearby-pharmacy-inquiry"]
```

이미지 검색에는 앞뒷면 사진이 모두 필요합니다. 특징 인식 실패·API 실패 시 안내 후 검색 화면에 머뭅니다. 통합 검색의 빈 결과도 안내 후 현재 화면에 머뭅니다. 상세 저장 버튼은 폴더 선택 모달을 열며 보관함 탭으로 즉시 이동하지 않습니다.

## 3. 영양제 검색 및 상세

```mermaid
flowchart TD
    Home[홈] --> Search["영양제 검색 /functional-food-search"]
    Search --> Filter[제품명 · 업체명 · 제품 형태]
    Filter --> List[동일 화면에서 목록 및 추가 로드]
    List --> Detail["영양제 상세 /functional-food-detail<br/>reportNumber"]
    Detail --> Product[제품 · 원재료 · 섭취 방법 · 주의사항]
    Detail --> Standards[연결된 기능성 기준]
    Detail --> Folder[공통 폴더 선택 모달]
    Search --> External[식품안전나라 외부 페이지]
```

## 4. 보관함 및 복용 알림

```mermaid
flowchart TD
    Save["보관함 /pill-save"] --> Folder["폴더 상세 /pill-save-folder/[id]"]
    Save --> Edit[폴더 생성 · 이름 변경 · 정렬 · 삭제]
    Folder --> Items[의약품 · 영양제 저장 목록]
    Items --> Kind{항목 종류}
    Kind -->|의약품| Drug[의약품 상세]
    Kind -->|영양제| Food[영양제 상세]
    Folder --> Transfer[항목 선택 · 이동 · 복사 · 삭제]
    Save --> Reminders["복용 알림 목록 /pill-reminder"]
    Items --> Specific[항목별 복용 알림 바텀시트]
    Specific --> Form["알림 설정 /pill-reminder-setting"]
    Reminders --> Form
    Form --> Selection[보관 항목 선택 모달]
    Selection --> Form
    Form --> Schedule[복용량 · 시간 · 요일 · 제목 · 메모 저장]
    Schedule --> OS[OS 로컬 알림 예약]
    OS --> Tap[알림 본체 선택]
    Tap --> Form
    OS --> Snooze[5분 뒤 다시 알림]
```

## 5. 주변 약국 및 설정

```mermaid
flowchart TD
    Pharmacy[주변 약국 또는 재고 문의 화면] --> Permission[위치 권한 확인]
    Permission --> Map[지도 · 약국 마커 · 클러스터]
    Map --> Research[지도 이동 후 현재 위치에서 검색]
    Research --> Map
    Map --> Open[현재 영업 중 필터]
    Open --> Map
    Map --> Card[단일 약국 카드 또는 클러스터 목록]
    Card --> Call[전화 앱 연결]
    Card --> Copy[주소 복사]
    Setting[설정] --> Info["앱 정보 /app-info<br/>앱 및 DB 버전"]
    Setting --> Terms["이용약관 /terms"]
    Setting --> Notice["공지 목록 /notice"]
    Notice --> Detail["공지 상세 /notice-detail"]
    Setting --> Clear[최근 조회 기록 삭제 확인 모달]
    Setting --> Delete[모든 복용 알림 삭제 확인 모달]
```

설정의 '기록 삭제'는 최근 조회 의약품 기록에 적용됩니다. 최근 검색어는 통합 검색 화면의 관리 기능과 구분합니다.
