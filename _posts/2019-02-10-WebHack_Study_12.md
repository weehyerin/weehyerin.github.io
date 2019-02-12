---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - XSS(Stored)"
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

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 14강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

<!--more-->

## 크로스 사이트 스크립팅

* 악의적인 스크립트 코드가 데이터 베이스에 저장됨
* 저장된 코드 내용을 확인한 사용자의 PC에 스크립트 코드가 실행되는 공격

![image](https://user-images.githubusercontent.com/28076542/52530557-22229680-2d4b-11e9-9895-e5e28806ff80.png)

* HTML 인젝션과 크게 다르지 않음

---

## XSS - Stored(Blog)(Low)

![image](https://user-images.githubusercontent.com/28076542/52530575-5ac27000-2d4b-11e9-9e26-c7a6eee4132f.png)

* 다음 코드를 삽입

```html
<script>alert(1);document.write(document.cookie);</script>
```

![image](https://user-images.githubusercontent.com/28076542/52530607-8a717800-2d4b-11e9-8160-0aa1e093afcc.png)

---

## XSS - Stored(Blog)(High)

![image](https://user-images.githubusercontent.com/28076542/52530776-b2fa7180-2d4d-11e9-8991-5164bd2cd673.png)

* 서버의 다음 코드의 필터링으로 바뀐 것

```php
<?php

}

while($row = $recordset->fetch_object())
{
    if($_COOKIE["security_level"] == "2")
    {

?>
        <tr height="40">

            <td align="center"><?php echo $row->id; ?></td>
            <td><?php echo $row->owner; ?></td>
            <td><?php echo $row->date; ?></td>
            <td><?php echo xss_check_3($row->entry); ?></td>

        </tr>

<?php

    }

    else
        if($_COOKIE["security_level"] == "1")
        {

?>
        <tr height="40">

            <td align="center"><?php echo $row->id; ?></td>
            <td><?php echo $row->owner; ?></td>
            <td><?php echo $row->date; ?></td>
            <td><?php echo xss_check_4($row->entry); ?></td>

        </tr>

<?php

        }
        else

            {

?>
```

---

## XSS - Stored (Change Secret)(Low)

![image](https://user-images.githubusercontent.com/28076542/52530806-50ee3c00-2d4e-11e9-93e2-f92e94992fce.png)

* `<script>alert(1);</script>`를 넣고 change
* SQL Injection (Login Form/user)에 들어가서 관리자 계정으로 로그인하면 위의 alert 결과물이 나옴

### SQL Injection (Login Form/user)

![image](https://user-images.githubusercontent.com/28076542/52530828-b7735a00-2d4e-11e9-8c02-07c225839469.png)

---

## XSS - Stored(User-Agent)

![image](https://user-images.githubusercontent.com/28076542/52530837-f1446080-2d4e-11e9-9bdb-191fbf7ff0e7.png)

### User Agent

* 웸 페이지에 들어갈 때 현재 상태를 사용자의 접속 환경을 알려줌
* 칼리리눅스의 파이어 폭스의 User Agent Switcher 애드온을 설치하면 현재 자신의 User Agent를 바꿀 수 있음

![image](https://user-images.githubusercontent.com/28076542/52531051-a9bfd380-2d52-11e9-940b-982d870f7de8.png)

* 안드로이드로 User Agent로 설정하고 네이버에 접속하여 BurpSuite로 확인하면 다음과 같음

![image](https://user-images.githubusercontent.com/28076542/52531058-d247cd80-2d52-11e9-8013-1b13a5e34fde.png)

![image](https://user-images.githubusercontent.com/28076542/52530901-767c4500-2d50-11e9-94fc-676c421e2345.png)

![image](https://user-images.githubusercontent.com/28076542/52530906-8b58d880-2d50-11e9-881d-0fc2e7dea39d.png)

* 서버가 302로 모바일 사이트로 리다이렉트 시킴

### User Agent에 스크립트 넣기

* `<script>document.write(document.cookie)</script>`를 삽입

![image](https://user-images.githubusercontent.com/28076542/52531083-3ec2cc80-2d53-11e9-83b2-173d5353bfbe.png)

![image](https://user-images.githubusercontent.com/28076542/52531092-6f0a6b00-2d53-11e9-9c81-ca12784e4702.png)

* 다시 재접속을하면 다음과 같음

![image](https://user-images.githubusercontent.com/28076542/52531098-8184a480-2d53-11e9-94a1-004d30a358a9.png)