## 파이썬에서 SQLite 사용 

```
데이터베이스(DB)에는 Oracle, MySQL, PostgresSQL, MSSQL등의 많은 제품들이 있다. 이 데이터베이스들에 대해서 어느정도 사용법을 숙지하려면 꽤나 오랜 시간과 경험이 필요하다.

아쉽지만 이 책으로 여러분에게 그런 경험을 전달해 줄 수는 없다.

여기서는 가장 간단한 SQLite("에스큐엘라이트" 라고 읽는다.)라는 데이터베이스에 대해서만 알아볼 것이다. 그렇다고 너무 실망하지는 말자. SQLite의 사용법을 숙지하게 되면 위에서 언급한 데이터베이스들에도 쉽게 적응할 수 있게 될 것이다.

SQLite는 주로 개발용이나 소규모 프로젝트에서 사용되는 아주아주 가벼운 파일기반의 DB이다. 개발시에는 SQLite를 사용하여 빠르게 개발하고 실제 운영시스템은 좀 더 규모있는 DB를 사용하는 것이 일반적인 개발 패턴이다. (참고. 파이썬은 DB를 핸들링하는 범용적인 API를 제공해 주기 때문에 이렇게 개발시와 운영시에 다른 DB를 사용하여 개발하는것이 가능하다.)

자, 이제 SQLite를 사용해 보자. 한가지 기쁜 사실은 파이썬 설치시 이미 SQLite도 함께 설치된다는 점이다. SQLite를 사용하기 위해 여러분이 추가적으로 진행해야 할 일은 없다.
```

1. 데이터베이스 접속하기 및 파일 생성
```
import sqlite3
conn = sqlite3.connect('jump2python.db')
```
- sqlite3  모듈의 connect 메서드를 이용하면 데이터베이스 핸들링할 수 있는 conn 객체를 얻을수 있다. 이때 입력으로 사용한 'jump2python.db'는 데이터를 저장할 데이터베이스 파일이 된다.

2. 테이블 생성하기
- 디비 데이터를 저정하는 공간인 테이블을 생성한다.
```
# 커서 생성
c = conn.cursor()

# 테이블 생성
c.execute('''CREATE TABLE book
             (id integer PRIMARY KEY, author text, subject text, page integer)''')
```
- DB 테이블을 생성하기 위해서는 위 처럼 CREATE TABLE 테이블명 (...) 과 같은 DB 질의문(쿼리문)을 실행해야 한다. 이런 쿼리문을 실행하기 위해서는 커서 객체(Cursor object)가 먼저 필요하다. 커서 객체는 위처럼 conn객체를 통해 얻을 수 있다.

쿼리문은 커서 객체의 execute 메서드를 호출하여 실행할 수 있다.

위 쿼리문을 실행하면 다음과 같은 구조의 book 테이블이 생성 된다.
```
컬럼명	컬럼타입	설명
id	integer	고유번호 (PRIMARY KEY)
author	text	지은이
subject	text	책제목
page	integer	쪽수
```
- book테이블에는 위처럼 총 4개의 컬럼이 생성된다. 컬럼타입은 컬럼에 저장되는 데이터의 성격을 의미한다. text는 문자열 integer는 숫자형 자료가 저장될 것이다. 특별하게 id 컬럼에는 PRIMARY KEY("프라이머리 키"라고 읽는다.)라고 지정이 되었는데 이 컬럼에는 동일한 값을 입력할 수 없다는 의미이다. 예를들어 id에 1이라는 값이 입력된 데이터가 있다면 id에 1이라는 값을 한번 더 저장할 수 없다는 말이다. 만약 동일한 값을 입력하려고 시도한다면 sqlite는 다음과 같은 오류를 발생시킬 것이다.
```
sqlite3.IntegrityError: UNIQUE constraint failed: book.id

# (참고. 컬럼타입은 데이터베이스별로 서로 다르다. 예를 들어 오라클의 문자열은 text가 아닌 varchar형식의 컬럼타입을 사용한다. 그리고 sqlite는 간편하게 컬럼의 길이를 따로 지정하지 않는데 대부분의 데이터베이스들은 컬럼의 길이도 함께 지정해 주어야 한다.)
```

3. 데이터 입력하기
```
# 데이터 입력
c.execute("INSERT INTO book VALUES (1, '박응용', '점프 투 파이썬', 300)")
```
- 데이터 입력 역시 커서 객체의 execute메서드를 이용하여 실행. 데이터 입력은 INSERT INTO 테이블명(book) VALUES (...) 쿼리문을 이용.
즉, 
```
id	author	subject	page
1	박응용	점프 투 파이썬	300
```

한건더 입력하면,
'c.execute("INSERT INTO book VALUES (2, 'JAYDEN', '점프 2 플라스크', 200)")'


