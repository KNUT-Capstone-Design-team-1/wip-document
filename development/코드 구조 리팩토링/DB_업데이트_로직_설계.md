# DB 버전 조회 로직

1. config 테이블 내 `schemaVersion` 및 `resourceVersion` 조회

2. DB 버전 조회 API 호출

3. `schemaVersion` 혹은 `resourceVersion`이 낮으면 DROP 테이블 수행

4. 테이블 스키마 조회 API 호출

5. CREATE 테이블 수행

- SQL Injection을 예방할 수 있도록 `columns`의 각 값에서 특수문자 제거

```ts
const getReplacedString = (str: string) => str?.replace(/[^a-zA-Z0-9]/g, "");

const columnPlaceHolder = columns
  .map((v) => {
    const name = getReplacedString(v.name);
    const type = `${getReplacedString(v.type)} ${v.size ? `(${v.size})` : ``}`;
    const nullable = v.nullable ? "NULL" : "NOT NULL";

    let defaultValue = defaultValue
      ? `"${getReplacedString(v.defaultValue)}"`
      : "";

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

6. 원천 데이터 API 호출 및 INSERT 수행

- 페이징으로 나눠서 여러 번 호출 (예상 7번)
- 첫 total 개수 만큼 반복문 실행 (기본 10,000)
- row 1000개씩 INSERT
- SQL Injection을 예방할 수 있도록 각 값에서 특수문자 제거
