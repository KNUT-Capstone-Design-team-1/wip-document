# 추상화 개념

---

# [애플리케이션 (wip-application-v2)](https://github.com/KNUT-Capstone-Design-team-1/wip-application-v2)

## 기능

- 식별 검색
  - 알약의 외형적인 특징을 입력하여 조건이 포함되는 알약을 검색
- 이미지 검색
  - 알약의 이미지를 첨부하여 조건이 포함되는 알약을 검색
- 주변 약국
  - 사용자의 위치 반경 내 약국들을 지도에 표시
- 알약 보관함
  - 검색된 알약을 사용자의 기기에 저장하여 검색 수행 없이 정보를 확인

## 데이터베이스

- 알약 데이터 (PillData)
  - 검색 기능 최적화를 위해 `의약품 낱알 식별 정보 데이터`와 `완제 의약품 허가 상세 데이터`를 병합한 데이터베이스
- 의약품 낱알 식별 정보 데이터 (DrugRecognition)
  - 알약의 외형 정보를 포함한 기본 정보
  - [출처](https://data.mfds.go.kr/OPCAC01F05?srchSrvcKorNm=%EC%9D%98%EC%95%BD%ED%92%88%20%EB%82%B1%EC%95%8C%EC%8B%9D%EB%B3%84%EC%A0%95%EB%B3%B4%20%EB%8D%B0%EC%9D%B4%ED%84%B0)
- 완제 의약품 허가 상세 데이터 (finished medicine permission detail)
  - 알약을 포함한 의약품의 허가 정보
  - [출처](https://data.mfds.go.kr/OPCAC01F05/search?loginCk=false&aplyYn=&taskDivsCd=&srchSrvcKorNm=%EC%99%84%EC%A0%9C+%EC%9D%98%EC%95%BD%ED%92%88+%ED%97%88%EA%B0%80+%EC%83%81%EC%84%B8+%EB%8D%B0%EC%9D%B4%ED%84%B0)
- 알약 보관함 (PillBox)
  - 사용자가 검색한 알약을 저장하는 데이터베이스

# [서버리스 (wip-serverless)](https://github.com/KNUT-Capstone-Design-team-1/wip-serverless)

## 서비스

- initial-info
  - 초기화 정보 확인 서버
  - 데이터베이스 등 업데이트 정보 및 애플리케이션 초기화를 위한 정보 확인
- image-search
  - 이미지 검색 프록시 서버
  - 애플리케이션으로 부터 이미지 정보를 받아 딥러닝 서버로 리버스 프록싱 수행
- wip-deep-learning-server-v2
  - 알약 식별 정보 추출 서버
  - 알약의 이미지로부터 식별 정보를 추출
- drug-detail
  - 알약 상세 정보 프록시 서버
  - [출처](https://www.data.go.kr/data/15095677/openapi.do#/API%20%EB%AA%A9%EB%A1%9D/getDrugPrdtPrmsnDtlInq04)
- wip-resource
  - 알약 정보 데이터베이스 리소스 파일 스토리지
  - 이미지 검색 및 식별 검색을 위한 데이터베이스 파일을 저장
    - 초기 데이터와 업데이트 데이터를 구분

# [리소스 배포도구 (wip-resource-deployer)](https://github.com/KNUT-Capstone-Design-team-1/wip-resource-deployer)

# [테스트 클라이언트 (wip-client)](https://github.com/KNUT-Capstone-Design-team-1/wip-client)
