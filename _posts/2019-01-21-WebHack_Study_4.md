---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - SQL 기초"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part1 A1
  - SQL 인젝션
---

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 6강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

<!--more-->

# SQL 인젝션 기초

## SQL 기초

### 1. Bee-Box 터미널에 가서 다음 명령어 입력

```bash
root@bee-box:/var/www/bWAPP# netstat -antp
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:512             0.0.0.0:*               LISTEN      5985/inetd      
tcp        0      0 0.0.0.0:513             0.0.0.0:*               LISTEN      5985/inetd      
tcp        0      0 0.0.0.0:514             0.0.0.0:*               LISTEN      5985/inetd      
tcp        0      0 0.0.0.0:9443            0.0.0.0:*               LISTEN      6602/lighttpd   
tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN      5844/mysqld     
tcp        0      0 0.0.0.0:139             0.0.0.0:*               LISTEN      6098/smbd       
tcp        0      0 0.0.0.0:5901            0.0.0.0:*               LISTEN      6128/Xvnc       
tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN      5961/nginx      
tcp        0      0 0.0.0.0:3632            0.0.0.0:*               LISTEN      5946/distccd    
tcp        0      0 0.0.0.0:6001            0.0.0.0:*               LISTEN      6128/Xvnc       
tcp        0      0 0.0.0.0:21              0.0.0.0:*               LISTEN      6493/proftpd: (acce
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      5095/cupsd      
tcp        0      0 0.0.0.0:9080            0.0.0.0:*               LISTEN      6602/lighttpd   
tcp        0      0 0.0.0.0:25              0.0.0.0:*               LISTEN      6054/master     
tcp        0      0 0.0.0.0:666             0.0.0.0:*               LISTEN      5922/bwapp_movie_se
tcp        0      0 0.0.0.0:8443            0.0.0.0:*               LISTEN      5961/nginx      
tcp        0      0 0.0.0.0:445             0.0.0.0:*               LISTEN      6098/smbd       
tcp6       0      0 :::80                   :::*                    LISTEN      6576/apache2    
tcp6       0      0 :::6001                 :::*                    LISTEN      6128/Xvnc       
tcp6       0      0 :::22                   :::*                    LISTEN      5718/sshd       
tcp6       0      0 :::443                  :::*                    LISTEN      6576/apache2
```

* 3306에서 mysql이 사용되고 있음

```bash
root@bee-box:/var/www/bWAPP# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 15
Server version: 5.0.96-0ubuntu3 (Ubuntu)

Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

* -u: user 지정
* -p: 패스워드 입력
* `ctrl + L`: 화면 정리

### 데이터 베이스 구조

![image](https://user-images.githubusercontent.com/28076542/51726656-c2ba5a80-20ab-11e9-86b7-487900afd7e8.png)

### 2. 데이터 베이스 확인하기

```bash
mysql> show databases;
ERROR 2006 (HY000): MySQL server has gone away
No connection. Trying to reconnect...
Connection id:    16
Current database: *** NONE ***

+--------------------+
| Database           |
+--------------------+
| information_schema | 
| bWAPP              | 
| drupageddon        | 
| mysql              | 
+--------------------+
4 rows in set (0.01 sec)
```

### 3. 새로운 데이터 베이스 만들기

```bash
mysql> create database testdb;
Query OK, 1 row affected (0.00 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema | 
| bWAPP              | 
| drupageddon        | 
| mysql              | 
| testdb             | 
+--------------------+
5 rows in set (0.00 sec)
```

### 4. 데이터 베이스 선택하고 테이블 만들기

```bash
mysql> use testdb;
Database changed
mysql> create table testtable1(
    -> no int not null,
    -> name varchar(10) not null,
    -> id varchar(10) not null,
    -> pass varchar(20) not null);
Query OK, 0 rows affected (0.01 sec)

mysql> show tables;
+------------------+
| Tables_in_testdb |
+------------------+
| testtable1       | 
+------------------+
1 row in set (0.00 sec)

mysql> desc testtable1
    -> ;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| no    | int(11)     | NO   |     | NULL    |       | 
| name  | varchar(10) | NO   |     | NULL    |       | 
| id    | varchar(10) | NO   |     | NULL    |       | 
| pass  | varchar(20) | NO   |     | NULL    |       | 
+-------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)
```

* `not null`은 값이 무조건 들어가야 된다는 뜻

### 5. 데이터 집어넣기

```bash
mysql> insert into testtable1 values(1, "Sun", "isc0304", "test1234");
Query OK, 1 row affected (0.01 sec)

