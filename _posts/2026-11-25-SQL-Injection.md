---
title: "[OWASP Top 10:2025] SQL Injection의 원리와 대응 방안"
date: 2026-11-25 18:00:00 +0900
categories: ["Research / Conferences"]
tags: [OWASP, SQL Injection, Web Security, Database]
---

## 1. 주제 선정 이유

웹사이트에서는 로그인, 회원가입, 검색 등 다양한 기능을 통해 사용자의 입력값을 처리한다. 이때 입력값을 안전하게 처리하지 않으면 보안 취약점이 발생할 수 있다.

OWASP Top 10에 포함된 취약점 중 SQL Injection은 사용자 입력값이 어떻게 보안 문제로 이어지는지 비교적 쉽게 이해할 수 있다는 점에서 선정하였다.

## 2. OWASP Top 10과 보안 동향

OWASP(Open Worldwide Application Security Project)는 소프트웨어 보안 향상을 목적으로 활동하는 비영리 재단이다.

OWASP Top 10은 웹 애플리케이션에서 중요하게 고려해야 할 보안 위험을 10가지 범주로 정리한 문서이다.

SQL Injection은 OWASP Top 10의 Injection 항목에 포함된다.

| 연도   | 순위  | 항목        |
| ---- | --- | --------- |
| 2021 | A03 | Injection |
| 2025 | A05 | Injection |

2021년에 비해 순위는 낮아졌지만, 여전히 주요 보안 위험으로 분류되고 있다. 특히 SQL Injection은 개인정보 유출이나 데이터 변조 등으로 이어질 수 있어 지속적인 관리가 필요한 취약점이다.

## 3. SQL Injection의 배경지식

### 3.1. 데이터베이스와 SQL

데이터베이스(Database)는 데이터를 체계적으로 저장하고 관리하기 위한 데이터의 집합이다. 웹사이트의 회원 정보, 게시글, 상품 정보 등이 데이터베이스에 저장된다.

SQL(Structured Query Language)은 관계형 데이터베이스에 저장된 데이터를 조회, 추가, 수정, 삭제하기 위해 사용하는 언어이다.

| 명령어    | 기능     |
| ------ | ------ |
| SELECT | 데이터 조회 |
| INSERT | 데이터 추가 |
| UPDATE | 데이터 수정 |
| DELETE | 데이터 삭제 |
| WHERE  | 조건 지정  |

예를 들어 회원 테이블에서 이름이 `user1`인 회원을 조회하는 SQL문은 다음과 같다.

```sql
SELECT * FROM users WHERE username = 'user1';
```

### 3.2. 웹사이트의 데이터 처리 과정

사용자가 웹사이트에 정보를 입력하면 서버는 입력값을 전달받고, 이를 바탕으로 데이터베이스에 필요한 정보를 요청한다.

```
사용자 입력
     ↓
웹 애플리케이션 서버
     ↓
SQL 쿼리 실행
     ↓
데이터베이스
     ↓
결과 반환
```


이 과정에서 사용자 입력값이 SQL문에 안전하지 않은 방식으로 포함되면 SQL Injection이 발생할 수 있다.

## 4. SQL Injection이란?

SQL Injection은 사용자의 입력값을 통해 SQL문의 구조를 변경하고, 데이터베이스가 개발자의 의도와 다른 동작을 수행하도록 만드는 공격이다.

MITRE에서는 해당 취약점을 CWE-89로 분류하고 있다.

### 4.1. 발생 원인

SQL Injection의 핵심 원인은 SQL 명령과 사용자 입력값을 분리하지 않는 것이다.

다음은 상품 이름을 입력받아 데이터를 조회하는 취약한 Python 코드이다.

```python
product = input("상품 이름: ")

query = "SELECT * FROM products WHERE name = '" + product + "'"

cursor.execute(query)
```


사용자가 `abcd`을 입력하면 다음과 같은 SQL문이 실행된다.

```sql
SELECT * FROM products WHERE name = 'abcd';
```


그러나 입력값에 SQL 문법으로 해석될 수 있는 문자가 포함되면 기존 SQL문의 구조가 변경될 수 있다.

예를 들어 작은따옴표가 포함된 `ab'cd`를 입력하면 다음과 같이 SQL 문법이 깨질 수 있다.

```sql
SELECT * FROM products WHERE name = 'ab'cd';
```


이처럼 입력값이 단순한 데이터가 아니라 SQL 문법의 일부로 해석되는 것이 SQL Injection의 핵심 문제이다.

## 5. SQL Injection의 공격 유형

SQL Injection은 공격 결과를 확인하는 방식에 따라 여러 유형으로 구분된다.

