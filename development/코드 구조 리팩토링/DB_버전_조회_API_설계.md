# 일의 순서

1. wip-serverless 작업
2. GCP Cloud Run 생성
3. wip-client 작업
4. expo에 env 값 등록

# API 스펙

#### request

- /:schemaVersion (URL param)

#### response

```ts
{
  success: boolean,
  schemaVersion: number,
  dataVersion: number,
}
```

# 로직

1. `config.json` 파일을 읽어 그대로 반환

# 클라이언트 로직

1. `schemaVersion` 및 `resourceVersion`을 config 테이블에 업데이트