mysql> select * from testtable1
    -> ;
+----+------+---------+----------+
| no | name | id      | pass     |
+----+------+---------+----------+
|  1 | Sun  | isc0304 | test1234 | 
+----+------+---------+----------+
1 row in set (0.01 sec)
```

* `insert`는 데이터 삽입 명령어
* `select`는 테이블을 선택하는 명령어
* `*`은 모든 값을 검색하겠다는 뜻
* 값을 마저 넣어보면

```bash
mysql> insert into testtable1 values(2, "black", "hat", "python");
Query OK, 1 row affected (0.00 sec)

mysql> insert into testtable1 values(3, "practical", "malware", "analysis");
Query OK, 1 row affected (0.00 sec)

mysql> insert into testtable1 values(4, "python", "hacking", "programming");
Query OK, 1 row affected (0.00 sec)

mysql> insert into testtable1 values(5, "coding", "interview", "analysis");
Query OK, 1 row affected (0.01 sec)

mysql> insert into testtable1 values(6, "john", "gray", "love");
Query OK, 1 row affected (0.00 sec)

mysql> insert into testtable1 values(7, "lego", "mindstorm", "ev3");
Query OK, 1 row affected (0.00 sec)

mysql> select * from testtable1
    -> ;
+----+-----------+-----------+-------------+
| no | name      | id        | pass        |
+----+-----------+-----------+-------------+
|  1 | Sun       | isc0304   | test1234    | 
|  2 | black     | hat       | python      | 
|  3 | practical | malware   | analysis    | 
|  4 | python    | hacking   | programming | 
|  5 | coding    | interview | analysis    | 
|  6 | john      | gray      | love        | 
|  7 | lego      | mindstorm | ev3         | 
+----+-----------+-----------+-------------+
7 rows in set (0.00 sec)
```

## SQL 기본 문법 1

* SELECT + WHERE 데이터 조건 검색

|종류|연산자|설명|
|:----:|:----:|:----:|
|비교|=, <, >, <=, >=, !=, <>|두 값을 비교|
|논리|AND, OR, NOT|조건과 조건을 결합|
|범위|BETWEEN a AND b|a와 b 사이에 존재하는지 검사|
|집합|IN|해당 값이 목록에 존재하는지 검사|
|패턴|LIKE|문자열의 패턴 검사|

### 6. 비교와 논리를 이용하여 검색하기

```bash
mysql> select * from testtable1 where no=1;
+----+------+---------+----------+
| no | name | id      | pass     |
+----+------+---------+----------+
|  1 | Sun  | isc0304 | test1234 | 
+----+------+---------+----------+
1 row in set (0.00 sec)

mysql> select * from testtable1 where no=2;
+----+-------+-----+--------+
| no | name  | id  | pass   |
+----+-------+-----+--------+
|  2 | black | hat | python | 
+----+-------+-----+--------+
1 row in set (0.00 sec)

mysql> select * from testtable1 where no>2;
+----+-----------+-----------+-------------+
| no | name      | id        | pass        |
+----+-----------+-----------+-------------+
|  3 | practical | malware   | analysis    | 
|  4 | python    | hacking   | programming | 
|  5 | coding    | interview | analysis    | 
|  6 | john      | gray      | love        | 
|  7 | lego      | mindstorm | ev3         | 
+----+-----------+-----------+-------------+
5 rows in set (0.00 sec)

mysql> select * from testtable1 where no=2 or no=1;
+----+-------+---------+----------+
| no | name  | id      | pass     |
+----+-------+---------+----------+
|  1 | Sun   | isc0304 | test1234 | 
|  2 | black | hat     | python   | 
+----+-------+---------+----------+
2 rows in set (0.00 sec)
```

### 7. 범위와 집합 검색하기

```bash
mysql> select * from testtable1 where no between 1 and 4;
+----+-----------+---------+-------------+
| no | name      | id      | pass        |
+----+-----------+---------+-------------+
|  1 | Sun       | isc0304 | test1234    | 
|  2 | black     | hat     | python      | 
|  3 | practical | malware | analysis    | 
|  4 | python    | hacking | programming | 
+----+-----------+---------+-------------+
4 rows in set (0.00 sec)

