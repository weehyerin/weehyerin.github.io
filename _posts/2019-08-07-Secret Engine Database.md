---
layout: post
title: "Secret Engine Database"
excerpt_separator:  <!--more-->
categories:
- Vault
tags:
- Vault
- Hashicorp
- security
---

<!--more-->

# Secret Engine Database

플러그인 인터페이스를 통해 여러 다른 데이터베이스를 작동. 
확장 성을 위해 사용자 정의 데이터베이스 유형을 실행하기 위한 여러 내장 데이터베이스 유형 및 노출 된 프레임워크가 있다. 
**즉, 데이터베이스에 access해야하는 서비스는 더 이상 자격 증명을 하드 코딩 할 필요가 없다.**
Vault에서 자격 증명을 요청하고 Vault의 임대 메커니즘을 사용하여 키를 보다 쉽게 ​​롤백 할 수 있다.

모든 서비스는 고유 한 자격 증명을 사용하여 데이터베이스에 access하므로 의심스러운 데이터 access가 발견되면 훨씬 쉽게 검사 할 수 있다.(SQL 사용자 이름을 기반으로 특정 서비스 인스턴스로 추적 가능.)


----------


# Static Role

데이터베이스 secret engine 은 "정적 역할"이라는 개념을 지원.
* 데이터베이스의 사용자 이름에 일대일로 매핑. 
* 데이터베이스 사용자의 현재 비밀번호는 구성 가능한 기간 동안 Vault에 의해 저장되고 자동으로 회전. 

**역할에 대한 자격 증명이 요청되면 Vault는 구성된 데이터베이스 사용자의 현재 비밀번호를 반환하므로 적절한 Vault 정책을 가진 사람은 누구나 데이터베이스의 사용자 계정에 액세스 할 수 있다.**

## SetUp

1. Enable the database secrets engine

~~~
vault secrets enable database
~~~
![스크린샷 2019-08-07 오후 12 04 18](https://user-images.githubusercontent.com/37536415/62591743-98274500-b90b-11e9-9f16-83b770c90e69.png)

2. 적절한 플러그인 및 연결 정보를 사용하여 Vault를 구성.
~~~

~~~