# 버저닝

버전 표기는 `major.minor.patch-build_number` 형식을 사용합니다. 예: `3.3.9-227`.

- 메이저: 하위 호환성이 깨지는 전체 구조 변경.
- 마이너: 기능 추가·기능 리팩토링.
- 패치: 버그 수정·라이브러리 변경 등.

## 현재 앱의 버전 설정

`wip-application-v2`의 `app.config.js`에서 `expo.version`을 설정합니다. 빌드 번호는 `BUILD` 환경 변수이며 iOS `buildNumber`, Android `versionCode`에 사용합니다. `BUILD`가 없을 때 코드의 기본값은 `1`입니다. 릴리즈 빌드에서는 배포 대상에 맞는 증가한 빌드 번호를 전달합니다.

현재 `package.json`에는 앱 `version` 필드가 없으므로 해당 파일을 수정하는 예전 절차를 사용하지 않습니다. 실제 빌드 명령은 앱의 `package.json`과 EAS 설정을 확인합니다.

공공 리소스의 스키마·데이터 버전은 앱 버전과 별도로 관리합니다. [DB 업데이트 정책](./database_update_policy.md)을 참고합니다.

배포 완료 기록은 [릴리즈 노트](../product/release_notes.md)에 작성합니다. 현재 코드의 버전만 확인하고 미배포 릴리즈 이력을 추가하지 않습니다.
