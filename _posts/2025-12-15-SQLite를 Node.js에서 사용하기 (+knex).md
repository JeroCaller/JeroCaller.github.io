---
title: "SQLite를 Node.js에서 사용하기 (+ Knex)"
category: "SQL"
tag: ["SQLite", "Node.js", "Knex", "DBMS", "RDBMS", "ORM", "Query Builder", "Embedded", "Embedded DBMS", "SQLite3"]
---

# SQLite 개요

[SQLite](https://sqlite.org/index.html)는 응용 프로그램에 넣어 사용할 수 있는 embedded RDBMS이다. MySQL, MariaDB처럼 일반적인 클라이언트 - 서버 DBMS와는 달리, SQLite는 단 하나의 파일을 생성하여 이를 데이터베이스로 활용한다. 

SQLite의 특징

- SQLite 라이브러리가 C 언어로 작성되었으며, 그 크기가 작아 가볍게 사용할 수 있다. SQLite는 주로 중소 규모의 데이터셋에 적합하다고 한다.
- 인덱스, 테이블, 뷰 등의 데이터베이스를 구성하는 모든 요소들을 단 하나의 파일에 저장하여 사용하는 구조이다. 따라서 SQLite를 이용하여 데이터베이스에 접근하는 방식이 파일 읽기 및 쓰기 작업과 동일하다고 볼 수 있다.
    - 파일 형식이기에 플랫폼에 상관없이 데이터베이스 파일을 다른 기기로 전송하기에 편하다.
    - 파일 기반이기에 백업도 해당 파일만 복사 및 붙여넣기하면 되는 매우 간단한 구조이다.
    - 반면 파일 기반이기에 동시적 접근(concurrency)이 힘들다.
    - 클라이언트 - 서버 DBMS와 달리 서버 프로세스가 존재하지 않기에 “Serverless 특징을 가진다”라고도 표현한다.
    - 심지어는 일반적인 파일 입출력보다도 더 빠른 읽기 및 쓰기 작업이 가능하다고 한다.
- 설치 과정 자체가 없으며, 필요한 설정도 없어 빠르게 사용 가능하다.
    - MariaDB와 같은 일반적인 클라이언트 - 서버 DBMS에서는 데이터베이스에 접근하기 위한 사용자 등록, 사용자 접근 권한 부여 등의 작업이 필요하고, 따라서 DCL(Data Control Language)이 존재하는 반면, SQLite에는 이러한 개념 자체가 없어 데이터베이스 접근에 대한 별도의 설정을 하지 않아도 된다.
    - 다만 이는 편리성 및 가벼운 DB라는 이점을 가져오지만, 누구나 데이터베이스에 접근 가능하기에 보안적으로 취약할 수 있다는 단점도 있다. 이러한 취약점은 SQLCipher와 같은 별도의 외부 암호화 확장 기능 같은 것을 사용하여 보완해야 한다.
- 안정성이 매우 높아 사소한 버그만으로도 큰 위험을 초래할 수 있는 분야에서도 안정적으로 사용 가능하다. 실제로 SQLite는 이러한 안정성을 극도로 추구하는 항공기에서도 사용되는 데이터베이스라고 한다. 100%의 branch 코드 커버리지를 자랑한다.
- Public Domain에 포함되어 있어 복사, 수정, 출판, 사용, 컴파일, SQLite의 소스 코드 배포 및 상업적, 비상업적 용도로 자유롭게 사용 가능하다.
- 안드로이드, IOS, macOS 등에 기본적으로 포함되어 있으며, 각종 기기에도 포함되어 있어 전세계적으로 많이 사용되는 임베디드 데이터베이스이다.

SQLite에서는 표준 SQL을 지원하며, 다른 DBMS에서 사용 가능하던 문법 대부분을 거의 그대로 사용 가능하다고 한다. 다만 세부적인 부분에서 여럿 차이점도 존재한다. 

- 유연한 타이핑(Flexible typing) - 여타 다른 DBMS에서는 한 컬럼에 지정된 데이터 타입에 맞는 데이터를 삽입해야하며, 전혀 엉뚱한 타입의 데이터 삽입 시 에러가 발생하지만, SQLite에서는 에러가 나지 않고 해당 데이터가 그대로 저장된다. 예를 들어 어떤 컬럼의 데이터 타입을 INTEGER로 지정하였을 때, 다른 DBMS에서는 “123”라는 문자열을 컬럼에 대입하려고 하면 암묵적으로 INTEGER 형으로 변환 시도를 하여 저장한다. 만약 “abc”처럼 숫자로 변환 불가능한 문자열을 대입하면 에러가 발생한다. 그러나 SQLite에서는 INTEGER라 지정한 컬럼에 “abc”와 같은 문자열을 그대로 저장하는 것이 가능하다. SQLite 공식에 따르면 이건 버그가 아니라 SQLite의 고유 기능 중 하나라고 한다.
    - 컬럼에 데이터 타입이 별도로 지정되지 않은 경우, 입력된 데이터에 대한 유형을 “추천”하는데, 이를 Type Affinity라고 한다. 즉, SQLite에서는 데이터 타입을 “강제”하지 않고 “추천”만 한다.
- 단 5가지의 데이터 타입만이 존재한다. NULL, INTEGER(0 포함 양의 정수), REAL(부동 소수점, 실수), TEXT(UTF-8 인코딩의 문자열), BLOB(이미지, 비디오와 같은 이진 데이터) 이 다섯 가지만을 지원한다고 한다.
    - 그럼에도 INT, INTEGER, TINYINT 등의 유형은 모두 INTEGER로 자동 변환된다. 따라서 다른 DBMS에서 SQLite로 문제 없이 마이그레이션할 수 있다. 자세한 유형 변환은 “[https://sqlite.org/datatype3.html](https://sqlite.org/datatype3.html)” 페이지의 “3.1.1. Affinity Name Examples” 부분을 참고.
- 별도의 boolean 타입이 없어 0과 1로 저장해야 한다. 다만 TRUE, FALSE 키워드를 인식할 수 있어 각각 1과 0으로 저장하도록 할 수 있다.
- 별도의 날짜 및 시각 타입이 없어 다음의 방식들을 고려해야한다.
    - “YYYY-MM-DD HH:mm:SS” 형식의 ISO-8601 포맷을 이용하여 날짜 정보를 TEXT로 저장한다.
    - 1970년부터 지금까지 흐른 초 단위 시간인 unix time을 INTEGER 형태로 저장한다.
    - Julian day 숫자를 REAL 타입으로 저장한다.
    - 이러한 이유로 인해 날짜 및 시각 데이터는 애플리케이션 단에서 잘 가공하여 데이터베이스에 저장 또는 추출하여 사용해야할 것이다.
- `INTEGER PRIMARY KEY` 컬럼에서는 `AUTO INCREMENT` 키워드를 별도로 지정해주지 않아도 자동으로 값을 증가시켜준다.

한 편, MariaDB와 같은 DBMS에서 데이터베이스 내부를 쉽게 보기 위해 HeidiSQL과 같은 클라이언트 GUI 프로그램을 사용했다면, SQLite에서도 이를 위한 GUI 제품이 여러 존재한다. 필자는 개인적으로 “[SQLiteStudio](https://sqlitestudio.pl/)”를 사용하고 있다. 

# Node.js에서 SQLite 사용해보기

<p class="notice--info">
💡

여기서 보일 코드들의 전체 소스 코드는 <a href="https://github.com/JeroCaller/HTML-CSS-JS-study/tree/main/nodejs/sqlite-study">https://github.com/JeroCaller/HTML-CSS-JS-study/tree/main/nodejs/sqlite-study</a>를 참고.
</p>

Node.js에서 SQLite를 사용해보겠다. 먼저 프로젝트 폴더를 만들어 그 안에서 `npm init -y` 를 입력하여 `package.json` 파일이 생성되도록 하였다. 필자는 ESM 모듈 방식을 사용할 것이므로 해당 파일 안에 `"type": "module"` 속성을 추가하였다. 

그 후, sqlite 외부 의존성을 다음과 같이 설치하였다.

```powershell
npm i sqlite3
```

코드 1-1.

그 후 프로젝트 폴더에 `index.js` 라는 파일을 만들었다. SQLite 데이터베이스 파일에 접근하는 코드를 다음과 같이 적는다.

```jsx
import sqlite3 from "sqlite3";

const db = new sqlite3.Database('test.db');
```

코드 1-2. `index.js`

`Database` 생성자 인자에는 데이터베이스가 될 파일명을 입력한다. 여기서는 `test.db` 라는 이름의 SQLite 데이터베이스 파일에 연결하려고 하였다. 만약 해당 파일이 존재하지 않는다면 해당 이름을 가지는 새 파일을 생성한 후 연결한다. 만약 이미 존재하는 파일이라면 해당 파일에 연결한다. 

만약 파일 형태가 아닌 인메모리(in memory) 방식으로 사용하고자 한다면 `Database` 생성자 인수에 `":memory:"` 라고 넣으면 된다. 

터미널 창에서 `node index.js` 를 입력하여 해당 자바스크립트 모듈을 실행해보면 프로젝트 폴더에 `test.db` 라는 파일이 새로 생성될 것이다. 

이번에는 해당 데이터베이스 내부에 새로운 테이블을 만들어보겠다. `db.run()` 또는 `db.exec()` 메서드 내부에 `CREATE TABLE ...` 구문을 문자열 형태로 그대로 넣어 호출하면 된다. 다만, `db` 객체에는 간혹 SQL을 비동기적으로 실행하는 함수들이 더러 있기 때문에, 여러 SQL문들을 순차적으로 실행하는 것을 보장하고자 하기 위해 `Promise` 객체로 감싸 이를 반환하는 wrapper 함수를 다음과 같이 작성하였다.

```jsx
const execute = async (sql) => {
  return new Promise((resolve, reject) => {
    db.exec(sql, (error) => {
      if (error) {
        reject(error);
      }

      resolve();
    });
  });
}

const main = async () => {
  const createTableSql = `CREATE TABLE IF NOT EXISTS products (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    price NUMERIC NOT NULL
  )`;

  try {
    await execute(createTableSql);
  } catch (error) {
    console.log('SQL 실행 도중 에러 발생');
    console.log(error);
  } finally {
    db.close();
  }
};

main();
```

코드 1-3. `index.js`

위 스크립트를 실행 후, SQLiteStudio와 같은 GUI로 확인해보면 `products` 라는 테이블이 새로 생성된 것을 확인할 수 있다. 만약 이를 자바스크립트로 확인하고자 한다면 다음의 코드를 추가하여 확인할 수 있겠다.

```jsx
const getTableSchema = async (table) => {
  const sql = `SELECT * FROM sqlite_schema WHERE name = ?`;

  return new Promise((resolve, reject) => {
    db.get(sql, table, (error, row) => {
      if (error) {
        reject(error);
      }

      resolve(row);
    });
  });
};

const main = async () => {
  try {
    // 생략...
    const result = await getTableSchema('products');
    console.log(result);
  } // 생략...
};

main();
```

코드 1-4. 

```jsx
{
  type: 'table',
  name: 'products',
  tbl_name: 'products',
  rootpage: 2,
  sql: 'CREATE TABLE products (\n' +
    '    id INTEGER PRIMARY KEY,\n' +
    '    name TEXT NOT NULL,\n' +
    '    price NUMERIC NOT NULL\n' +
    '  )'
}
```

코드 1-4 콘솔 실행 결과

SQLite에서는 기본적으로 `sqlite_schema` 라는 테이블이 존재하여 지금까지 데이터베이스에 저장된 모든 테이블, 뷰 등의 요소들의 정보들이 기록되어 있다. 따라서 이를 이용하면 특정 테이블의 CREATE 정보 등을 확인할 수 있는 것이다. 위처럼 결과가 나왔다는 것은 해당 테이블이 잘 생성되었음을 의미한다. 

`insert.js` 라는 별도의 모듈을 만들어 다음과 같이 앞서 생성한 테이블 내에 하나의 데이터를 삽입하는 기능을 구현해보았다.

```jsx
import sqlite3 from "sqlite3";

const db = new sqlite3.Database('test.db');

const executeSql = async (sql, ...params) => {
  if (params && params.length > 0) {
    return new Promise((resolve, reject) => {
      db.run(sql, params, (error) => {
        if (error) {
          reject(error);
        }

        resolve();
      });
    });
  }

  return new Promise((resolve, reject) => {
    db.run(sql, (error) => {
      if (error) {
        reject(error);
      }

      resolve();
    });
  });
};

const main = async () => {
  const insertSql = `INSERT INTO products(name, price) VALUES(?, ?)`;
  try {
    await executeSql(insertSql, "양상추", 4900.1);
  } catch (error) {
    console.log('SQL 실행 도중 에러 발생');
    console.log(error);
  } finally {
    db.close();
  }
};

main();

```

코드 1-5. `insert.js`

SQL injection 보안 위험을 방지하기 위해 문자열 SQL 쿼리문에 실제 값 대신 `?` 와 같은 파라미터를 사용하는데, 실제 값을 대입할 때에는 `db.run()` 메서드의 2번째 인자인 `param` 에 직접 대입하는 방식을 취하여 해당 쿼리문을 실행하게 된다. 위 코드에서 `executeSql` 함수에서는 SQL에 파라미터가 있을 때와 없을 때 모두를 처리할 수 있도록 작성하였다. 위 스크립트를 실행시키면 앞서 생성한 `products` 테이블에 데이터 한 건이 저장된다. 

이와 같은 방식으로 UPDATE, DELETE 작업도 똑같이 구현할 수 있다. 그저 SQL문만 바꾸면 되기 때문이다. DML 작업은 `db.run()` 하나로 모두 실행 가능하다고 보면 되겠다. 

한 편, 단 건이 아닌 여러 건에 대한 데이터 작업을 반복 실행하고자 하는 경우, `db.prepare()` 문을 이용하여 쿼리문을 미리 컴파일하여 실행 준비시킬 수 있다. 이를 이용하면 매번 똑같은 형태의 SQL문을 반복 실행할 때마다 해당 SQL문을 프로그램이 일일히 해석하도록 하지 않아도 되어서 성능 향상을 꾀할 수 있다. 다음은 이를 이용하여 여러 건의 데이터를 삽입하는 코드이다.

```jsx
import sqlite3 from "sqlite3";

const db = new sqlite3.Database('test.db');

const executeInsertMany = async (sql, data) => {
  return new Promise((resolve, reject) => {
    db.serialize(() => {
      const stmt = db.prepare(sql, (error) => {
        if (error) {
          reject(error);
        }
      });
  
      for (let oneData of data) {
        stmt.run(...oneData, (error) => {
          if (error) {
            reject(error);
          }
        });
      }
  
      stmt.finalize((error) => {
        if (error) {
          reject(error);
        }
      });
  
      resolve();
    });
  });
};

const executeSelectAll = async (sql) => {
  return new Promise((resolve, reject) => {
    db.each(sql, (error, row) => {
      if (error) {
        reject(error);
      }

      console.log(row);
      resolve();
    });
  });
};

const main = async () => {
  const insertSql = `INSERT INTO products(name, price) VALUES (?, ?)`;
  const insertData = [
    ["라면", 2000],
    ["생수", 1000],
    ["밥", 3000],
  ];

  const selectAllSql = `SELECT * FROM products`;

  try {
    await executeInsertMany(insertSql, insertData);
    await executeSelectAll(selectAllSql);
  } catch (error) {
    console.log("SQL 작업 실행 도중 에러 발생");
    console.log(error);
  } finally {
    db.close();
  }
}

main();

```

코드 1-6. `insertMany.js`

위 코드의 `executeInsertMany` 함수 내부를 보면, 먼저 `db.prepare(sql)` 를 호출하여 SQL문을 미리 컴파일, 준비시킨다. 이 API 호출로 나온 `Statement` 객체의 `run()` 메서드를 for문 내에서 각 데이터마다 호출하게끔 하였다. 이 과정을 통해 여러 데이터가 한 건씩 입력될 것이다. 이 `Statement` 를 모두 사용하였다면 `finalize()` 함수를 호출하여 종료시킨다. 

여러 쿼리문들이 동시에 실행되도록 할 수 있어 만약 쿼리문들 간의 순서가 중요할 경우, 순서대로 한 줄씩 실행되는 것을 보장하기 위해 위에서 언급한 모든 과정을 `db.serialize()` 함수의 콜백 함수로 감싸 호출하였다. 

한 편, 위 코드의 `executeSelectAll` 함수에서는 SELECT의 여러 건의 결과 데이터를 추출하는 방법 중 하나인 `db.each()` 문을 볼 수 있다. 여러 건의 SELECT 결과 데이터를 가져오는 `db.all()` 과의 차이점은, `db.each()` 는 한 번에 한 행씩 순회하여 콜백 함수를 이용하여 각 행들을 바로 처리할 때 사용할 수 있다. `db.all()` 은 모든 결과 데이터를 한꺼번에 받아오기 때문에 상대적으로 메모리를 많이 차지한다. 

이와 같이 SELECT 결과 데이터를 가져오는 방법으로 `db.each()` 와 `db.all()` 이 있는데, 다음 예제에서는 `db.all()` 과 `db.get()` 을 이용하여 SELECT 결과 데이터를 출력한다. 

```jsx
import sqlite3 from "sqlite3";

const db = new sqlite3.Database('test.db');

const getAll = async (sql) => {
  return new Promise((resolve, reject) => {
    db.all(sql, (error, rows) => {
      if (error) {
        reject(error);
      }

      resolve(rows);
    });
  })
};

const getFirst = async (sql) => {
  return new Promise((resolve, reject) => {
    db.get(sql, (error, row) => {
      if (error) {
        reject(error);
      }

      resolve(row);
    })
  });
};

const main = async () => {
  const selectAllSql = `SELECT * FROM products`;

  try {
    const rows = await getAll(selectAllSql);

    for (let row of rows) {
      console.log(row);
    }

    console.log("======");

    const oneRow = await getFirst(selectAllSql);
    console.log(oneRow);
  } catch (error) {
    console.log(`SQL 작업 중 에러 발생`);
    console.log(error);
  } finally {
    db.close();
  }
}

main();

```

코드 1-7. `select.js`

`db.get()` 함수는 SELECT 결과 데이터 한 건을 반환하는 함수이다. 만약 결과 데이터가 여러 개이더라도 맨 첫 번째 데이터만을 반환한다. 

# 번외: Node.js에서 사용 가능한 ORM 및 Query builder 간단한 소개와 knex 사용해보기

애플리케이션 계층에서 데이터베이스 작업을 객체 지향적으로 하기 위해 객체와 데이터베이스를 매핑하는 기술인 ORM(Object-Relational Mapping)을 사용할 수 있다. 이는 Node.js 진영에서도 마찬가지이다. Node.js 진영에서는 Sequalize, TypeORM, Prisma 등의 라이브러리들이 있다. 

ORM은 데이터베이스 작업을 객체지향적으로 하기 쉽게 만들어주는 기술이지만, 그만큼 학습 곡선이 존재하여 배우는데에 만만치 않다. SQL과는 또 다른, 아예 새로운 기술을 배운다고 봐도 될 것이다. 

사실 애플리케이션과 데이터베이스 연동을 위해 꼭 ORM을 반드시 사용해야만 하는 것은 아니다. 앞서 보았듯, SQL문을 그대로 사용하여 데이터베이스와 소통하는 것도 가능하다. 다만 앞서 본 방식은 SQL문을 오로지 문자열 방식으로만 작성하는 방식이라 SQL문의 문법 오류를 런타임에 가서야 발견할 수 있고, 안정적인 쿼리문 작성이 쉽지 않다는 문제가 있다. 이렇듯 문자열 형태의 SQL문 사용 방식을 회피하면서도 ORM의 특유의 높은 학습 곡선도 피할 수 있는 대안으로 Query Builder가 있다. 메서드 체이닝 방식을 이용하여 SQL 키워드에 대응되는 메서드들을 호출하여 간접적으로 쿼리문을 형성시키는 방식이다. Spring 진영의 Mybatis를 떠올릴 수 있겠다. Node.js 진영에서는 Query Builder 라이브러리로 [knex.js](https://knexjs.org/)가 있다. 이 글에서는 knex를 사용하여 Node.js 환경에서 SQLite를 다뤄보는 방법에 대해 간단하게 살펴보도록 하겠다. 

<p class="notice--info">
💡

다음에 보일 코드들의 전체 소스 코드는 <a href="https://github.com/JeroCaller/HTML-CSS-JS-study/tree/main/nodejs/sqlite-with-knex">https://github.com/JeroCaller/HTML-CSS-JS-study/tree/main/nodejs/sqlite-with-knex</a> 여기를 참고.
</p>

이미 sqlite3 의존성을 설치한 상황이라면, 다음의 명령어로 knex 의존성만 추가하면 된다. 

```powershell
npm i knex --save
```

코드 2-1.

그리고 자바스크립트에서 다음과 같이 import 하여 사용하면 되겠다.

```jsx
import knex from "knex";
```

코드 2-2.

knex는 맨 처음에 설정이 필요하다. sqlite3 사용 시 다음과 같이 설정 가능하다.

```jsx
const kn = knex({
  client: 'sqlite3',
  connection: {
    filename: 'knex-study.db'
  },
  useNullAsDefault: true
});
```

코드 2-3.

`client` 부분에는 어떤 DBMS를 사용하는지 명시한다. `connection` 에서는 `filename` 을 통해 연결하고자 하는 DB 파일명을 적는다. 여기서도 마찬가지로 해당 파일이 없다면 자동으로 새로 생성한 뒤 자동으로 연결되며, 이미 존재한다면 해당 파일에 연결된다. 

위와 같이 설정한 `knex` 객체를 이용하여 본격적으로 SQL문을 간접적으로 빌드하면 된다. 

테이블, 뷰 생성 등 DDL 관련 작업은 `knex.schema` 라는 getter 함수에 접근하여 할 수 있다. 예를 들어 다음과 같이 특정 테이블을 새로 하나 생성할 수 있다. 

```jsx
await kn.schema.createTableIfNotExists('users', (table) => {
  table.increments('id', {primaryKey: true});
  table.string('name').notNullable();
  table.integer('age');
  table.string('job');
});

/*
위 구문은 SQL문으로 다음과 같음.

CREATE TABLE IF NOT EXISTS users (
	id INT PRIMARY KEY AUTO_INCREMENT,
	name VARCHAR(...) NOT NULL,
	age INT,
	job VARCHAR(...)
);
*/
```

코드 2-4. 

전체 또는 특정 조건을 만족하는 데이터 조회 쿼리문은 다음과 같이 생성할 수 있다. 

```jsx
await kn.select().from('sqlite_schema')
  .where('tbl_name', 'users')  // WHERE tbl_name == 'users'
  .then((results) => {
      console.log("생성한 테이블 스키마 구조 확인");
      console.log(results);
  });

// 전체 데이터 조회
await kn.select().from('users')
  .then((results) => {
      console.log("테이블 데이터 확인");
        
      if (!results || results.length == 0) {
        console.log("조회된 데이터 없음");
        return;
      }
        
      for (let result of results) {
        console.log(result);
      }
  });
```

코드 2-5. 

SQLite DB 파일을 하나 생성한 후, 그 안에 특정 테이블을 생성한 뒤 그 안에 여러 데이터를 삽입, 조회, 삭제해보는 예제 코드를 다음과 같이 작성해보았다. 

```jsx
import knex from "knex";

const kn = knex({
  client: 'sqlite3',
  connection: {
    filename: 'knex-study.db'
  },
  useNullAsDefault: true
});

const main = async () => {

  try {
    await kn.schema.createTableIfNotExists('users', (table) => {
      table.increments('id', {primaryKey: true});
      table.string('name').notNullable();
      table.integer('age');
      table.string('job');
    });

    await kn.select().from('sqlite_schema')
      .where('tbl_name', 'users')
      .then((results) => {
        console.log("생성한 테이블 스키마 구조 확인");
        console.log(results);
      });

    await kn.insert([
      {name: '자바스', age: 23, job: '정원관리사'},
      {name: '정디비', age: 31, job: '프로그래머'},
      {name: '김큐엘', age: 25}
    ]).into('users');

    await kn.select().from('users')
      .then((results) => {
        console.log("테이블 데이터 확인");
        
        for (let result of results) {
          console.log(result);
        }
      });

    await kn.delete().from('users')
      .then((results) => {
        console.log('전체 데이터 삭제 완료');
      });

    await kn.select().from('users')
      .then((results) => {
        console.log("테이블 데이터 확인");
        
        if (!results || results.length == 0) {
          console.log("조회된 데이터 없음");
          return;
        }
        
        for (let result of results) {
          console.log(result);
        }
      });
  } catch (error) {
    console.log('SQL 작업 중 에러 발생');
    console.log(error);
  } finally {
    try {
      kn.destroy();
    } catch (error) {
      console.log('knex 닫는 도중 에러 발생');
      console.log(error);
    }
  }
};

main();

```

코드 2-6. `index.js` 

```jsx
생성한 테이블 스키마 구조 확인
[
  {
    type: 'table',
    name: 'users',
    tbl_name: 'users',
    rootpage: 2,
    sql: 'CREATE TABLE `users` (`id` integer not null primary key autoincrement, `name` varchar(255) not null, `age` integer, `job` varchar(255))'
  }
]
테이블 데이터 확인
{ id: 10, name: '자바스', age: 23, job: '정원관리사' }
{ id: 11, name: '정디비', age: 31, job: '프로그래머' }
{ id: 12, name: '김큐엘', age: 25, job: null }
전체 데이터 삭제 완료
테이블 데이터 확인
조회된 데이터 없음
```

코드 2-6 콘솔 실행 결과

데이터베이스 작업을 완료하였다면 `knex.destroy()` 를 호출하여 연결을 종료시킬 수 있다. 

knex에 대한 자세한 사용 방법은 Knex의 [Guide](https://knexjs.org/guide/) 를 참고하면 되겠다. 

---

References

[1] sqlite 공식 홈페이지

[SQLite Home Page](https://sqlite.org/)

[2] [SQLite](https://ko.wikipedia.org/wiki/SQLite)

[3] 나무위키 - SQLite

[https://namu.wiki/w/SQLite](https://namu.wiki/w/SQLite)

[4] sqlite nodejs 공식 docs

[SQLite \| Node.js v25.2.1 Documentation](https://nodejs.org/api/sqlite.html)

[5] knexjs - Query builder library

[SQL Query Builder for Javascript \| Knex.js](https://knexjs.org/)

[6] sqlite를 nodejs에서 사용하는 방법

[SQLite NodeJS 모듈 이용해서 CRUD 구현하기 # DBBrowser SQLite3](https://developer88.tistory.com/entry/SQLite-NodeJS%EC%97%90%EC%84%9C-%EA%B5%AC%ED%98%84%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-%EC%A0%95%EB%A6%AC-SQLite3)

[7] sqlite GUI

[SQLiteStudio](https://sqlitestudio.pl/)

[8] [SQLite Node.js Tutorial](https://www.sqlitetutorial.net/sqlite-nodejs/)

[9] knex ESM 사용법 및 with typescript

[[Typescript] Knex.js로 MySQL/MariaDB 연동하기](https://blog.betaman.kr/84)

[10] sqlite 테이블 구조 확인

[SQLite 테이블 구조(스키마) 확인 2가지 방법(desc tablename) - 오솔길](https://osg.kr/archives/1259)

[11] [node.js ORM, 무엇을 써야 할까? (Sequelize, TypeORM, Prisma, Knex)](https://velog.io/@elon/node.js-ORM-%EB%AC%B4%EC%97%87%EC%9D%84-%EC%8D%A8%EC%95%BC-%ED%95%A0%EA%B9%8C-Sequelize-TypeORM-Prisma-Knex)

[12] [왜 Nodejs ORM을 쓰지 말아야 할까](https://yceffort.kr/2021/07/dont-use-nodjs-orm)