- 데이터 입력시 다음과 같이 변수를 이용하여 값을 전달하여 입력할 수도 있다.
    ```
    _id = 3
    author = "박응용"
    subject = "점프 투 파이썬"
    page = 300
    c.execute("INSERT INTO book VALUES (%d, '%s', '%s', %d)" % (_id, author, subject, page))

    ```
    - 하지만 위처럼 쿼리문을 구성하는 것은 보안상 매우 취약하다. 사용자로부터 입력 받은 값이 쿼리문에 그대로 들어가기 때문에 악의적인 입력값으로 인해 데이터베이스에 위험한 쿼리가 실행될 수 있기 때문이다. (이런것을 SQL Injection 공격이라고 부른다.)

따라서 위의 방식보다는 ? 물음표 스타일의 쿼리문을 작성
```
_id = 4
author = "박응용"
subject = "점프 투 파이썬"
page = 300
c.execute("INSERT INTO book VALUES (?, ?, ?, ?)", (_id, author, subject, page))
```
입력해야 할 부분을 '%s' 대신 물음표(?)로 바꾸어 주고 %연산자로 데이터를 입력하는 대신 튜플 형태의 데이터를 두번째 파라미터로 넘겨야 한다.

- 또는 다음과 같은 딕셔너리 이름 기반 스타일의 퀄리도 가능
```
c.execute("INSERT INTO book VALUES (:id, :author, :subject, :page)",
                 {"id": 5, "author": "박응용", "subject": "점프 투 파이썬", "page": 300})
```

4. 데이터 조회하기
```
c.execute('SELECT * FROM book')
all = c.fetchall()
print(all)
```
- 조회는 `SELECT ... FROM 테이블명 ...`  형식의 쿼리문을 이용. 커서 객체의 fetchall 메서드를 호출하면 조회 결과를 모두 리스트로 반환 한다.
즉, 다음과 같이 출력 된다.
```
[(1, '박응용', '점프 투 파이썬', 300), (2, '홍길동', '점프 투 자바', 200), (3, '박응용', '점프 투 파이썬', 300), (4, '박응용', '점프 투 파이썬', 300), (5, '박응용', '점프 투 파이썬', 300)]
```
    - fetchall은 한번 수행하면 끝이다. 다시 한번 수행한다면 동일한 결과가 리턴되는 것이 아니라 빈 리스트가 출력 된다.

- fetchall 대신 fetchone 을 사용하면 모든 데이터가 아닌, 한개의 데이터 row를 가져온다.
```
c.execute('SELECT * FROM book')
one = c.fetchone()
print(one)
```
    - 한개의 row 가 다음 처럼 튜플형태로 출력
    ```
    (1, '박응용', '점프 투 파이썬', 300)
    ```

5. 데이터 저장과 취소
- 지금까지 데이터의 입력과 조회를 알아보았다. 위에서 언급하지는 않았지만 UPDATE 쿼리문과 DELETE 쿼리문 역시 커서객체의 execute메서드로 수행할 수 있다.

- 이제 입력한 데이터를 저장해 보자. 지금까지 INSERT 쿼리문으로 데이터를 입력했는데 저장하지 않고 파이썬 프로그램을 종료하게 되면 데이터는 사라지게 된다.

- 데이터의 저장은 다음과 같이 컨넨션 객체(Connection object)의 commit 메서드를 호출해야 한다.
 ```
conn.commit()
```
- 만약 데이터 입력을 했는데도 불구하고 데이터가 유실되었다면 프로그램에서 commit 메서드가 정상적으로 호출되었는지 확인해 봐야 한다.

- 이번에는 데이터의 입력을 취소하는 방법에 대해서 알아보자. 취소를 위해서는 다음처럼 컨넥션 객체의 rollback 메서드를 호출해야 한다.
'conn.rollback()'

- rollback 메서드가 호출되면 최종 commit 된 이후의 데이터의 변경사항들이 모두 취소된다. 이미 commit된 데이터들은 rollback을 하더라도 취소되지 않는다.

commit, rollback 구문은 우리가 문서편집기를 열고 데이터를 편집하다가 저장(commit) 또는 Ctrl-Z를 입력하여 기존으로 되돌리기(rollback)를 하는것과 매우 비슷하다.

(참고. INSERT외에 DELETE, UPDATE 등으로 변경된 데이터들도 commit, rollback이 동일하게 동작한다.)

6. 데이터베이스 접속 종료
- 접속 종료는 close()
```
conn.close()
```
- 단, close는 자동으로 commit을 수행하지 않기 때문에 commit 없이 close 될 경우 변경된 내용들이 모두 사라지게 된다는 점에 주의해야 한다.





