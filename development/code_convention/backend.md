# 백엔드 코드 컨벤션

현재 `wip-serverless`는 GCP 서비스와 Cloudflare Workers의 JavaScript 코드이며, `wip-resource-deployer`는 TypeScript 기반 리소스 생성 도구입니다. 언어별 기존 모듈의 명명 규칙을 따릅니다.

## 파일과 이름

- JavaScript·TypeScript 함수·변수는 기존 모듈의 camelCase 규칙을 따릅니다.
- 서비스 디렉터리는 `wip-pill-detail`처럼 기존 kebab-case, 공통 역할 파일은 `authentication.js`, `cache.js`처럼 목적이 보이는 이름을 씁니다.
- Python 파일을 추가할 때에는 파일·변수·함수에 snake_case, 클래스에 PascalCase를 사용합니다. 현재 백엔드 전체를 Python 구현으로 설명하지 않습니다.

## 서비스 경계

- 요청 입력·인증·응답 변환과 검색·데이터 가공 로직의 책임을 구분합니다.
- 페이지 번호·limit·cursor·품목번호·테이블 등 외부 입력을 검증하고 허용 범위 안에서 처리합니다.
- D1은 바인딩 쿼리를 사용합니다. 리소스 JSON·스키마·버전 변경은 앱의 동기화 계약과 함께 확인합니다.
- 의약품 통합 검색은 품목번호와 커서를 반환하고, 의약품 상세 API는 효능·용법·주의 문서를 제공합니다. 건강기능식품 로컬 검색·복용 알림 예약과 책임을 혼동하지 않습니다.
- 원격 API 실패·타임아웃·대체 조회·캐시 만료를 명시적으로 처리합니다.
- 캐시 키에는 결과에 영향을 주는 검색 조건·페이지·버전을 포함합니다.
- 로그·예제·문서에 API 키·토큰·인증 파일 내용을 기록하지 않습니다.

실제 서비스 연결은 [시스템 아키텍처](../../diagram/system_architecture.md)를 참고합니다.
