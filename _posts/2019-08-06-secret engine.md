---
layout: post
title: "secret engine"
excerpt_separator:  <!--more-->
categories:
- Vault
tags:
- Vault
- Hashicorp
- security
---

<!--more-->

## what is secret engine?


**Vault는 가상 파일 시스템과 비슷하게 작동.** 

읽기 / 쓰기 / 삭제 / 목록 작업은 해당 비밀 엔진으로 전달되고 비밀 엔진은 이러한 작업에 대응하는 방법을 결정.

**이 추상화는 Vault가 물리적 시스템, 데이터베이스, HSM 등과 직접 인터페이스 할 수 있도록 만든다. <br> 추가적으로 Vault는 동일한 읽기/쓰기 인터페이스를 사용하면서 AWS IAM, 동적 SQL 사용자 생성 등과 같은 고유한 환경과 상호 작용할 수 있다.**



******


## 오류 발생

~~~
vault write foo/bar a=b
~~~

결과 : 
<img width="429" alt="스크린샷 2019-08-05 오후 4 53 16" src="https://user-images.githubusercontent.com/37536415/62448010-a35e6180-b7a1-11e9-9214-29367a8a4b23.png">

path prefix는 Vault에게 트래픽을 라우팅해야하는 엔진의 비밀을 알려준다. 

> **요청 Vault에 도달 --> 가장 길게 일치되는 초기 경로 -->  해당 경로에서 활성화 된 해당 비밀 엔진으로 요청을 전달**

>> 기본적으로 Vault는 secret secret / 경로에서 kv라는 비밀 엔진을 사용. 
>> kv secret 엔진은 원시 데이터를 읽고 백엔드 스토리지에 씀


Vault는 kv 외에 다른 많은 비밀 엔진을 지원하며 이 기능은 Vault를 유연하고 독창적으로 만든다. 

**이 페이지는 비밀 엔진과 해당 엔진이 지원하는 작업에 대해 설명.**



******



## secret engine 사용하기

> **kv secret 엔진의 다른 인스턴스를 다른 경로에서 활성화.**

파일 시스템과 마찬가지로 Vault는 다양한 경로에서 비밀 엔진을 사용할 수 있다. 

각 경로는 완전히 격리되어 있으며 다른 경로와 대화 할 수 없다. --> 예를 들어, foo에서 활성화 된 kv 비밀 엔진은 bar에서 활성화 된 kv 비밀 엔진과 통신 할 수 없다.

**비밀 엔진이 사용되는 경로의 기본값은 비밀 엔진의 이름. (아래 두 개의 명령어 중 아무거나 써도 됨)**
~~~
vault secrets enable -path=kv kv

or 

vault secrets enable kv
~~~

<img width="515" alt="스크린샷 2019-08-05 오후 5 00 39" src="https://user-images.githubusercontent.com/37536415/62448408-b6256600-b7a2-11e9-883d-e3e36bae621c.png">

**비밀 엔진이 사용되는 경로의 기본값은 비밀 엔진의 이름. (두 개의 명령어 중 아무거나 써도 됨)**



******



## secret engine 만들기가 잘 되었는 지 확인하기 

~~~
vault secrets list
~~~

<img width="568" alt="스크린샷 2019-08-05 오후 5 05 09" src="https://user-images.githubusercontent.com/37536415/62448602-33e97180-b7a3-11e9-8edb-432aa9a0534d.png">

**Vault 서버에 5 개의 활성화 된 비밀 엔진이 있음을 보여줌.**


### 활성화 된 새로운 kv 비밀 엔진에 데이터를 읽고 써보기

~~~

$ vault write kv/my-secret value="s3c(eT"
Success! Data written to: kv/my-secret

$ vault write kv/hello target=world
Success! Data written to: kv/hello

$ vault write kv/airplane type=boeing class=787
Success! Data written to: kv/airplane

$ vault list kv
Keys
----
airplane
hello
my-secret

~~~



******



## disable secret engine

> 더 이상 필요하지 않으면 비활성화 할 수 있음 <br> secret engine이 비활성화되면 모든 secret이 취소되고 해당 vault 데이터 및 구성이 제거. 

<img width="447" alt="스크린샷 2019-08-05 오후 5 14 42" src="https://user-images.githubusercontent.com/37536415/62449143-8b3c1180-b7a4-11e9-872a-a330c999c3fa.png">
