mysql> select * from testtable1 where name in ("Sun");
+----+------+---------+----------+
| no | name | id      | pass     |
+----+------+---------+----------+
|  1 | Sun  | isc0304 | test1234 | 
+----+------+---------+----------+
1 row in set (0.00 sec)

mysql> select * from testtable1 where name in ("black");
+----+-------+-----+--------+
| no | name  | id  | pass   |
+----+-------+-----+--------+
|  2 | black | hat | python | 
+----+-------+-----+--------+
1 row in set (0.00 sec)

mysql> select * from testtable1 where name in ("Sun", "black");
+----+-------+---------+----------+
| no | name  | id      | pass     |
+----+-------+---------+----------+
|  1 | Sun   | isc0304 | test1234 | 
|  2 | black | hat     | python   | 
+----+-------+---------+----------+
2 rows in set (0.01 sec)

```

### 8. 패턴 검색하기

```bash
mysql> select * from testtable1 where name like "Sun"
    -> ;
+----+------+---------+----------+
| no | name | id      | pass     |
+----+------+---------+----------+
|  1 | Sun  | isc0304 | test1234 | 
+----+------+---------+----------+
1 row in set (0.00 sec)

mysql> select * from testtable1 where name like "S%";
+----+------+---------+----------+
| no | name | id      | pass     |
+----+------+---------+----------+
|  1 | Sun  | isc0304 | test1234 | 
+----+------+---------+----------+
1 row in set (0.00 sec)

mysql> select * from testtable1 where name like "%a%";
+----+-----------+---------+----------+
| no | name      | id      | pass     |
+----+-----------+---------+----------+
|  2 | black     | hat     | python   | 
|  3 | practical | malware | analysis | 
+----+-----------+---------+----------+
2 rows in set (0.00 sec)

mysql> select * from testtable1 where name like "%l%";
+----+-----------+-----------+----------+
| no | name      | id        | pass     |
+----+-----------+-----------+----------+
|  2 | black     | hat       | python   | 
|  3 | practical | malware   | analysis | 
|  7 | lego      | mindstorm | ev3      | 
+----+-----------+-----------+----------+
3 rows in set (0.00 sec)
```

---

### SQL 기본 문법 2

* SELECT + limit 검색 레코드 수 제한

|사용 방법|설명|
|:----:|:----:|
|limit 1|레코드 1개 출력|
|limit 0, 2|첫 번째(0) 레코드부터 2개의 레코드를 가져옴|

--> 어디서부터 어디까지만 출력해라

### 9. limit을 이용해 원하는 수만큼 출력하기

```bash
mysql> select * from testtable1 limit 3;
+----+-----------+---------+----------+
| no | name      | id      | pass     |
+----+-----------+---------+----------+
|  1 | Sun       | isc0304 | test1234 | 
|  2 | black     | hat     | python   | 
|  3 | practical | malware | analysis | 
+----+-----------+---------+----------+
3 rows in set (0.00 sec)

mysql> select * from testtable1 limit 0,3;
+----+-----------+---------+----------+
| no | name      | id      | pass     |
+----+-----------+---------+----------+
|  1 | Sun       | isc0304 | test1234 | 
|  2 | black     | hat     | python   | 
|  3 | practical | malware | analysis | 
+----+-----------+---------+----------+
3 rows in set (0.00 sec)

mysql> select * from testtable1 limit 1,3;
+----+-----------+---------+-------------+
| no | name      | id      | pass        |
+----+-----------+---------+-------------+
|  2 | black     | hat     | python      | 
|  3 | practical | malware | analysis    | 
|  4 | python    | hacking | programming | 
+----+-----------+---------+-------------+
3 rows in set (0.00 sec)

