# 일의 순서

1. wip-serverless 작업
2. GCP Cloud Run 생성
3. wip-client 작업
4. expo에 env 값 등록

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

1. `resourceVersion`을 config에 업데이트
2. row 1000개씩 INSERT

- SQL Injection을 예방할 수 있도록 각 값에서 특수문자 제거
