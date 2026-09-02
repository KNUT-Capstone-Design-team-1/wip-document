## 1. [시맨틱 버저닝](https://2mukee.tistory.com/1014)을 기반으로 `major.minor.patch-build_number`로 명명

- (ex) 2.0.0-123

#### 메이저 버전
- 전체 리팩토링 등 하위 호환성 지원 불가한 업데이트

#### 마이너 버전
- 기능 리팩토링

#### 패치 버전
- 버그 수정, 라이브러리 변경 등 잡다한 변경 사항

## 2. github action 빌드 전 `package.json`의 `version`에 build_number를 제외한 알맞은 버전 값 입력 후 push