# 02-03. QueryDSL과 SQL Injection

### Q. SQL Injection 공격의 대표적인 패턴에는 어떤 것들이 있나요?

첫 번째는 `Tautology Attack (항상 참이 되는 조건 주입)`입니다. 이는 공격자가 SQL 쿼리에 `OR '1'='1'`과 같이 **항상 참이 되는 조건을 삽입하여, 원래 쿼리의 참/거짓 여부와 관계없이 조건문 전체를 참으로 만드는 방식**입니다. 예를 들어, 로그인 쿼리에서 `SELECT * FROM users WHERE username='[입력된_사용자명]' AND password='[입력된_비밀번호]'` 와 같은 형태라면, 공격자가 사용자명 필드에 `'admin' OR '1'='1'`을 주입하여 `SELECT * FROM users WHERE username='admin' OR '1'='1' AND password='[입력된_비밀번호]'` 가 되게 만듭니다. 이렇게 되면 비밀번호가 틀려도 `OR '1'='1'` 때문에 항상 조건문이 참이 되어 로그인에 성공하거나, 모든 사용자 정보를 조회할 수 있게 됩니다.

두 번째는 `Union 기반 공격(Union-based SQL Injection)` 입니다. 이는 공격자가 `UNION SELECT` 문을 사용하여 **현재 쿼리 결과에 다른 테이블의 데이터를 합치는 방식**입니다. 공격자는 먼저 기존 쿼리의 컬럼 개수와 타입을 파악한 후, `UNION SELECT null, null, database(), user()` 와 같은 쿼리를 주입하여 데이터베이스명이나 사용자 정보를 획득할 수 있습니다. 더 나아가 `UNION SELECT username, password FROM admin_users` 와 같이 민감한 정보가 담긴 다른 테이블의 데이터를 직접 조회할 수도 있습니다. 이 공격은 반환되는 데이터가 화면에 직접 출력되는 경우에 특히 유용합니다.

세 번째는 `에러 기반 공격(Error-based SQL Injection)` 입니다. 이 방식은 **SQL 구문 오류를 의도적으로 발생시켜 데이터베이스가 반환하는 에러 메시지를 통해 정보를 얻어내는 공격**입니다. 예를 들어, `SELECT * FROM users WHERE id = '1' AND (SELECT 1 FROM non_existent_table)`과 같이 존재하지 않는 테이블을 참조하여 에러를 유발시키면, 에러 메시지에 데이터베이스의 버전 정보나 특정 함수의 결과값이 포함되어 노출될 수 있습니다. 이를 통해 공격자는 데이터베이스의 구조나 데이터를 조금씩 유추해 나갑니다.

네 번째는 `블라인드 SQL Injection (Blind SQL Injection)` 입니다. 이 공격은 서버가 에러 메시지나 쿼리 결과를 직접 반환하지 않을 때 사용됩니다. 대신, 쿼리 결과에 따라 웹 페이지의 응답이 달라지는 것을 이용하여 정보를 추출합니다. 크게 두 가지 하위 유형이 있습니다.

* **Boolean-based Blind SQL Injection:** 참/거짓 결과에 따라 페이지의 로딩 속도나 내용 변화(예: 특정 텍스트의 유무)를 관찰하여 정보를 유추합니다. `AND SUBSTRING(VERSION(), 1, 1) = '5'`와 같은 조건문을 주입하고, 페이지 반응을 보면서 데이터베이스 버전의 각 문자를 하나씩 알아내는 식입니다.
* **Time-based Blind SQL Injection:** 쿼리의 실행 시간에 의도적으로 지연을 주어 정보를 유추합니다. `AND IF(SUBSTRING(VERSION(), 1, 1) = '5', SLEEP(5), 0)`와 같이 참일 경우 5초 지연을 발생시키는 쿼리를 주입하여, 지연 여부를 통해 조건문의 참/거짓을 판단합니다. 이는 매우 느리지만, 어떤 정보도 반환되지 않는 상황에서도 공격이 가능하게 합니다.



### Q. 만약 QueryDSL을 사용하여 동적 쿼리를 생성하는 로직이 있다면, SQL Injection으로부터 안전하다고 확신할 수 있을까요?

QueryDSL의 가장 큰 장점은 `Prepared Statement`를 기본적으로 활용한다는 점입니다. 이는 SQL Injection을 방어하는 가장 효과적인 방법으로, 사용자 입력값을 쿼리의 일부가 아닌 단순한 데이터로 처리하여 악의적인 SQL 코드가 실행되지 않도록 합니다. 예를 들어, `query.where(user.username.eq(inputUsername))`와 같이 필드와 값을 직접 비교하는 방식은 안전합니다. `inputUsername`에 어떤 문자열이 들어오든, QueryDSL은 이를 문자열 리터럴로 인식하고 SQL의 매개변수 바인딩 메커니즘을 통해 처리하기 때문입니다.

하지만 문제는 개발자가 QueryDSL의 이러한 안전한 메커니즘을 **우회하는 방식**으로 코드를 작성할 때 발생합니다. `Expressions.stringTemplate`이나 `Expressions.constant`를 사용하여 외부 입력값을 쿼리에 직접 문자열로 삽입하거나, 컬럼명, 테이블명 등을 사용자 입력으로 동적으로 받아와 검증 없이 쿼리에 반영하는 경우 SQL Injection에 취약해질 수 있습니다. 이러한 기능들은 유연성을 제공하지만, 동시에 개발자가 보안 취약점을 만들 수 있는 여지를 줍니다.

결론적으로, QueryDSL은 기본적으로 **Prepared Statement**를 통해 SQL Injection 방어에 매우 효과적이지만, 개발자가 외부로부터 들어오는 사용자 입력을 쿼리 구성 요소로만 사용하고, 이를 SQL 구문 자체에 직접 포함시키지 않는다는 전제하에 안전합니다.
