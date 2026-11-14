# 데이터 흐름

기준과 소스 경로는 [애플리케이션 구조](../development/application_architecture.md)를 참고합니다.

## 의약품 검색

```mermaid
sequenceDiagram
    actor User as 사용자
    participant App as 앱
    participant API as 검색 및 AI API
    participant DB as 로컬 SQLite
    alt 통합 검색
        User->>App: 키워드 입력
        App->>API: 공백 구분 최대 5개 키워드, limit, cursor
        API-->>App: 품목번호 목록, nextCursor, hasMore
        App->>DB: 품목번호로 의약품 기본 정보 조회
        DB-->>App: 목록 데이터
    else 식별 검색
        User->>App: 식별 문자와 외형 조건
        App->>DB: 조건 검색 및 페이지 조회
        DB-->>App: 후보 목록
    else 이미지 검색
        User->>App: 앞면 및 뒷면 사진
        App->>API: 이미지 특징 추출 요청
        API-->>App: 외형 및 식별 조건
        App->>DB: 추출 조건으로 후보 조회
        DB-->>App: 후보 목록
    end
    App-->>User: 결과 목록
    User->>App: 후보 선택
    App->>DB: 기본 정보 및 성분 분류 데이터
    App->>API: 효능, 용법, 주의 문서 조회
    App-->>User: 의약품 상세 정보
```

통합 검색의 원격 번호와 로컬 데이터 버전이 다르면 조회 가능한 항목 수가 달라질 수 있습니다. `totalDataCount`를 서버 전체 검색 건수라고 단정하지 않습니다. 상세 기본 정보는 원격 문서보다 먼저 표시될 수 있으며 원격 실패 시 일부 내용이 제한됩니다.

## 보관 및 복용 관리

```mermaid
sequenceDiagram
    actor User as 사용자
    participant App as 앱
    participant DB as 로컬 SQLite
    participant OS as 기기 알림 시스템
    User->>App: 의약품 또는 영양제 상세에서 저장
    App->>DB: 폴더와 항목 저장
    Note over App,DB: 의약품 ITEM_SEQ / 영양제 functional-food:품목제조신고번호
    User->>App: 보관 항목, 복용량, 시간과 요일 설정
    App->>OS: 알림 권한 확인 및 요청
    App->>DB: 복용 알림 및 대상 항목 저장
    App->>OS: 알림 일정 재예약
    OS-->>User: 예약된 복용 시간 알림
    opt 5분 뒤 다시 알림
        User->>OS: 다시 알림 액션
        OS->>App: 알림 응답 전달
        App->>OS: 5분 뒤 단발 알림 예약
    end
```

`복용 완료` 액션은 현재 안내 토스트를 표시하며 복용 이력을 DB에 누적하지 않습니다.

## 공공 리소스 동기화

```mermaid
flowchart TD
    Boot[외부 설정 · 로컬 DB 초기화] --> Compare[서버와 로컬 스키마 및 데이터 버전 비교]
    Compare --> Required{필수 또는 중단된 업데이트?}
    Required -->|아니요 · 업데이트 있음| Ask[사용자 선택]
    Ask -->|수락| Fetch[페이지 병렬 수신 · 파일 캐시]
    Required -->|예| Fetch
    Ask -->|거절| Main[메인 진입]
    Compare -->|업데이트 없음| Main
    Fetch --> Validate[캐시 페이지 구조 및 누락 검증]
    Validate --> Apply[SQLite 반영 · 0건 삽입 감지]
    Apply --> Version[성공한 테이블의 버전 갱신]
    Version --> Cleanup[캐시 및 진행 상태 정리]
    Cleanup --> Main
    Fetch -. 진행 상태 저장 .-> State[(AsyncStorage)]
    State -. 재시작 시 복구 판단 .-> Compare
    Validate -->|실패| Retry[실패 처리 · 정리 · 지연 재시도]
    Apply -->|실패| Retry
    Fetch -->|실패| Retry
    Retry --> Fetch
```

전체 업데이트가 하나의 트랜잭션으로 원자 교체되는 구조는 아닙니다. 테이블·삽입 배치 단위 처리와 실패 후 재시도를 사용합니다. OS가 앱을 중지한 동안의 계속 실행을 보장하지 않으며, 복귀·재실행 시 저장 상태로 복구합니다.
