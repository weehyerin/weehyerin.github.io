---
layout: post
title: "비박스를 활용한 웹 모의해킹 완벽 실습 - 오리엔테이션"
excerpt_separator:  <!--more-->
categories:
 - WebHack
tags:
  - 웹 해킹
  - 인프런
---

<!--more-->

# 출처

## 인프런, 비박스를 활용한 웹 모의해킹 완벽 실습 1~2강 <https://www.inflearn.com/course/%EB%B9%84%EB%B0%95%EC%8A%A4-%EB%AA%A8%EC%9D%98%ED%95%B4%ED%82%B9-%EC%8B%A4%EC%8A%B5/>

---

# 오리엔테이션

# 1강

## 웹 애플리케이션 취약점 진단

* 여러 가지 프로그램이 동작하는 웹 환경에 대해 취약점을 진단하기 위해 필요함
* 보안 분야에서 웹을 먼저 공부하는 것을 추천 --> 소스코드가 보이기 때문에 분석과 시큐어 코딩을 해볼 수 있음

## Bee-Box

* 웹 포트를 열어 놓은 웹 서버
* 공격자 입장에서 이 서버를 공격해보는 것이 목적
* 오픈 소스이며 내용 소스도 확인 가능하며 설치도 쉬움
* 어떻게 안전한 코드를 짤 것인지에 대한 대응 방안도 확인하기 쉬움
* 100가지가 넘는 다양한 취약점을 가지고 있음(2014년까지의 취약점이 있어 나름 최신 취약점도 존재)
* 레벨별로 나뉘어져 있음(Low, Medium, High)
* 책 비박스 환경을 활용한 웹 모의해킹 완벽 실습 기반의 강의

## bWAPP

* Bee-Box 환경(우분투 환경)에 존재하는 애플리케이션
* **아이디: bee, 비밀번호: bug**
* 취약점은 OWASP TOP 10으로 분류되어 있음

## OWASP TOP 10

* Open Source Application Security Project의 약자로 웹 애플리케이션의 취약점을 연구하는 표준 기구

## 점검 도구

* Burp Suite: 프록시 도구
* SQLmap: SQL 인젝션 자동화 도구
* MetasploitL: 침투 테스트
* User Agent Switcher: User Agent 변경
* SQLmap ~ User Agent Switcher는 칼리리눅스를 이용함

### Burp Suite

![image](https://user-images.githubusercontent.com/28076542/51426175-046d8000-1c2a-11e9-9803-56633feb2043.png)

### SQLmap

* 데이터베이스는 데이터를 관리하는데 이 때 질의(Query)과정이 이루어짐
* SQL에는 이러한 과정이 있는데 SQLmap은 이러한 질의 과정에 일어날 수 있는 취약점 진단 도구

### Metasploit

* 취약점 공격을 자동화하는 도구

### User Agent Switcher

* 하나의 서버에서 여러 웹의 요청들을 처리하는데 알맞은 응답을 처리하기 위해 처리하기 위해 user Agent를 확인함
* 예를 들어 스마트폰으로 웹을 들어가면 모바일 사이트로 들어가는데 이 이유가 바로 user agent를 확인하기 때문
* User agent는 바꿀 수 있음

---

# 2강

## 프로그램 설치

* 칼리리눅스, 가상머신(vmware 아니면 virtualbox, 나는 vmware), Bee-Box
* 이미 설치함

## 환경 구성

### Bee-Box

* Bee-Box 네트워크 설정은 NAT 설정
* Bee-Box에서 키보드 입력이 이상하면 Bel --> Key Preference --> Layout --> Add --> Korean
* 터미널에서 `ifconfig`로 아이피 확인

### 칼리리눅스

* 터미널 열기 
* `msfconsole -h` --> 메타스플로잇 설치 확인
* `burpsuite` --> BurpSuite 설치 확인
* `sqlmap -h` --> SQL map 설치 확인
* `ifconfig` --> 아이피 확인
* iceweasel 혹은 파이어폭스 실행하기
* Bee-Box에서 확인한 아이피 주소로 접속

---