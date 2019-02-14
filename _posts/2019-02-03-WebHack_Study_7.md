---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - SQL 인젝션 기초(AJAX, Login, Blog)"
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

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 9강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

<!--more-->

# SQL 인젝션 기초

## SQL 인젝션(AJAX, Login, Blog)

## AJAX/JSON/jQuery

* AJAX(Asynchronous JavaScript and XML): 자바스크립트와 JSON을 혼합하여 사용하는 기술
* 페이지 이동 없이 고속으로 화면 전환 가능, 서버 처리를 기다리지 않고 비동기 요청 가능, 수신하는 데이터 양을 줄이고, 클라이언트에게 처리를 위임할 수 있음

## SQL Injection (AJAX/JSON/JQuery)

![image](https://user-images.githubusercontent.com/28076542/52172175-7fe43b00-27ad-11e9-9dd2-76a7dfd4c36a.png)

![image](https://user-images.githubusercontent.com/28076542/52172231-a9519680-27ae-11e9-8e81-b54fdcd57903.png)

![image](https://user-images.githubusercontent.com/28076542/52172237-c8502880-27ae-11e9-9f05-a8144f079f49.png)

![image](https://user-images.githubusercontent.com/28076542/52172241-def67f80-27ae-11e9-8222-b1a52ccdb3d8.png)

* 검색 버튼을 누르지 않고 키보드 키만 눌러도 **XMLHttpRequest**를 통해 요청
* Response는 다음과 같음

![image](https://user-images.githubusercontent.com/28076542/52172252-1d8c3a00-27af-11e9-9d50-a801a4fb5758.png)

### 페이지 소스 확인

```html
<script>

        $("#title").keyup(function(){
            // Searches for a movie title
            var search = {title: $("#title").val()};

            // AJAX call
            $.getJSON("sqli_10-2.php", search, function(data){
                init_table();
                // Constructs the table from the JSON data
                var total = 0;
                $.each(data, function(key, val){
                    total++;
                    $("#table_yellow tr:last").after("<tr><td>" + val.title + "</td><td align='center'>" + val.release_year + "</td><td>" + val.main_character + "</td><td align='center'>" + val.genre + "</td><td align='center'><a href='http://www.imdb.com/title/" + val.imdb + "' target='_blank'>Link</a></td></tr>");
                });
                // Empty result
                if (total == 0)
                {
                    $("#table_yellow tr:last").after("<tr height='30'><td colspan='5' width='580'>No movies were found!</td></tr>");
                }
            })

        });

        function init_table(){
            $("#table_yellow").html("<tr height='30' bgcolor='#ffb717' align='center'>" +
                    "<td width='200'><b>Title</b></td>" +
                    "<td width='80'><b>Release</b></td>" +
                    "<td width='140'><b>Character</b></td>" +
                    "<td width='80'><b>Genre</b></td>" +
                    "<td width='80'><b>IMDb</b></td>" +
                    "</tr>"
                    );
        }

    </script>
```

* 이전 SQL 인젝션과 마찬가지로 검색창에 SQL 인젝션 구문을 넣으면 실행되는 것을 확인할 수 있음
* `0' union select 1,table_name,3,4,5,6,7 from information_schema.tables #`

![image](https://user-images.githubusercontent.com/28076542/52172305-1b76ab00-27b0-11e9-9875-c268ae66289c.png)

## SQL Injection (Login Form/Hero)

![image](https://user-images.githubusercontent.com/28076542/52172311-3a753d00-27b0-11e9-9bf8-738568f42e92.png)

* Login: `' or 1=1 #`
* Password: `1`

![image](https://user-images.githubusercontent.com/28076542/52172326-8cb65e00-27b0-11e9-862b-647055396eda.png)

* 테이블 두 번째 것을 출력하고 싶으면
* Login: `' or 1=1 limit 1,1 #`

## SQL Injection (Stored(Blog))

![image](https://user-images.githubusercontent.com/28076542/52172487-b58c2280-27b3-11e9-9e86-14a9066bb1fe.png)

* SQL의 insert문을 이용하여 테이블을 채우는 구조
* 대략 형태는 `insert testtable1 value('test1234', 'bee')`와 같음
* `test1234', database()) #`

![image](https://user-images.githubusercontent.com/28076542/52172518-5b3f9180-27b4-11e9-8d4e-11f8e360a4b6.png)

* `test1234', @@version)#`

![image](https://user-images.githubusercontent.com/28076542/52172525-81fdc800-27b4-11e9-92b4-01bcb6949a90.png)