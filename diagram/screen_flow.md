# Screen Flowcharts

## 1. Unified Search (통합 검색)
```mermaid
flowchart TD
    subgraph UnifiedSearchScreen[통합 검색 화면]
        SearchInput[검색어 입력창]
        SearchButton[검색 버튼]
        RecentSearch[최근 검색어 목록]
    end
    
    SearchInput -->|텍스트 입력| SearchButton
    SearchButton -->|클릭| PillSearchResultListScreen[검색 결과 목록 화면]
    RecentSearch -->|아이템 클릭| PillSearchResultListScreen
```

## 2. Pill Identification Search (알약 식별 검색)
```mermaid
flowchart TD
    subgraph PillIdentificationSearchScreen[알약 식별 검색 화면]
        ShapeSelect[모양 선택 버튼]
        ColorSelect[색상 선택 버튼]
        LineSelect[분할선 선택 버튼]
        MarkInput[마크 입력창]
        SearchButton[검색 버튼]
    end

    ShapeSelect -->|선택| SearchButton
    ColorSelect -->|선택| SearchButton
    LineSelect -->|선택| SearchButton
    MarkInput -->|입력| SearchButton
    SearchButton -->|클릭| PillSearchResultListScreen[검색 결과 목록 화면]
```

## 3. Pill Image Search (알약 이미지 검색)
```mermaid
flowchart TD
    subgraph PillImageSearchScreen[알약 이미지 검색 화면]
        CameraButton[카메라 촬영 버튼]
        GalleryButton[갤러리 선택 버튼]
        ImageCrop[이미지 크롭 및 확인]
        SearchButton[검색 버튼]
    end

    CameraButton -->|촬영| ImageCrop
    GalleryButton -->|선택| ImageCrop
    ImageCrop -->|확인| SearchButton
    SearchButton -->|클릭| PillDetailScreen[알약 상세 정보 화면]
```

## 4. Pill Search Result List (검색 결과 목록)
```mermaid
flowchart TD
    subgraph PillSearchResultListScreen[검색 결과 목록 화면]
        ResultItem[알약 검색 결과 리스트 아이템]
        FilterButton[필터 버튼]
        SortButton[정렬 버튼]
    end

    FilterButton -->|클릭| ResultItem
    SortButton -->|클릭| ResultItem
    ResultItem -->|클릭| PillDetailScreen[알약 상세 정보 화면]
```

## 5. Pill Detail (알약 상세 정보)
```mermaid
flowchart TD
    subgraph PillDetailScreen[알약 상세 정보 화면]
        SaveButton[보관함 저장 버튼]
        ShareButton[공유 버튼]
        InfoTab[기본 정보/주의사항 탭]
    end

    SaveButton -->|클릭| PillSaveScreen[알약 보관함 화면]
    InfoTab -->|탭 전환| PillDetailScreen
```

## 6. Pill Save (알약 보관함)
```mermaid
flowchart TD
    subgraph PillSaveScreen[알약 보관함 화면]
        SavedPillItem[보관된 알약 아이템]
        DeleteButton[삭제 버튼]
    end

    SavedPillItem -->|클릭| PillDetailScreen[알약 상세 정보 화면]
    DeleteButton -->|클릭| PillSaveScreen
```

## 7. Nearby Pharmacy (주변 약국)
```mermaid
flowchart TD
    subgraph NearbyPharmacyScreen[주변 약국 화면]
        MapView[지도 뷰]
        PharmacyMarker[약국 마커]
        PharmacyDetail[약국 상세 정보 바텀시트]
    end

    MapView -->|이동| PharmacyMarker
    PharmacyMarker -->|클릭| PharmacyDetail
```

## 8. Setting (설정)
```mermaid
flowchart TD
    subgraph SettingScreen[설정 화면]
        NoticeButton[공지사항 버튼]
        TermsButton[이용약관 버튼]
        ClearDataButton[데이터 초기화 버튼]
    end

    NoticeButton -->|클릭| NoticeScreen[공지사항 화면]
    TermsButton -->|클릭| TermsScreen[이용약관 화면]
    ClearDataButton -->|클릭| Dialog[확인 다이얼로그]
```
