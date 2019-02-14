---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - Base64 인코딩 복호화/HTML5 웹 저장소/중요 정보 텍스트 파일 저장"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part6 A6
  - 민감 데이터 노출
---

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 18강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

<!--more-->

## 민감 데이터 노출

* 클라이언트와 서버 통신 시 암호화 프로토콜(SSL)을 사용하여 중요한 정보를 보호
* 사용자의 민감한 정보 입력 시 암호화 후 저장
* 데이터 처리 및 암호화 저장은 서버 기반에서 실행
* 민감한 정보가 암호화되지 않거나 취약한 암호화 방식을 쓰는 예가 있음

![image](https://user-images.githubusercontent.com/28076542/52774890-b3627780-3081-11e9-9b75-e899e0ba5489.png)

---

## Base64 Encoding (Secret)

![image](https://user-images.githubusercontent.com/28076542/52775063-1bb15900-3082-11e9-81a6-e2e3747e66ea.png)

### BurpSuite로 확인한 Secret

![image](https://user-images.githubusercontent.com/28076542/52775110-3b488180-3082-11e9-8744-e7f709f69762.png)

* **secret=QW55IGJ1Z3M%2F**
* [Base64 decoder](https://www.base64decode.org/)
* Decode 결과: **Any bugs6**

## Base64 인코딩

* 8bit 데이터를 64진수 문자셋으로 변환하는 것
* `=`로 인코딩의 끝을 알림

![:----:](https://user-images.githubusercontent.com/28076542/52776801-b8c1c100-3085-11e9-80c2-f3563bfb0d60.png)

---

## Base64 Encoding (Secret) (Medium)

![image](https://user-images.githubusercontent.com/28076542/52777086-5b7a3f80-3086-11e9-9d56-c15f90c9489a.png)

* **secret=83785efbbef1b4cdf3260c5e6505f7d2261f738d**

### 칼리리눅스의 hash-identifer로 암호화 알고리즘 확인

```bash
root@ming:~# hash-identifier
   #########################################################################
   #	 __  __ 		    __		 ______    _____	   #
   #	/\ \/\ \		   /\ \ 	/\__  _\  /\  _ `\	   #
   #	\ \ \_\ \     __      ____ \ \ \___	\/_/\ \/  \ \ \/\ \	   #
   #	 \ \  _  \  /'__`\   / ,__\ \ \  _ `\	   \ \ \   \ \ \ \ \	   #
   #	  \ \ \ \ \/\ \_\ \_/\__, `\ \ \ \ \ \	    \_\ \__ \ \ \_\ \	   #
   #	   \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/	   #
   #	    \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.1 #
   #								 By Zion3R #
   #							www.Blackploit.com #
   #						       Root@Blackploit.com #
   #########################################################################

   -------------------------------------------------------------------------
 HASH: 83785efbbef1b4cdf3260c5e6505f7d2261f738d

Possible Hashs:
[+]  SHA-1
[+]  MySQL5 - SHA-1(SHA-1($pass))

Least Possible Hashs:
[+]  Tiger-160
[+]  Haval-160
[+]  RipeMD-160
[+]  SHA-1(HMAC)
[+]  Tiger-160(HMAC)
[+]  RipeMD-160(HMAC)
[+]  Haval-160(HMAC)
[+]  SHA-1(MaNGOS)
[+]  SHA-1(MaNGOS2)
[+]  sha1($pass.$salt)
[+]  sha1($salt.$pass)
[+]  sha1($salt.md5($pass))
[+]  sha1($salt.md5($pass).$salt)
[+]  sha1($salt.sha1($pass))
[+]  sha1($salt.sha1($salt.sha1($pass)))
[+]  sha1($username.$pass)
[+]  sha1($username.$pass.$salt)
[+]  sha1(md5($pass))
[+]  sha1(md5($pass).$salt)
[+]  sha1(md5(sha1($pass)))
[+]  sha1(sha1($pass))
[+]  sha1(sha1($pass).$salt)
[+]  sha1(sha1($pass).substr($pass,0,3))
[+]  sha1(sha1($salt.$pass))
[+]  sha1(sha1(sha1($pass)))
[+]  sha1(strtolower($username).$pass)
```

* SHA-1으로 암호화되어 있음
* [SHA-1 Decoder](https://hashkiller.co.uk/sha1-decrypter.aspx)
* Decode 결과: Any bugs?

---

## Base64 Encoding (Secret) (High)

![image](https://user-images.githubusercontent.com/28076542/52777650-a5aff080-3087-11e9-8963-847683c012e9.png)

* **secret=83785efbbef1b4cdf3260c5e6505f7d2261f738d**
* Medium이랑 secret 문자열이 같아 결국 High도 취약함을 알 수 있음

---

## HTML5 Web Storage (Secret)

![image](https://user-images.githubusercontent.com/28076542/52778267-fd9b2700-3088-11e9-9ff5-c2d90bfaa670.png)

### HTML5 Web Storage의 페이지 소스

```html
<script>

if(typeof(Storage) !== "undefined")
{

    localStorage.login = "bee";
    localStorage.secret = "Any bugs?";

}
else
{

    alert("Sorry, your browser does not support web storage...");

}

</script>
```

* 스크립트를 사용해서 로컬 스토리지에 있는 값을 가져오라는 것

> HTML5 Storage는 로컬에서 데이터를 저장하는데 쿠키보다 더 큰 양을 저장하고 서버에 데이터를 보내지 않는다. 쿠키는 document.write(document.cookie)를 XSS로 나타나게 할 수 있었지만 HTML5 Storage에 저장된 값은 다른 스크립트가 필요하다.

### HTML5 Storage를 불러오는 스크립트

```javascript
<script>
try {
  var result = "";
  for(Var key in localStorage) {
    result += key + "=" + localStorage.getItem(key) + ";";
  }
  alert(result);
}

catch(error) {
  alert(error.message);
}
</script>

<script>
for (var key in localStorage) {
  document.write(key + " : " + localStorage[key] + "<br>");
}
</script>
```

* 이 스크립트를 **XSS - Reflected (Get)** 입력 칸에 입력

### XSS - Reflected (Get)에 스크립트 입력 결과

![image](https://user-images.githubusercontent.com/28076542/52778940-7ea6ee00-308a-11e9-955f-13ae0a48348c.png)

---

## HTML5 Web Storage (Secret) (High)

* Low와 마찬가지 작업을 하면 secret이 나오는데 SHA-1으로 해시하였기 때문에 문제가 있음

---

## Text Files (Accounts)

![image](https://user-images.githubusercontent.com/28076542/52780008-abf49b80-308c-11e9-9ebb-4ba3022e559e.png)

* bee와 bug를 입력하고 다운로드 파일을 누르면 bee와 bug가 입력된 창이 뜨게 됨
* **Medium**에서는 bee와 해시 값이 나타남
* 칼리리눅스에 확인하면 이 해시도 똑같이 SHA-1임
* **High**에서는 bee와 해시 값이 나타나는데 이를 칼리리눅스에서 확인하면 SHA-256임
* **salt**가 있어서 미리 계산되어 해시 값과 비교하는 **rainbow table**로 해시를 맞출 수가 없음

### Text Files (Accounts) (High)에서 bee와 bug를 입력했을 때 나타나는 값

![image](https://user-images.githubusercontent.com/28076542/52780274-27eee380-308d-11e9-9558-e49fe8408a63.png)

```bash
'bee', '87da9d0c5c399e35ab68b30e409738d7839644fd55da966a068f32d4cf69952d', 'salt:b5e09011547a133af46d3543e5c31099'
```

```bash
root@ming:~# hash-identifier
   #########################################################################
   #	 __  __ 		    __		 ______    _____	   #
   #	/\ \/\ \		   /\ \ 	/\__  _\  /\  _ `\	   #
   #	\ \ \_\ \     __      ____ \ \ \___	\/_/\ \/  \ \ \/\ \	   #
   #	 \ \  _  \  /'__`\   / ,__\ \ \  _ `\	   \ \ \   \ \ \ \ \	   #
   #	  \ \ \ \ \/\ \_\ \_/\__, `\ \ \ \ \ \	    \_\ \__ \ \ \_\ \	   #
   #	   \ \_\ \_\ \___ \_\/\____/  \ \_\ \_\     /\_____\ \ \____/	   #
   #	    \/_/\/_/\/__/\/_/\/___/    \/_/\/_/     \/_____/  \/___/  v1.1 #
   #								 By Zion3R #
   #							www.Blackploit.com #
   #						       Root@Blackploit.com #
   #########################################################################

   -------------------------------------------------------------------------
 HASH: 87da9d0c5c399e35ab68b30e409738d7839644fd55da966a068f32d4cf69952d

Possible Hashs:
[+]  SHA-256
[+]  Haval-256

Least Possible Hashs:
[+]  GOST R 34.11-94
[+]  RipeMD-256
[+]  SNEFRU-256
[+]  SHA-256(HMAC)
[+]  Haval-256(HMAC)
[+]  RipeMD-256(HMAC)
[+]  SNEFRU-256(HMAC)
[+]  SHA-256(md5($pass))
[+]  SHA-256(sha1($pass))

   -------------------------------------------------------------------------
```

### John the Ripper로 Text Files (Accounts) (High)의 해시를 맞춰보기

```bash
root@ming:~# hashid -j 87da9d0c5c399e35ab68b30e409738d7839644fd55da966a068f32d4cf69952d
Analyzing '87da9d0c5c399e35ab68b30e409738d7839644fd55da966a068f32d4cf69952d'
[+] Snefru-256 [JtR Format: snefru-256]
[+] SHA-256 [JtR Format: raw-sha256]
[+] RIPEMD-256 
[+] Haval-256 [JtR Format: haval-256-3]
[+] GOST R 34.11-94 [JtR Format: gost]
[+] GOST CryptoPro S-Box 
[+] SHA3-256 [JtR Format: raw-keccak-256]
[+] Skein-256 [JtR Format: skein-256]
[+] Skein-512(256) 
root@ming:~# cat > passwd.txt
87da9d0c5c399e35ab68b30e409738d7839644fd55da966a068f32d4cf69952d
root@ming:~# john --format:raw-sha256 ./passwd.txt --salt:b5e09011547a133af46d3543e5c31099
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA256 [SHA256 128/128 AVX 4x])
Press 'q' or Ctrl-C to abort, almost any other key for status
```

> `cat > 파일명` 엔터 후 내용 입력 후 `ctrl+d`하면 터미널로 파일 내용 채우면서 파일 쓰기가 가능하다.