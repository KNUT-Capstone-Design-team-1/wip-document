# 애플리케이션 구조와 구현 근거

## 기준

- 확인일: 2026-11-14.
- 앱: `wip-application-v2`, 커밋 `8846de40`의 로컬 작업 트리. 확인 당시 앱 저장소의 미커밋 변경 없음.
- `app.config.js`의 앱 버전은 `3.3.9`. 문서 저장소의 마지막 릴리즈 노트는 `3.3.8`이므로 코드 버전을 배포 완료 버전으로 해석하지 않습니다.
- 서버 연결은 로컬 `wip-serverless`, 리소스 생성은 `wip-resource-deployer` 참고. 운영 환경 호출·배포 검증은 수행하지 않았습니다.
- 아래 코드 경로는 별도 표기가 없으면 앱 저장소 기준입니다.

## 기술과 디렉터리

`package.json` 기준 Expo 55, React 19.2, React Native 0.83.10, TypeScript 5.9, Expo Router, Zustand 5, Expo SQLite를 사용합니다. 버전은 선언값이며 실제 설치·운영 환경의 버전과는 구분합니다.

| 경로                                          | 책임                                                    |
| --------------------------------------------- | ------------------------------------------------------- |
| `app/`                                        | 파일 기반 라우트와 루트 초기화                          |
| `src/features/`                               | 기능별 화면·컴포넌트·훅·서비스·데이터 접근              |
| `src/features/shared/`                        | 기능 간 공통 검색 입력·목록·정보 행·이미지 처리         |
| `src/layouts/`                                | 메인 레이아웃, 헤더와 하단 탭                           |
| `src/services/apis/`                          | GCP·Cloudflare API 클라이언트                           |
| `src/services/database/`                      | SQLite 연결·트랜잭션·쿼리·인덱스·마이그레이션·분류 매칭 |
| `src/store/`                                  | 최근 검색·조회, 외부 URL, 공통 모달·로딩 등 공통 상태   |
| `src/components/`, `src/hooks/`, `src/utils/` | 공통 UI·훅·유틸리티                                     |

기본 호출 방향은 화면/컴포넌트 → 훅 → 서비스 → 리포지토리 → 데이터 소스 → 공통 API/DB입니다. 현재 영양제 훅은 DB 쿼리를 직접 호출하고, 일부 스토어는 서비스 호출을 포함합니다. 이 문서는 구현 현황이며 [프론트엔드 규칙](./code_convention/frontend.md)은 신규·수정 코드의 작성 기준입니다.

## 라우트

| 화면                | 라우트 파일                                                                        | 주요 입력                  |
| ------------------- | ---------------------------------------------------------------------------------- | -------------------------- |
| 홈                  | `app/(tabs)/index.tsx`                                                             | 최근 조회·기능 메뉴        |
| 통합 검색           | `app/unified-search/index.tsx`                                                     | 키워드                     |
| 식별 검색           | `app/pill-identification-search/index.tsx`                                         | 외형 조건                  |
| 이미지 검색·카메라  | `app/pill-image-search/index.tsx`, `app/camera/index.tsx`                          | 앞뒷면 사진                |
| 의약품 결과·상세    | `app/pill-search-result-list/index.tsx`, `app/pill-search-result-detail/index.tsx` | 결과 상태, 상세 `ITEM_SEQ` |
| 영양제 검색·상세    | `app/functional-food-search/index.tsx`, `app/functional-food-detail/index.tsx`     | 상세 `reportNumber`        |
| 보관함·폴더         | `app/(tabs)/pill-save/index.tsx`, `app/pill-save-folder/[id].tsx`                  | 폴더 ID                    |
| 복용 알림 목록·설정 | `app/pill-reminder/index.tsx`, `app/pill-reminder-setting/index.tsx`               | 편집 시 `reminderId`       |
| 약국·문의           | `app/(tabs)/nearby-pharmacy/index.tsx`, `app/nearby-pharmacy-inquiry/index.tsx`    | 문의 모드·의약품 컨텍스트  |
| 설정·앱 정보·약관   | `app/(tabs)/setting/index.tsx`, `app/app-info/index.ts`, `app/terms/index.ts`      | 앱·DB 버전                 |
| 공지 목록·상세      | `app/notice/index.tsx`, `app/notice-detail/index.tsx`                              | 선택 공지                  |

## 검색과 상세

| 기능        | 실제 처리                                                                                                     | 대표 근거                                                                                                                     |
| ----------- | ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| 통합 검색   | 앞뒤 공백 제거 → 공백 분리 최대 5개 키워드 → Workers에 limit 100·cursor 전달 → 품목번호로 로컬 기본 정보 결합 | `src/features/unified_search/services/unifiedSearchService.ts`                                                                |
| 식별 검색   | 조건을 공통 SQLite 쿼리로 변환하여 로컬 후보 조회                                                             | `src/services/database/queries/pill_data_query_builder.ts`, `src/services/database/queries/pill_data_filters.ts`              |
| 이미지 검색 | 앞뒷면 사진 필수 → 원격 특징 추출 → 로컬 검색 → 공통 결과 목록                                                | `src/features/pill_image_search/hooks/usePillImageActions.ts`, `src/features/pill_image_search/utils/pillImageActionUtils.ts` |
| 추가 로드   | 통합 검색은 커서·hasMore, 식별·이미지 검색은 로컬 페이지당 30개                                               | `src/features/pill_search_result_list/hooks/use_pill_search_result_list.ts`                                                   |
| 상세        | 로컬 기본 정보와 스테로이드 성분 매칭, 별도 성분 분류, 원격 효능·용법·주의 문서와 운전 주의 문구 결합         | `src/features/pill_search_result_detail/services/pill_detail_service.ts`                                                      |
| 상세 캐시   | 품목별 원격 문서 메모리 캐시, TTL·최대 개수 제한, 동일 품목 동시 요청 공유                                    | `src/features/pill_search_result_detail/data/cache/pill_detail_cache.ts`                                                      |
| 영양제      | 로컬 제품명·업체명·제품 형태 검색, 페이지당 30개, 신고번호로 제품과 분류 기준 조회                            | `src/features/functional_food_search/hooks/`, `src/services/database/queries/functional_food.ts`                              |

