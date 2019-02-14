---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - 취약한 직접 객체 참조(중요 정보 변경 및 초기화, 상품 주문 가격 조작)"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part4 A4
  - 취약한 직접 객체 참조
---

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 16강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

<!--more-->

## 취약한 직접 객체 참조

* 클라이언트에서 보내는 값을 무작정 믿고 인증을 처리한다면 문제가 발생할 수 있다는 것

---

## Insecure DOR (Change Secret)

![image](https://user-images.githubusercontent.com/28076542/52609185-bc0b5000-2ebf-11e9-8638-a18213d03e32.png)

* 비밀번호 힌트를 바꾸는 내용임을 알 수 있음
* New secret에 test1234를 누르고 SQL Injection **(Login Form/user)**에 들어가면 secret 값이 바뀜을 알 수 있음

### SQL Injection (Login Form/User)

![image](https://user-images.githubusercontent.com/28076542/52609278-10163480-2ec0-11e9-822f-e47a1766502e.png)

* ID: `bee`, Password: `bug`를 입력하면 위와 같은 결과가 나오며 Your secret: **test1234**가 뜸
* SQL 인젝션으로 조회할 수 있음 --> ID: `' or 1=1 #`, Password: `bug`

### ID에 SQL 인젝션한 결과 나오는 아이디 --> A.I.M.

![image](https://user-images.githubusercontent.com/28076542/52690448-116b5e00-2fa1-11e9-95bf-f89f76ed3721.png)

* A.I.M의 secret 값을 바꾸기 위해 다시 **Insecure DOR (Change Secret)**으로 이동
* BurpSuite로 login 항목 값 변경

### Insecure DOR에서 BurpSuite로 login 값 변경

![image](https://user-images.githubusercontent.com/28076542/52690594-8343a780-2fa1-11e9-914c-02254ae3461c.png)

### SQL Injection (Login Form/User)에서 다시 A.I.M. 조회한 결과

![image](https://user-images.githubusercontent.com/28076542/52690670-c43bbc00-2fa1-11e9-9b36-d6cc668b22e0.png)

* 클라이언트 요청에 대하여 웹에서 로그인 된 유저인지 확인을 하지 않아 문제가 됨

---

## Insecure DOR (Change Secret) (High)

New secret = test4321로 값을 입력하고 BurpSuite로 이를 확인하면 다음과 같음

### BurpSuite로 확인한 High 레벨 Insecure DOR

![image](https://user-images.githubusercontent.com/28076542/52699172-7c745f00-2fb8-11e9-8c78-2c4e1fb17ece.png)

* token이 존재함을 알 수 있음

### insecure_direct_object_ref_1.php에서 취약한 부분

```php
// If the security level is not MEDIUM or HIGH
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

* `$login`에 대하여 검증절차 없이 받아 문제가 됨

### insecure_direct_object_ref_1.php에서 Medium이나 High에서 처리하는 부분

```php
else
            {
                
                // If the security level is MEDIUM or HIGH
                if(!isset($_REQUEST["token"]) or !isset($_SESSION["token"]) or $_REQUEST["token"] != $_SESSION["token"])
                {

                    $message = "<font color=\"red\">Invalid token!</font>";            

                }

                else
                {

                    $secret = mysqli_real_escape_string($link, $secret);
                    $secret = htmlspecialchars($secret, ENT_QUOTES, "UTF-8");

                    $sql = "UPDATE users SET secret = '" . $secret . "' WHERE login = '" . $login . "'";

                    // Debugging
                    // echo $sql;      

                    $recordset = $link->query($sql);
```

* **token**을 설정하여 서버에 있는 **token**과 일치하는지 확인함

---

## Insecure DOR (Reset Secret)

![image](https://user-images.githubusercontent.com/28076542/52700678-de829380-2fbb-11e9-83a6-8a2f7a5cfb91.png)

### BurpSuite로 확인

![image](https://user-images.githubusercontent.com/28076542/52700770-1689d680-2fbc-11e9-891e-747aeffbb881.png)

* login으로 `bee`를 secret으로 `bug`를 보내주고 있음

### SQL Injection (Login Form/User)에서 확인

![image](https://user-images.githubusercontent.com/28076542/52700876-49cc6580-2fbc-11e9-97ea-544d391b8d34.png)

### A.I.M.으로 바꾸기

![image](https://user-images.githubusercontent.com/28076542/52700969-78e2d700-2fbc-11e9-8208-d1ffb672621e.png)

### A.I.M. secret 값 확인

![image](https://user-images.githubusercontent.com/28076542/52701051-a9c30c00-2fbc-11e9-9ca3-9a08641ca14d.png)

* 당연히 High 레벨에서는 안 됨

### insecure_direct_object_ref_3.php에서 High 레벨에서 안 되게 하는 부분

```php
function ResetSecret()
        {
            var xmlHttp;
            // Code for IE7+, Firefox, Chrome, Opera, Safari
            if(window.XMLHttpRequest)
            {
                xmlHttp = new XMLHttpRequest();
            }
            // Code for IE6, IE5
            else
            {
                xmlHttp = new ActiveXObject("Microsoft.XMLHTTP");
            }
            xmlHttp.open("POST","xxe-2.php",true);
            xmlHttp.setRequestHeader("Content-type","text/xml; charset=UTF-8");
            xmlHttp.send("<reset><login><?php if(isset($_SESSION["login"])){echo $_SESSION["login"];}?></login><secret>Any bugs?</secret></reset>");
        }
```

* **xxe-2.php**로 넘어가 secret을 reset함을 알 수 있음

### xxe-2.php

```php
// If the security level is MEDIUM or HIGH
else
{

    // Disables XML external entities. Doesn't work with older PHP versions!
    // libxml_disable_entity_loader(true);
    $xml = simplexml_load_string($body);
    
    // Debugging
    // print_r($xml);

    $login = $_SESSION["login"];
    $secret = $xml->secret;
```

* `$_SESSION`이라는 변수를 두어 방어하였음

---

## Insecure DOR (Order Tickets)

![image](https://user-images.githubusercontent.com/28076542/52701479-a7ad7d00-2fbd-11e9-97e7-5771b34a75cf.png)

### BurpSuite로 ticket_price를 15에서 1로 바꾸기

![image](https://user-images.githubusercontent.com/28076542/52716627-857a2600-2fe2-11e9-889c-35c9617066b5.png)

### 가격 조작한 결과

![image](https://user-images.githubusercontent.com/28076542/52716646-9460d880-2fe2-11e9-9941-ae3f9f4ea4c6.png)

* 겁나 유치하네

---

## Insecure DOR (Order Tickets) (Medium)

![image](https://user-images.githubusercontent.com/28076542/52716949-44364600-2fe3-11e9-8a5a-c37d888331de.png)

* price 항목이 없어졌음

### BurpSuite에서 ticket_price 항복을 입력 후 Forward와 그 결과

![image](https://user-images.githubusercontent.com/28076542/52717014-7051c700-2fe3-11e9-8004-51d739d9c697.png)

![image](https://user-images.githubusercontent.com/28076542/52717048-8495c400-2fe3-11e9-9384-20db5632df57.png)

### insecure_direct_object_ref_2.php에서 문제가 되는 코드 부분

```php
if(isset($_REQUEST["ticket_quantity"]))
{
    
    if($_COOKIE["security_level"] != "2")
    {

        if(isset($_REQUEST["ticket_price"]))
        {

            $ticket_price = $_REQUEST["ticket_price"];

        }
        
    }
    
    $ticket_quantity = abs($_REQUEST["ticket_quantity"]);
    $total_amount = $ticket_quantity * $ticket_price;
```

* 레벨 1에서 ticket_price가 설정되어 있는지 확인하고 설정되어 있다면 적용 
* **즉, ticket_price 값을 보내면 그대로 적용**