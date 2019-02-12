---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - 인증 결함"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part2 A2
  - 인증 결함과 세션 관리
---

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 12강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

<!--more-->

## 인증 결함

* 인증에 필요한 사용자의 계정 정보를 노출하는 취약점
* HTML 코드, GET 요청, URL 변수 노출
* 취약한 암호 설정
* 취약한 인증 과정

![image](https://user-images.githubusercontent.com/28076542/52519800-3eb7c380-2ca4-11e9-877d-43f95e60419c.png)

---

## Broken Auth - Insecure Login Forms(Low)

![image](https://user-images.githubusercontent.com/28076542/52519820-8d655d80-2ca4-11e9-8aa7-48a3dfc42950.png)

* 소스코드를 보면 다음과 같음

```html
<div id="main">

    <h1>Broken Auth. - Insecure Login Forms</h1>

    <p>Enter your credentials.</p>

    <form action="/bWAPP/ba_insecure_login_1.php" method="POST">

        <p><label for="login">Login:</label><font color="white">tonystark</font><br />
        <input type="text" id="login" name="login" size="20" /></p> 

        <p><label for="password">Password:</label><font color="white">I am Iron Man</font><br />
        <input type="password" id="password" name="password" size="20" /></p>

        <button type="submit" name="form" value="submit">Login</button>  

    </form>

    </br >    
        
</div>
```

* id: **tonystark**, password: **I am Iron Man**이 써져있고 흰색 글자로 설정
* 황당한 경우로 이럴 경우는 거의 없다고 보면 될듯

![image](https://user-images.githubusercontent.com/28076542/52519835-e1704200-2ca4-11e9-8f0e-d7efe7f4568a.png)

---

## Broken Auth - Insecure Login Forms(Medium)

![image](https://user-images.githubusercontent.com/28076542/52519851-26947400-2ca5-11e9-8c7b-02e6d0720f32.png)

* id는 적혀있지만 패스워드는 적혀있지 않음
* 소스코드를 보면 다음과 같음

```html
<div id="main">    

    <h1>Broken Auth. - Insecure Login Forms</h1>

    <p>Enter the correct passphrase to unlock the secret.</p>

    <form>   

        <p><label for="name">Name:</label><font color="white">brucebanner</font><br />
        <input type="text" id="name" name="name" size="20" value="brucebanner" /></p> 

        <p><label for="passphrase">Passphrase:</label><br />
        <input type="password" id="passphrase" name="passphrase" size="20" /></p>

        <input type="button" name="button" value="Unlock" onclick="unlock_secret()" /><br />

    </form>

    </br >    
        
</div>
```

* 로그인을 하면 `unlock_secret()` 함수로 이동함을 알 수 있고 살펴보면 다음과 같음

```html
<script language="javascript">   

function unlock_secret()
{

    var bWAPP = "bash update killed my shells!"

    var a = bWAPP.charAt(0);  var d = bWAPP.charAt(3);  var r = bWAPP.charAt(16);
    var b = bWAPP.charAt(1);  var e = bWAPP.charAt(4);  var j = bWAPP.charAt(9);
    var c = bWAPP.charAt(2);  var f = bWAPP.charAt(5);  var g = bWAPP.charAt(4);
    var j = bWAPP.charAt(9);  var h = bWAPP.charAt(6);  var l = bWAPP.charAt(11);
    var g = bWAPP.charAt(4);  var i = bWAPP.charAt(7);  var x = bWAPP.charAt(4);
    var l = bWAPP.charAt(11); var p = bWAPP.charAt(23); var m = bWAPP.charAt(4);
    var s = bWAPP.charAt(17); var k = bWAPP.charAt(10); var d = bWAPP.charAt(23);
    var t = bWAPP.charAt(2);  var n = bWAPP.charAt(12); var e = bWAPP.charAt(4);
    var a = bWAPP.charAt(1);  var o = bWAPP.charAt(13); var f = bWAPP.charAt(5);
    var b = bWAPP.charAt(1);  var q = bWAPP.charAt(15); var h = bWAPP.charAt(9);
    var c = bWAPP.charAt(2);  var h = bWAPP.charAt(2);  var i = bWAPP.charAt(7);
    var j = bWAPP.charAt(5);  var i = bWAPP.charAt(7);  var y = bWAPP.charAt(22);
    var g = bWAPP.charAt(1);  var p = bWAPP.charAt(4);  var p = bWAPP.charAt(28);
    var l = bWAPP.charAt(11); var k = bWAPP.charAt(14);
    var q = bWAPP.charAt(12); var n = bWAPP.charAt(12);
    var m = bWAPP.charAt(4);  var o = bWAPP.charAt(19);

    var secret = (d + "" + j + "" + k + "" + q + "" + x + "" + t + "" +o + "" + g + "" + h + "" + d + "" + p);

    if(document.forms[0].passphrase.value == secret)
    { 
              
        // Unlocked
        location.href="/bWAPP/ba_insecure_login_2.php?secret=" + secret;

    }
    
    else
    {
        
        // Locked
        location.href="/bWAPP/ba_insecure_login_2.php?secret=";
                
    }

}	

</script>
```

* 암호문을 만들고 이를 일치하는지 안 하는지 확인하는 작업
* Unlocked이면 `"/bWAPP/ba_insecure_login_2.php?secret="` Locked이면 `"/bWAPP/ba_insecure_login_2.php?secret="`로 이동
* 크롬 개발자 도구에서 자바스크립트 콘솔을 이용

![image](https://user-images.githubusercontent.com/28076542/52519927-79226000-2ca6-11e9-9ee7-46158f32df8b.png)

---

## Broken Auth - Weak Passwords(Low)

![image](https://user-images.githubusercontent.com/28076542/52519940-b4249380-2ca6-11e9-870c-b11aa57087f9.png)

### Burp Suite로 Intruder 실행

![image](https://user-images.githubusercontent.com/28076542/52519983-3d3bca80-2ca7-11e9-9089-ab42dea31de0.png)

* id: bee, password: bug 입력하였음

![image](https://user-images.githubusercontent.com/28076542/52520012-7116f000-2ca7-11e9-9872-a0afe0c1bed5.png)

* Sniper: 한 개만 집중 공격
* clear를 누르고 bug를 드래그 한 후 Add 버튼 클릭
* Payload 탭을 누르고 Brute forcer 타입 선택

![image](https://user-images.githubusercontent.com/28076542/52520044-e4b8fd00-2ca7-11e9-976e-f76cfa3186d3.png)

* Options에서 스레드 개수 설정도 할 수 있는데 무료여서 못함
* Start attack을 누르면 진행을 하는데 오래 걸리니 한셈 치고 넘어감

![image](https://user-images.githubusercontent.com/28076542/52520064-26e23e80-2ca8-11e9-9a99-a37be17b634f.png)

* Brute Force 말고 사전 공격을 할 수 있는데 이를 위해 사전 파일이 필요함
* [사전파일](https://github.com/fuzzdb-project/fuzzdb/blob/master/wordlists-user-passwd/passwds/phpbb.txt)
* 칼리리눅스에서 `wget https://raw.githubusercontent.com/fuzzdb-project/fuzzdb/master/wordlists-user-passwd/passwds/phpbb.txt` 실행

![image](https://user-images.githubusercontent.com/28076542/52520260-07004a00-2cab-11e9-93cb-61ba458ec199.png)

![image](https://user-images.githubusercontent.com/28076542/52520299-64949680-2cab-11e9-98bc-c5bf2850f74f.png)

* 이것도 오래걸리니 한셈 침

---

## Broken Auth - Passwords Attack(High)

![image](https://user-images.githubusercontent.com/28076542/52520351-0ddb8c80-2cac-11e9-86f8-30ba13b17a2d.png)

* 캡차가 걸려있는데 이를 확인해보면 다음과 같음

```php
include("security.php");

include("security_level_check.php");

include("functions_external.php");



$captcha = random_string();

$_SESSION["captcha"] = $captcha;



// Creates the canvas

// Creates a new image

// image = imagecreate(233, 49);



// Creates the canvas

// Creates an image from a existing image

$image = imagecreatefrompng("images/captcha.png");



// Sets up some colors for use on the canvas

$white = imagecolorallocate($image, 255, 255, 255);

$black = imagecolorallocate($image, 0, 0, 0);

$orange = imagecolorallocate($image, 222, 77, 14);



// Loads a font (GDF)

// $font = imageloadfont("fonts/atommicclock.gdf");



// Loads a font (TTF)

$font = "fonts/arial.ttf";



// Writes the string (GDF)

// imagestring($image, $font, 0, 0, $captcha, $orange);

// imagestring($image, $font, 40, 10, $captcha, $black);



// Writes the string (TTF)

// imagettftext($image, $size, $angle, $x, $y, $color, $fontfile, $text);

imagettftext($image, 20, 0, 75, 38, $orange, $font, $captcha);

        

// Output the image to the browser

header ("Content-type: image/png");

imagepng($image);



// Cleans up after yourself

imagedestroy($image)



?>
```

* `$captcha = random_string();`로 랜덤하여 한 번에 맞추기 힘듦

---

## Broken Auth - Weak Passwords(High)

* Low와 다르게 비밀번호를 길게 하고 대소문자에 특수문자 숫자를 넣게 하여 뚫기 힘듦
* 이와같이 패스워드가 약한지 아닌지 확인하는 사이트가 있음 [How Secure is My Password?](https://howsecureismypassword.net/)