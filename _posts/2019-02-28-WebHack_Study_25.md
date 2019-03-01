---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - 검증되지 않은 리다이렉트와 포워드"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part10 A10
  - 검증되지 않은 리다이렉트와 포워드
---

<!--more-->

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 27강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

## 검증되지 않은 리다이렉트 및 포워드

* 리다이렉션 시 목적지에 대한 검증이 이루어지지 않을 때 공격에 사용 가능
* 악성 사이트나 피싱 등의 공격으로 사용자에게 피해가 발생
* 관리자 단말 PC에 악성코드가 감염되면 순식간에 내부 시스템 침투 가능성

![image](https://user-images.githubusercontent.com/28076542/53549919-d8261700-3b78-11e9-8b34-b319ac51733d.png)

## Unvalidated Redirects & Forwards (1)

![image](https://user-images.githubusercontent.com/28076542/53550181-6ef2d380-3b79-11e9-9394-fb13f2200a61.png)

* Beam 버튼을 누르면 선택한 사이트로 이동

### 1. BurpSuite로 프록시 잡기

![image](https://user-images.githubusercontent.com/28076542/53550254-9fd30880-3b79-11e9-89a6-af9f13395b53.png)

![image](https://user-images.githubusercontent.com/28076542/53553229-84b7c700-3b80-11e9-8a5e-e4e476a2fc77.png)

* 302로 Redirect하는 것을 알 수 있음

### 2. BurpSuite에서 Redirect URL 바꾸기

![image](https://user-images.githubusercontent.com/28076542/53554827-ffceac80-3b83-11e9-878f-1ce96888f0bd.png)

* URL:`http%3A%2Fwww.naver.com`
* 별거 없음

## Unvalidated Redirects & Forwards (2)

![image](https://user-images.githubusercontent.com/28076542/53555054-82576c00-3b84-11e9-9c52-67b88e579a51.png)

* 이것도 위와 마찬가지

### unvalidated_redir_fwd_1.php에서 방어하는 부분

```php
// Destroys the session and the cookie 'admin'
    if($_COOKIE["security_level"] == "2")
    {
        // Destroys the session 
        $_SESSION = array();
        session_destroy();
        // Deletes the cookies
        setcookie("admin", "", time()-3600, "/", "", false, false);
        setcookie("movie_genre", "", time()-3600, "/", "", false, false);
        setcookie("secret", "", time()-3600, "/", "", false, false);
        setcookie("top_security", "", time()-3600, "/", "", false, false);
        setcookie("top_security_nossl", "", time()-3600, "/", "", false, false);
        setcookie("top_security_ssl", "", time()-3600, "/", "", false, false);
    }
    switch($_REQUEST["url"])
    {
        case "1" :
            header("Location: http://itsecgames.blogspot.com");
            break;
        case "2" :
            header("Location: http://www.linkedin.com/in/malikmesellem");
            break;
        case "3" :
            header("Location: http://twitter.com/MME_IT");
            break;
        case "4" :
            header("Location: http://www.mmeit.be/en");
            break;
        default :
            header("Location: login.php");
            break;
    }
}
```

* 쿠키를 지우고 미리 정해진 URL로만 가게 설정함

### unvalidated_redir_fwd_2.php에서 방어하는 부분

```php
if(isset($_REQUEST["ReturnUrl"]) && ($_COOKIE["security_level"] == "1" || $_COOKIE["security_level"] == "2"))
{
    header("Location: portal.php");
    exit;
}
```

* 무조건 portal.php로 이동하게 함

# 끝 ^~^