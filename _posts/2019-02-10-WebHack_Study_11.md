---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - 세션 관리"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
  - Part2 A2
  - 인증 결함과 세션 관리
---

<!--more-->

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 13강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

## 세션 관리

* 웹 사이트에서 서비스를 제공할 때 사용자의 로그인을 유지하기 위하여 사용
* 실제로 가장 많이 도출되는 취약점 중 하나가 세션 관리 미흡에서 발생
* 관리자 페이지 접근에 대한 세션 처리 미흡으로 많은 취약점 도출
* 특히 개발자가 중간에 교체되거나 유지보수하며 추가된 페이지
* 정상적인 프로세스가 진행되는 과정에서 발생 --> 자동 진단 도구/특정한 패턴 매칭을 도출할 수 없는 취약점
* 기존의 아이디와 패스워드가 아닌 다른 방식을 사용

---

## Session Mgmt - Adminisgtrative Portals(Low)

![image](https://user-images.githubusercontent.com/28076542/52530184-90178f80-2d44-11e9-83e0-9b631cb3d628.png)

* URL: `http://192.168.190.143/bWAPP/smgmt_admin_portal.php?admin=0`
* **admin=0**이니 **admin=1**으로 바꾸면 페이지가 변경됨

![image](https://user-images.githubusercontent.com/28076542/52530209-c6550f00-2d44-11e9-86d5-23baa01df122.png)

---

## Session Mgmt - Adminisgtrative Portals(Medium)

![image](https://user-images.githubusercontent.com/28076542/52530215-db31a280-2d44-11e9-9e0b-c7f1a2811d17.png)

* 쿠키를 확인하라는 힌트가 있음
* BurpSuite로 쿠키를 확인

![image](https://user-images.githubusercontent.com/28076542/52530224-fa303480-2d44-11e9-8240-dda4d9278e7b.png)

* **admin=1**로 바꾸면 페이지가 바뀜

![image](https://user-images.githubusercontent.com/28076542/52530229-116f2200-2d45-11e9-881d-3011e7a558fa.png)

---

## Session Mgmt - Adminisgtrative Portals(High)

* 현재 접속하는 계정 bee는 관리자 계정으로 들어가면 바로 unlocked가 되어있다고 함
* 새로 계정을 생성하여 들어가야 됨

![image](https://user-images.githubusercontent.com/28076542/52530241-66129d00-2d45-11e9-81ca-31775c028bcb.png)

![image](https://user-images.githubusercontent.com/28076542/52530254-95c1a500-2d45-11e9-933a-4f3d2a07deae.png)

* dba의 도움을 받아서 이 페이지를 풀라는 말
* dba는 데이터베이스의 관리자로 일반 사용자는 데이터베이스의 값을 변경하지 못하고 오직 데이터베이스의 관리자만 바꿀 수 있어 안전함

---

## Session Mgmt - Session ID in URL

* 다시 관리자 계정 bee로 로그인 후 접속

![image](https://user-images.githubusercontent.com/28076542/52530277-0bc60c00-2d46-11e9-9139-aebd6a044402.png)

* URL: `http://192.168.190.143/bWAPP/smgmt_sessionid_url.php?PHPSESSID=cf15ee7d335fba26f39d64443da3b7b6`
* 위 **PHPSESSID**는 관리자 계정 bee의 쿠키값
* 일반 계정으로 접속하고 위 세션 아이디를 쿠키 값으로 설정하면 관리자 계정으로 접근 가능
* **꿀팁: EditThisCookie를 크롬 확장자로 설치할 것 ㅎ__ㅎ**(칼리 리눅스가 아닌 윈도우의 크롬으로 이용하였음)

![image](https://user-images.githubusercontent.com/28076542/52530313-a3c3f580-2d46-11e9-908a-414a786d2875.png)

![image](https://user-images.githubusercontent.com/28076542/52530374-049ffd80-2d48-11e9-826d-373ea9aa97f9.png)

* 이 페이지에서 URL에서 노출된 관리자 세션 아이디`cf15ee7d335fba26f39d64443da3b7b6`를 EditThisCookie로 쿠키 값에 삽입

![image](https://user-images.githubusercontent.com/28076542/52530379-26998000-2d48-11e9-8077-557c97f95b72.png)

![image](https://user-images.githubusercontent.com/28076542/52530405-74ae8380-2d48-11e9-8f6d-a2ce263775c1.png)

* 다시 접속하면 unlock 되어 있음