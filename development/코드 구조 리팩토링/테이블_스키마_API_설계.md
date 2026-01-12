# 일의 순서

1. wip-serverless 작업
2. GCP Cloud Run 생성
3. wip-client 작업
4. expo에 env 값 등록

# API 스펙

#### request

- N/A

#### response

```ts
{
  columns: [
    {
      name: string,
      type: string,
      size: number, // byte
      nullable: boolean,
      defaultValue: string | number | null,
      isPK: boolean,
    }
  ]
}
```

# 로직

1. `schema.json` 파일을 읽어 그대로 반환

- SQL에 바로 쓸 수 있도록 DDL문 형식으로 값을 정의

# 클라이언트 로직

1. DROP TABLE
2. CREATE TABLE 쿼리에 매핑

- SQL Injection을 예방할 수 있도록 `columns`의 각 값에서 특수문자 제거

```ts
const getReplacedString = (str: string) => str?.replace(/[^a-zA-Z0-9]/g, "");

const columnPlaceHolder = columns
  .map((v) => {
    const name = getReplacedString(v.name);
    const type = `${getReplacedString(v.type)} ${v.size ? `(${v.size})` : ``}`;
    const nullable = v.nullable ? "NULL" : "NOT NULL";

    let defaultValue = defaultValue ? `"${getReplacedString(v.defaultValue)}"` : "";

    if (typeof v.defaultValue === "number") {
      defaultValue = v.defaultValue;
    }

    if (v.defaultValue === null) {
      defaultValue = "NULL";
    }

    return `${name} ${type} ${nullable} ${defaultValue}`;
  })
  .join(", ");

const pkPlaceHolder = getReplacedString(columns.find((v) => v.isPK).name);

const sql = `CREATE TABLE pill_data (
             ${columnPlaceHolder},
             PRIMARY KEY (${pkPlaceHolder})
             );`;
```
