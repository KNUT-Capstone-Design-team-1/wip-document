# 프론트엔드 (Application) 코드 컨벤션

## 파일 및 변수 네이밍
- **jsx 파일**: 카멜 케이스 (CamelCase 또는 PascalCase 적용)
- **소스코드 파일 (js, ts)**: 스네이크 케이스 (snake_case) 또는 케밥 케이스(kebab-case) 권장 (프로젝트 설정에 맞춤)
- **변수명**: 카멜 케이스 (camelCase)
- **함수명**: 카멜 케이스 (camelCase), 사용하는 언어의 규칙을 준수

## 일반적인 React Native 코드 규칙
- 함수형 컴포넌트와 Hooks를 적극 활용한다.
- 불필요한 리렌더링을 방지하기 위해 `React.memo`, `useMemo`, `useCallback` 등을 적절히 사용한다.
- 컴포넌트의 Props 타입은 항상 TypeScript의 `interface` 또는 `type`으로 명시한다.
- 상태 관리는 전역 상태와 지역 상태를 명확하게 구분하여 사용한다.

## 아키텍처 규칙 (Feature-Based Architecture)
- 기능(Feature) 단위로 디렉터리를 분리하여 응집도를 높인다.
- 각 Feature 내부에 해당 기능과 관련된 `components`, `hooks`, `utils`, `api`, `types` 등을 모아둔다.
- 도메인 로직과 UI 로직을 분리한다.

## 컴포넌트와 스타일의 분리
- 컴포넌트 코드(JSX)와 스타일 코드(StyleSheet)는 철저히 분리한다.
- 컴포넌트 파일의 길이를 줄이고 가독성을 높이기 위해 스타일은 별도의 파일(예: `styles.ts`)로 분리하거나 파일 하단에 선언한다.
- 인라인 스타일(`style={{...}}`)의 사용을 지양하고, 재사용 가능한 스타일은 공유 스타일로 관리한다.
