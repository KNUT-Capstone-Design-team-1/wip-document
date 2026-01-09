# 일의 순서

1. wip-serverless 작업
2. GCP Cloud Run 생성
3. wip-client 작업
4. expo에 env 값 등록
5. wip-resource-deployer 작업

- json 파일만 생성하도록 변경 (realm 관련 로직 제거)

# API 스펙

#### request

- `?page=1`

#### response

```ts
{
  success: boolean,
  resourceVersion: string,
  datas: [
    ...
  ],
  total: number,
}
```

# 로직

1. `data.json`으로 부터 1만개씩 페이징하여 응답

# 클라이언트 로직

1. 첫 total 개수 만큼 반복문 실행 (기본 10,000)
2. row 1000개씩 INSERT

- SQL Injection을 예방할 수 있도록 각 값에서 특수문자 제거