의약품 상세는 원격 문서 실패 시 기본 정보만 표시될 수 있습니다. 성분·문구 기반 분류는 진단이나 안전성 보증을 의미하지 않습니다. 영양제 검색을 의약품 통합 검색에 합쳐 조회하는 구조는 아닙니다.

## 저장소와 동시성

- `src/services/database/sqlite.ts`: `app.db`를 여는 Promise를 공유하며, 실패하면 다음 호출에서 재시도합니다.
- `src/services/database/transaction.ts`: 같은 DB 연결의 트랜잭션 실행을 큐로 직렬화합니다. 이 함수 안에서 다시 같은 래퍼를 호출하지 않습니다.
- `src/services/database/query_batch.ts`: 쿼리 바인딩 수를 나눕니다. 저장 항목 이동·복사도 묶음 조회·삽입을 사용합니다.
- `src/services/database/queries/pill_search_indexes.ts`, `resource_query_indexes.ts`: 의약품·리소스 조회 인덱스.
- `src/services/database/queries/classification_catalog.ts`: 분류 데이터 캐시. 리소스 변경 시 무효화합니다.
- 공공 리소스 9개 테이블은 [DB 정책](../operation/database_update_policy.md)에 정리했습니다. 보관함·복용 알림 테이블은 사용자 로컬 데이터로 별도 관리합니다.

## 보관함과 복용 관리

의약품은 품목번호 `ITEM_SEQ`, 영양제는 `functional-food:` + 품목제조신고번호로 공통 보관함의 `item_seq`에 저장합니다. 동일 번호 충돌을 피하고 항목 선택 시 종류에 맞는 상세 라우트로 이동합니다.

| 역할                               | 근거                                                                                                      |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------- |
| 영양제 식별자 변환                 | `src/features/pill_save/utils/saved_item_identity.ts`                                                     |
| 영양제 상세 저장 버튼              | `src/features/functional_food_search/components/atoms/FunctionalFoodSaveButton.tsx`                       |
| 폴더·항목 조회·변경                | `src/features/pill_save/services/pill_save_service.ts`, `data/datasources/pill_save_sqlite_datasource.ts` |
| 항목 이동·복사                     | `src/features/pill_save/data/datasources/pill_save_transfer.ts`                                           |
| 의약품·영양제 알림 메타데이터 결합 | `src/features/pill_reminder/data/datasources/pill_reminder_item_queries.ts`                               |
| 알림 생성·DB 저장·재예약           | `src/features/pill_reminder/services/pill_reminder_create_service.ts`                                     |
| 권한·채널·스케줄·응답 처리         | `src/features/pill_reminder/services/pill_reminder_notification_service.ts`, `data/datasources/`          |
| 알림 선택·5분 뒤 재알림·완료 안내  | `src/features/pill_reminder/utils/reminder_notification_response_helper.ts`                               |

복용 알림은 복수 시간·요일·대상별 복용량을 가진 일정입니다. 완료 액션은 토스트이며 복용 이력 저장 기능은 없습니다. 서버 푸시 토큰 등록·발송이 아닌 기기 로컬 알림입니다.

## 초기화와 공통 기능

`app/_layout.tsx`가 `useAppInitializer`로 초기화를 제어합니다. 초기화 중에는 DB 업데이트 UI를, 완료 후에는 메인 레이아웃·공지·공통 모달·가이드 오버레이를 표시하고 알림 응답 감시를 시작합니다.

- 동기화: `src/features/database_update/services/database_sync_orchestrator.ts`가 다운로드 → 캐시 검증 → SQLite 적용 → 정리를 순서대로 실행합니다. 페이지 캐시는 파일에, 진행 상태는 AsyncStorage에 저장합니다.
- 약국: `src/features/nearby_pharmacy/services/`가 위치·로컬 약국 조회·클러스터링·전화/주소 복사를 담당합니다. 영업 여부는 제공 시간과 날짜를 기준으로 계산합니다.
- 재고 문의: `src/features/nearby_pharmacy/hooks/use_stock_inquiry.ts`가 의약품 정보를 문의 화면에 전달하고 전화 연결을 지원합니다.
- 가이드: 루트의 `GuideProvider`·`GuideOverlay`와 기능별 투어를 사용합니다. [적용 가이드](./guide_integration.md) 참고.
- 공지: Workers 조회와 필독 바텀시트. 광고: Mobile Ads 초기화, 전면·검색 결과 광고.
- 설정: `src/features/setting/hooks/use_setting.ts`가 최근 조회 기록과 모든 복용 알림 삭제를 구분합니다.

## 검증

이 문서 작업에서는 코드와 문서의 대응 관계를 확인합니다. 기기 알림·권한·지도·광고의 실제 동작은 [테스트 시나리오](../test/index.md)에 따라 Android와 iOS에서 별도 확인합니다.

앱 저장소의 현재 검증 명령은 `yarn lint`, `yarn test`, `yarn test:sqlite`, `yarn test:api`입니다. API 테스트는 환경 설정과 원격 서비스가 필요합니다. `test:sqlite`는 영양제 저장, 쿼리 성능, DB 동시성 통합 테스트를 실행합니다.
