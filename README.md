# 이게뭐약

AI 기반 의약품 검색 및 상세 정보 제공, 복용 관리, 편의 기능 제공 애플리케이션 서비스

`이게뭐약`은 의약품 키워드 검색, 알약 외형·이미지 식별, 의약품 상세 정보 확인, 영양제 검색, 보관함과 복용 알림, 주변 약국 안내를 제공합니다. 보건·의료 업계 종사자, 약 복용자와 보호자가 필요한 정보를 찾고 일상에서 복용 일정을 관리하도록 돕습니다.

## 문서

- [애플리케이션 소개](./product/app_introduction.md)
- [기능 정의](./operation/abstraction.md)
- [시스템 아키텍처](./diagram/system_architecture.md) / [유스케이스](./diagram/usecase.md)
- [화면 흐름](./diagram/screen_flow.md) / [데이터 흐름](./diagram/data_flow.md)
- [애플리케이션 구조](./development/application_architecture.md)
- [테스트 시나리오](./test/index.md)
- [운영](./operation/index.md) / [프로덕트](./product/index.md) / [개발](./development/index.md) / [다이어그램](./diagram/index.md)
- [릴리즈 노트](./product/release_notes.md) / [개발 관련 링크](./operation/dev_links.md)

## 문서 기준

2026-11-14에 로컬 `wip-application-v2`의 커밋 `8846de40`을 기준으로 라우트, 기능 모듈, 서비스, 저장소, API와 DB 쿼리를 확인했습니다. 서버 내부 연결은 로컬 `wip-serverless`, 원천 데이터 가공은 `wip-resource-deployer` 코드를 함께 참고했습니다. 실제 운영 배포 상태를 확인한 문서는 아닙니다.

현재 코드의 기능과 스토어에 배포된 버전의 기능은 다를 수 있습니다. 배포 이력은 [릴리즈 노트](./product/release_notes.md), 문서 검토 범위는 [검토 기록](./development/documentation_review.md)을 참고하세요.