mysql> select * from testtable1 limit 2,3;
+----+-----------+-----------+-------------+
| no | name      | id        | pass        |
+----+-----------+-----------+-------------+
|  3 | practical | malware   | analysis    | 
|  4 | python    | hacking   | programming | 
|  5 | coding    | interview | analysis    | 
+----+-----------+-----------+-------------+
3 rows in set (0.00 sec)
```

### SQL 기본 문법 3

* SELECT + UNION 여러 조회 결과를 결합하여 출력
* UNION에 연결되는 두 테이블의 컬럼의 수가 일치되어야 함

### 10. 테이블 하나 더 만들기

```bash
mysql> create table uniontb(
    ->   var1 varchar(10) not null,
    ->   var2 varchar(10) not null,
    ->   var3 varchar(10) not null,
    ->   var4 varchar(10) not null);
Query OK, 0 rows affected (0.01 sec)

mysql> insert into uniontb values("Building", "Machine", "Learning", "Systems");
Query OK, 1 row affected (0.00 sec)

mysql> insert into uniontb values("with", "Python", "Seven", "Language");
Query OK, 1 row affected (0.00 sec)

mysql> select * from uniontb;
+----------+---------+----------+----------+
| var1     | var2    | var3     | var4     |
+----------+---------+----------+----------+
| Building | Machine | Learning | Systems  | 
| with     | Python  | Seven    | Language | 
+----------+---------+----------+----------+
2 rows in set (0.00 sec)

mysql> select * from testtable1;
+----+-----------+-----------+-------------+
| no | name      | id        | pass        |
+----+-----------+-----------+-------------+
|  1 | Sun       | isc0304   | test1234    | 
|  2 | black     | hat       | python      | 
|  3 | practical | malware   | analysis    | 
|  4 | python    | hacking   | programming | 
|  5 | coding    | interview | analysis    | 
|  6 | john      | gray      | love        | 
|  7 | lego      | mindstorm | ev3         | 
+----+-----------+-----------+-------------+
7 rows in set (0.00 sec)
```

* uniontb와 testtable1의 column수가 같으므로 합치는 작업을 할 것
* **column의 수가 같아야만 합칠 수 있음**

### 11. 테이블 합치기

```bash
 select * from testtable1 union select * from uniontb;
+----------+-----------+-----------+-------------+
| no       | name      | id        | pass        |
+----------+-----------+-----------+-------------+
| 1        | Sun       | isc0304   | test1234    | 
| 2        | black     | hat       | python      | 
| 3        | practical | malware   | analysis    | 
| 4        | python    | hacking   | programming | 
| 5        | coding    | interview | analysis    | 
| 6        | john      | gray      | love        | 
| 7        | lego      | mindstorm | ev3         | 
| Building | Machine   | Learning  | Systems     | 
| with     | Python    | Seven     | Language    | 
+----------+-----------+-----------+-------------+
9 rows in set (0.00 sec)
```

### 12. 앞 테이블에는 거짓을 뒤 테이블에는 참인 명령을 써보기

```bash
mysql> select * from testtable1 where 1=2 union select * from uniontb;
+----------+---------+----------+----------+
| no       | name    | id       | pass     |
+----------+---------+----------+----------+
| Building | Machine | Learning | Systems  | 
| with     | Python  | Seven    | Language | 
+----------+---------+----------+----------+
2 rows in set (0.00 sec)

mysql> select * from testtable1 where no=8 union select * from uniontb;
+----------+---------+----------+----------+
| no       | name    | id       | pass     |
+----------+---------+----------+----------+
| Building | Machine | Learning | Systems  | 
| with     | Python  | Seven    | Language | 
+----------+---------+----------+----------+
2 rows in set (0.00 sec)
```

* `where 1=2`와 `where no=8`은 거짓이므로 뒤 uniontb의 내용만 나오게 됨

```bash
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema | 
| bWAPP              | 
| drupageddon        | 
| mysql              | 
| testdb             | 
+--------------------+
5 rows in set (0.01 sec)
```

* **information schema**: 다른 데이터 베이스를 조회할 수 있는 테이블
* information schema를 악용할 수 있음 --> 앞에 쿼리는 거짓, 뒤에는 테이블을 넣는 방법으로 가능