| 공격 유형               | 공격 원리                              |
| ------------------- | ---------------------------------- |
| Error-based         | 데이터베이스의 오류 메시지를 이용하여 내부 정보를 파악     |
| UNION-based         | 여러 SQL 조회 결과를 결합하여 의도하지 않은 데이터에 접근 |
| Boolean-based Blind | 조건의 참·거짓에 따른 응답 차이를 이용하여 정보 추론     |
| Time-based Blind    | 서버의 응답 시간 차이를 이용하여 정보 추론           |
| Out-of-band         | 별도의 통신 경로를 이용하여 정보 획득              |

특히 Blind SQL Injection은 데이터베이스의 조회 결과가 화면에 직접 출력되지 않더라도 내부 정보를 추론할 수 있다는 특징이 있다.

### 5.1. 주요 피해

- 올바른 비밀번호 없이 인증 우회
- 회원 정보 및 개인정보 유출
- 데이터베이스 정보 변조 및 삭제
- 서비스 운영 중단

공격으로 발생할 수 있는 피해 범위는 데이터베이스의 권한과 시스템 환경에 따라 달라진다.

## 6. 실제 공격 사례

### TalkTalk 개인정보 유출 사고

2015년 10월 영국의 통신 기업 TalkTalk에서 SQL Injection을 이용한 개인정보 유출 사고가 발생하였다.

공격자는 웹사이트의 취약점을 이용하여 고객의 개인정보 및 금융 관련 정보에 접근하였다.

영국 정보위원회(ICO)는 적절한 보안 조치와 모니터링이 부족했다는 점 등을 근거로 2016년 TalkTalk에 40만 파운드의 과징금을 부과하였다.

이 사고는 오래전부터 알려진 취약점이라도 적절한 보안 조치가 이루어지지 않으면 큰 피해로 이어질 수 있음을 보여준다.

## 7. SQL Injection 대응 방안

### 7.1. Parameterized Query 사용

SQL Injection을 방지하기 위한 대표적인 방법은 SQL 명령과 사용자 입력값을 분리하여 처리하는 것이다.

이를 Parameterized Query(매개변수화된 쿼리)라고 한다.

**개선 전 코드**

```python
query = "SELECT * FROM products WHERE name = '" + product + "'"

cursor.execute(query)
```


**개선 후 코드**

```python
query = "SELECT * FROM products WHERE name = ?"

cursor.execute(query, (product,))
```

개선된 코드는 `?`를 통해 입력값이 들어갈 위치를 지정하고, SQL 명령과 입력값을 별도로 전달한다.

따라서 사용자 입력값은 SQL 명령이 아닌 데이터로 처리되며, 해당 입력값을 통해 SQL문의 구조를 변경할 수 없게 된다.

### 7.2. 추가적인 대응 방안

| 대응 방안     | 설명                             |
| --------- | ------------------------------ |
| 입력값 검증    | 입력 데이터가 요구되는 형식에 맞는지 확인        |
| 최소 권한 원칙  | 데이터베이스에 필요한 권한만 부여하여 피해 최소화    |
| 오류 메시지 관리 | 상세한 데이터베이스 오류가 외부에 노출되지 않도록 설정 |
| WAF       | 의심스러운 웹 요청을 탐지하고 차단            |
| 보안 테스트    | 코드 검토 및 취약점 점검을 통해 문제를 사전에 발견  |

입력값 검증이나 WAF만으로 모든 SQL Injection을 방지할 수는 없다. 따라서 SQL 명령과 입력값을 분리하는 근본적인 해결 방법을 우선 적용해야 한다.

## 8. 학습 소감

SQL Injection을 조사하면서 단순한 사용자 입력값도 처리 방식에 따라 심각한 보안 문제로 이어질 수 있다는 것을 알게 되었다.

특히 취약한 코드와 개선된 코드를 비교하면서 작은 코드 작성 방식의 차이가 보안에 큰 영향을 미칠 수 있다는 점이 인상적이었다.

또한 데이터베이스의 정보가 직접 노출되지 않더라도 응답 시간이나 응답 내용의 차이를 통해 내부 정보를 추론할 수 있다는 점을 알게 되었다.

앞으로 프로그램을 개발할 때 정상적인 기능 구현뿐만 아니라 예상하지 못한 입력값에 대해서도 안전하게 동작하는지 고려해야겠다고 생각하였다.

## 9. 참고자료

1. [OWASP Top 10:2025 – A05 Injection](https://top10.owasp.org/2025/A05_2025-Injection/)
2. [MITRE – CWE-89: SQL Injection](https://cwe.mitre.org/data/definitions/89.html)
3. [PortSwigger – SQL Injection](https://portswigger.net/web-security/sql-injection)
4. [PortSwigger – Blind SQL Injection](https://portswigger.net/web-security/sql-injection/blind)
5. [OWASP – SQL Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)
6. [Python Documentation – sqlite3](https://docs.python.org/3/library/sqlite3.html)
7. [ICO – TalkTalk Cyber Attack](https://ico.org.uk/about-the-ico/media-centre/talktalk-cyber-attack-how-the-ico-investigation-unfolded/)
