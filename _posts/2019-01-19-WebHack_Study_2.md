---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - HTML 인젝션"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part1 A1
  - HTML 인젝션
---

<!--more-->

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 4강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

# A1. 인젝션

* 인젝션은 주사를 주입하는 것과 같이 코드를 주입하여 프로그램이 다른 동작을 수행하게 하는 것

## HTML 인젝션 Reflected(GET)

![image](https://user-images.githubusercontent.com/28076542/51426907-397ed000-1c34-11e9-9a57-e4f58b09e79c.png)

* 취약한 매개변수에 악의적인 HTML 코드를 삽입하는 공격
* HTML 태그로 악의적인 사이트에 연결하거나 악성 파일을 다운로드 하도록 유도

### HTML 실습

#### 1. 칼리리눅스 터미널 실행 후 gedit 열기

#### 2. 다음 코드 입력

```html
<html>
<head>
<title>Test Title</title>
<body>
<h1>Hello World</h1>
<h2>Hello World2</h2>
Hello World
</body>
</html>
```

![image](https://user-images.githubusercontent.com/28076542/51426849-b3628980-1c33-11e9-96b0-433ca45a7de0.png)

* 이 코드를 `test.html`로 저장하고 실행하면 다음과 같은 결과를 얻음

![image](https://user-images.githubusercontent.com/28076542/51426877-e9077280-1c33-11e9-9aa9-ac8f776b1767.png)

* 다른 웹사이트들도 이러한 HTML 코드로 구성되어 있음

### HTML 인젝션에서 알아야할 것

![image](https://user-images.githubusercontent.com/28076542/51426929-94b0c280-1c34-11e9-863d-ac56d37f7d53.png)

* 서버와 클라이언트에서 사용하는 언어가 각각 다름
* 클라이언트는 예전에 정적 페이지만 보여줬는데 요즘은 자바스크립트 등 다른 언어를 삽입할 수 있음
* 따라서 HTML 코드를 바꾸는 과정을 수행할 수 있음

### HTML 인젝션 Reflected 실습

![image](https://user-images.githubusercontent.com/28076542/51426961-d8a3c780-1c34-11e9-9a41-541fa024fc59.png)

![image](https://user-images.githubusercontent.com/28076542/51426982-13a5fb00-1c35-11e9-9c43-696c46d148cb.png)

![image](https://user-images.githubusercontent.com/28076542/51426984-25879e00-1c35-11e9-8add-471260a5967b.png)

* HTML 태그가 해석이 되어 클라이언트 창에 띄워주게 됨
* 클라이언트 코드가 서버에 영향을 준 것이 아니라
* 서버로 보낸 클라이언트 코드가 클라이언트한테 그대로 전달한 것(Reflected)
* 이러한 요청과 응답의 과정은 HTTP로 이루어짐

### HTTP 살펴보기

#### 1. 칼리리눅스에서 BurpSuite 실행하기

#### 2. 파이어폭스에서 Preference --> Advanced --> Network의 Settings

![image](https://user-images.githubusercontent.com/28076542/51427038-05a4aa00-1c36-11e9-9922-19c0f4636722.png)

* HTTP proxy: 127.0.0.1, port 8080

#### 3. BurpSuite 옵션에서 intercept 옵션 켜기

![image](https://user-images.githubusercontent.com/28076542/51434343-dfb4ef00-1ca1-11e9-8171-42f31b9aeddd.png)

#### 4. bWApp에 다음과 같이 입력 후 BurpSuite에서 잡히는 것 확인하기

![image](https://user-images.githubusercontent.com/28076542/51434369-59e57380-1ca2-11e9-8728-facc6fbc766f.png)

* body가 없고 header만 있음

## test.html에 자바스크립트 넣어보기

```html
<html>
<head>
<title>Test Title</title>
<body>
<h1>Hello World</h1>
<h2>Hello World2</h2>
Hello World
</body>
</html>
<script>alert("TEST")</script>
```

![image](https://user-images.githubusercontent.com/28076542/51434403-d6785200-1ca2-11e9-91bd-a0f1e21cbf74.png)

* bWApp에 적용해도 같은 결과가 나옴
* 스크립트 삽입이 가능하면 쿠키를 가져오고 악의적인 사이트로 리다이렉트 될 수 있음
* 이미지 태그로 이미지를 가져올 수도 있음 --> `<img src=hhtp~~/img.png>`
* 쿠키도 가져올 수 있음 --> `<script>alert(document.cookie)</script>`

![image](https://user-images.githubusercontent.com/28076542/51434448-8fd72780-1ca3-11e9-9898-ec8981679d4d.png)

---

## medium 레벨로 가서 확인하기

![image](https://user-images.githubusercontent.com/28076542/51434457-e93f5680-1ca3-11e9-96a8-2f56628f95b8.png)

![image](https://user-images.githubusercontent.com/28076542/51434465-2dcaf200-1ca4-11e9-9d2e-c4b90ce7655a.png)

* low와 달리 medium에서는 태그가 그대로 나옴
* 소스를 확인해보면 태그가 다른 문자로 치환되어 있음

### 1. 비박스로 가서 웹페이지 소스 확인해보기

* 터미널을 열고

1. `cd /var/www/bWAPP`

2. `gedit htmli_get.php`

![image](https://user-images.githubusercontent.com/28076542/51434494-150f0c00-1ca5-11e9-8224-fc0e8f700f68.png)

```php
include("security.php");
include("security_level_check.php");
include("functions_external.php");
include("selections.php");

function htmli($data)
{
    switch($_COOKIE["security_level"])
    {
        case "0" :
            $data = no_check($data);
            break;
        case "1" :
            $data = xss_check_1($data);
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

* security level에 따라 분기되는 것을 알 수 있음
* 이러한 정보는 `functions_external.php`에 있음
* ctrl+z를 누르고 bg 눌러서 백그라운드로 편집기를 실행시키고 터미널에 `gedit functions_external.php` 입력

### 2. functions_external.php 소스코드 확인

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
```

### 3. 코드의 취약점 확인

```php
 // Bypasses double encoding attacks
 // <script>alert(0)</script>
 // %3Cscript%3Ealert%280%29%3C%2Fscript%3E
 // %253Cscript%253Ealert%25280%2529%253C%252Fscript%253E
 $input = urldecode($input);
```

* `< >`를 보내지 않고 인코딩한 값을 보내면 디코딩을 해줌
* [html Decoder/Encoder](https://meyerweb.com/eric/tools/dencoder/)
* [get에 URL 인코딩/디코딩이 필요한 이유](http://blog.naver.com/PostView.nhn?blogId=awesomedad&logNo=220748168859)
* 다음과 같이 인코딩하고 삽입하면 medium 레벨 통과

![image](https://user-images.githubusercontent.com/28076542/51435248-b18dda00-1cb6-11e9-87c6-6717a33c66ab.png)

### medium 헷갈리는 부분

1. 클라이언트에서 서버에게 Get을 보낼 때 자동으로 인코딩을 하는가? --> 그렇다.

2. 서버는 클라이언트의 Get 메시지를 자동으로 디코딩 하는가? --> 그렇다.

3. medium에서 두 번 인코딩했는데 이게 무슨 의미인가? --> 서버의 php 소스코드에 디코딩 함수가 **굳이** 존재한다. 원래 서버는 클라이언트의 메시지를 자동적으로 디코딩하는데 한 번 더 디코딩 한 것으로 두 번 디코딩했다. 평문을 디코딩하면 그대로 평문이기 때문에 클라이언트에 스크립트가 전송될 수 있었던 것이다.

4. 정리해봐라

억지 인코딩 메시지 --> 억지 인코딩 메시지를 자동으로 인코딩 --> 서버에서 자동으로 메시지 디코딩 --> php에 **굳이** 존재하는 디코딩 함수 덕에 한 번 더 디코딩 --> 평문 스크립트 메시지 클라이언트에 도달

## high 레벨로 가서 확인하기

```php
include("security.php");
include("security_level_check.php");
include("functions_external.php");
include("selections.php");

function htmli($data)
{
    switch($_COOKIE["security_level"])
    {
        case "0" :
            $data = no_check($data);
            break;
        case "1" :
            $data = xss_check_1($data);
            break;
        case "2" :
            $data = xss_check_3($data); // high 레벨
            break;
        default :
            $data = no_check($data);
            break;
    }
    return $data;
}
```

```php
function xss_check_3($data, $encoding = "UTF-8")
{
    // htmlspecialchars - converts special characters to HTML entities
    // '&' (ampersand) becomes '&amp;
    // '"' (double quote) becomes '&quot;' when ENT_NOQUOTES is not set
    // "'" (single quote) becomes '&#039;' (or &apos;) only when ENT_QUOTES is set
    // '<' (less than) becomes '&lt;'
    // '>' (greater than) becomes '&gt;'
    return htmlspecialchars($data, ENT_QUOTES, $encoding);
}
```

* low와 medium에서 했던 것 둘 다 통하지 않음
* **htmlspecialchars** 함수는 php에서 제공하는 기본 함수로 HTML에서 사용하는 특수문자를 UTF=8로 변환시켜 줌 --> 이게 바로 대응 방안
* 그런데 **htmlspecialchars**도 우회가 가능하다고 하는데 다음에 살펴보자...

---

## HTML 인젝션 Reflected(POST)

* post는 get과 달리 헤더로 전달되지 않고 body를 통해 전달
* 민감한 데이터나 대량의 파일은 post 방식으로 전달
* Get이랑 똑같이 하면 low와 medium이 통과됨

## Current URL은 책 보세요 ^^

## HTML 인젝션 Stored(Blog)

* low는 상자 내용 안에 Get에서 했던 짓 똑같이하면 됨

```html
<form action="/bWAPP/htmli_post.php" method="POST"> 
  <p><label for="firstname">First name:</label><br> 
  <input type="text" id="firstname" name="firstname"></p> 
  <p><label for="lastname">Last name:</label><br> 
  <input type="text" id="lastname" name="lastname"></p> 
  <button type="submit" name="form" value="submit">Go</button>
</form>
```

* 이 코드를 삽입하면 다음과 같이 화면이 뜸

![image](https://user-images.githubusercontent.com/28076542/51435651-7d6ae700-1cbf-11e9-88f8-6527a66f3b6e.png)

* high는 안 됨
* Stored는 Reflected와 방식이 다름

![image](https://user-images.githubusercontent.com/28076542/51435625-db4aff00-1cbe-11e9-9135-8310ed0e26ad.png)

* 여러 사람이 보는 게시판에 악의적인 코드를 넣어 사용자의 정보를 탈취할 수 있음
* low와 high에서 차이가 발생하는 이유를 보기 위해 **htmli_stored.php**를 보면 다음과 같이 다름

```php
while($row = $recordset->fetch_object())
{

    if($_COOKIE["security_level"] == "1" or $_COOKIE["security_level"] == "2")
    {
?>
        <tr height="40">
            <td align="center"><?php echo $row->id; ?></td>
            <td><?php echo $row->owner; ?></td>
            <td><?php echo $row->date; ?></td>
            <td><?php echo xss_check_3($row->entry); ?></td>
        </tr>
```

* **php echo xss_check_3($row->entry);**로 저장한 것을 불러올 때 확인