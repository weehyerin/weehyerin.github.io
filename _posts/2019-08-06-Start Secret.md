---
layout: post
title: "Start Secret"
excerpt_separator:  <!--more-->
categories:
- Vault
tags:
- Vault
- Hashicorp
- security
---

<!--more-->

## Start - CLI를 사용하여 임의의 비밀 정보를 안전하게 읽고 쓰기

**Vault의 핵심 기능 중 하나는 임의의 비밀 정보를 안전하게 읽고 쓸 수 있다는 것.**

*****

Vault에 기록 된 비밀 키는 암호화 된 다음 백엔드 저장소에 기록
dev 서버의 경우, 백엔드 스토리지는 메모리에 있지만 프로덕션 환경에서는 디스크에 있음.
저장소는 저장 장치 드라이버에 전달되기 전에 값을 암호화 한다. 
백엔드 저장 메커니즘은 Vault로 암호를 해독해야만 한다.

*******

## 예제

- foo = world 쌍을 secret / hello 경로에 쓰기. 
- 경로 앞에 secret /가 붙는 것이 중요. secret / : 임의의 secret을 읽고 쓸 수있는 곳.

client 쪽에서 작성
~~~
vault kv put secret/hello foo=world
~~~

**결과** 
: 
<img width="329" alt="스크린샷 2019-08-05 오후 4 33 27" src="https://user-images.githubusercontent.com/37536415/62446839-c63b4680-b79e-11e9-81fd-99cdabfad0c7.png">


여러 개의 데이터도 쓸 수 있음

~~~
vault kv put secret/hello foo=world excited=yes
~~~~

**명령어**
~~~
vault kv put
~~~

- 명령 행에서 직접 데이터를 쓰는 것 외에도 STDIN과 파일에서 값과 키 쌍을 읽을 수 있다. 
- command 명령어에 대한 설명 : https://www.vaultproject.io/docs/commands/index.html



******

 ## 위에서 저장한 값 가져오기
 
 ~~~
 vault kv get secret/hello
 ~~~
<img width="455" alt="스크린샷 2019-08-05 오후 4 37 40" src="https://user-images.githubusercontent.com/37536415/62447044-6002f380-b79f-11e9-9bef-4e87e59e65c4.png">

> **Vault는 저장소에서 데이터를 가져 와서 해독**

+ 추가 정보 제공
secret 엔진은 다른 시스템에 대한 시간제한 접근을 허용하는 비밀에 대한 lease를 생성하며, 
lease_id는 식별자를 포함하며 리스가 유효한 기간(초)을 포함

******

## json query를 사용하여, 비밀 값 추출 예제

~~~
vault kv get -field=excited secret/hello
~~~
> 결과 : <img width="552" alt="스크린샷 2019-08-05 오후 4 45 00" src="https://user-images.githubusercontent.com/37536415/62447496-778eac00-b7a0-11e9-920e-d2a705a456c9.png">

~~~
vault kv get -format=json secret/hello
~~~
> 결과 : <img width="538" alt="스크린샷 2019-08-05 오후 4 45 07" src="https://user-images.githubusercontent.com/37536415/62447497-78274280-b7a0-11e9-912e-1c642a6d292d.png">


*****

## 아까 저장한 내용 지우기 

~~~
vault kv delete secret/hello
~~~

<img width="520" alt="스크린샷 2019-08-05 오후 4 46 27" src="https://user-images.githubusercontent.com/37536415/62447539-95f4a780-b7a0-11e9-9608-0dad427d25fc.png">

다시 get으로 확인해보면, 삭제된 것을 확인할 수 있음
+ created, deletion time을 확인 할 수 있음

<img width="444" alt="스크린샷 2019-08-05 오후 4 47 11" src="https://user-images.githubusercontent.com/37536415/62447662-d48a6200-b7a0-11e9-9ca5-009f515c7306.png">




