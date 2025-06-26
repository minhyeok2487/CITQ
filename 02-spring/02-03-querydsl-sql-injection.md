# 02-03. QueryDSL과 SQL Injection

### Q. SQL Injection 공격의 대표적인 패턴에는 어떤 것들이 있나요?

첫 번째는 `Tautology Attack (항상 참이 되는 조건 주입)`입니다. 이는 공격자가 SQL 쿼리에 `OR '1'='1'`과 같이 **항상 참이 되는 조건을 삽입하여, 원래 쿼리의 참/거짓 여부와 관계없이 조건문 전체를 참으로 만드는 방식**입니다. 예를 들어, 로그인 쿼리에서 `SELECT * FROM users WHERE username='[입력된_사용자명]' AND password='[입력된_비밀번호]'` 와 같은 형태라면, 공격자가 사용자명 필드에 `'admin' OR '1'='1'`을 주입하여 `SELECT * FROM users WHERE username='admin' OR '1'='1' AND password='[입력된_비밀번호]'` 가 되게 만듭니다. 이렇게 되면 비밀번호가 틀려도 `OR '1'='1'` 때문에 항상 조건문이 참이 되어 로그인에 성공하거나, 모든 사용자 정보를 조회할 수 있게 됩니다.

두 번째는 `Union 기반 공격(Union-based SQL Injection)` 입니다. 이는 공격자가 `UNION SELECT` 문을 사용하여 **현재 쿼리 결과에 다른 테이블의 데이터를 합치는 방식**입니다. 공격자는 먼저 기존 쿼리의 컬럼 개수와 타입을 파악한 후, `UNION SELECT null, null, database(), user()` 와 같은 쿼리를 주입하여 데이터베이스명이나 사용자 정보를 획득할 수 있습니다. 더 나아가 `UNION SELECT username, password FROM admin_users` 와 같이 민감한 정보가 담긴 다른 테이블의 데이터를 직접 조회할 수도 있습니다. 이 공격은 반환되는 데이터가 화면에 직접 출력되는 경우에 특히 유용합니다.

세 번째는 `에러 기반 공격(Error-based SQL Injection)` 입니다. 이 방식은 **SQL 구문 오류를 의도적으로 발생시켜 데이터베이스가 반환하는 에러 메시지를 통해 정보를 얻어내는 공격**입니다. 예를 들어, `SELECT * FROM users WHERE id = '1' AND (SELECT 1 FROM non_existent_table)`과 같이 존재하지 않는 테이블을 참조하여 에러를 유발시키면, 에러 메시지에 데이터베이스의 버전 정보나 특정 함수의 결과값이 포함되어 노출될 수 있습니다. 이를 통해 공격자는 데이터베이스의 구조나 데이터를 조금씩 유추해 나갑니다.

네 번째는 `블라인드 SQL Injection (Blind SQL Injection)` 입니다. 이 공격은 서버가 에러 메시지나 쿼리 결과를 직접 반환하지 않을 때 사용됩니다. 대신, 쿼리 결과에 따라 웹 페이지의 응답이 달라지는 것을 이용하여 정보를 추출합니다. 크게 두 가지 하위 유형이 있습니다.

* **Boolean-based Blind SQL Injection:** 참/거짓 결과에 따라 페이지의 로딩 속도나 내용 변화(예: 특정 텍스트의 유무)를 관찰하여 정보를 유추합니다. `AND SUBSTRING(VERSION(), 1, 1) = '5'`와 같은 조건문을 주입하고, 페이지 반응을 보면서 데이터베이스 버전의 각 문자를 하나씩 알아내는 식입니다.
* **Time-based Blind SQL Injection:** 쿼리의 실행 시간에 의도적으로 지연을 주어 정보를 유추합니다. `AND IF(SUBSTRING(VERSION(), 1, 1) = '5', SLEEP(5), 0)`와 같이 참일 경우 5초 지연을 발생시키는 쿼리를 주입하여, 지연 여부를 통해 조건문의 참/거짓을 판단합니다. 이는 매우 느리지만, 어떤 정보도 반환되지 않는 상황에서도 공격이 가능하게 합니다.
