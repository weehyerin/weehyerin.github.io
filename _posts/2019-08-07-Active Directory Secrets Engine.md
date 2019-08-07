---
layout: post
title: "Active Directory Secrets Engine"
excerpt_separator:  <!--more-->
categories:
- Vault
tags:
- Vault
- Hashicorp
- security
---

<!--more-->

# Active Directory Secrets Engine

**AD (Active Directory) Secrets Engine : Vault의 플러그인 중 하나**

AD secret engine은 **AD 암호를 많은 동적으로 rotation 하며**, 인스턴스가 공유 암호에 동시에 액세스 할 수 있도록 함.

간단한 설정과 간단한 creds API를 사용하면 액세스를 위해 인스턴스를 수동으로 미리 등록 할 필요가 없다. 

AppRole과 같은 방법을 통해 creds 경로에 대한 액세스 권한이 부여 된 경우 사용할 수 있다.



-------------



## Plugin System

모든 Vault 인증 및 비밀 백엔드는 플러그인으로 간주. --> 이 개념을 통해 내장 및 외부 플러그인을 모두 레고처럼 취급 할 수 있다. 
모든 플러그인은 여러 다른 위치에 존재할 수 있고, 각기 다른 버전의 플러그인이 있을 수 있으며 각 버전은 Vault의 버전과 다르다.

### Plugin Architecture

> Vault의 플러그인은 Vault가 RPC를 통해 실행하고 통신하는 완전히 분리 된 독립형 응용 프로그램. 
> 이는 플러그인 프로세스가 Vault와 동일한 메모리 공간을 공유하지 않으므로 해당 인터페이스 및 인수에만 액세스 할 수 있음을 의미 
> 또한 플러그인 충돌로 Vault 전체가 충돌 할 수 없다.

### Plugin Communication

Vault는 플러그인의 RPC 서버와 통신하기 위해 상호 인증 된 TLS 연결을 만든다. 

> 플러그인 프로세스를 호출하는 동안 Vault는 래핑 토큰을 플러그인 프로세스 환경으로 전달합니다. 이 토큰은 일회용이며 짧은 TTL이 있습니다. 랩핑을 해제하면 원래 Vault 프로세스와 대화하는 데 사용할 수 있도록 고유하게 생성 된 TLS 인증서 및 개인 키를 플러그인에 제공합니다.




-------------



## Lazy Rotation

<예시>
<상황>
> * 비밀번호는 1 시간의 TTL(Time To Live)로 구성
> * 이 암호를 사용하는 모든 서비스 인스턴스는 12 시간 동안 꺼져 있다.
> * 그런 다음 깨어나 암호를 다시 요청 

비밀번호 TTL이 1 시간으로 설정되어 있어 다음 요청시 비밀번호가 12 시간 동안 회전하지 않음. 
즉, "Lazy Rotation"은 아래 조건이 모두 충족 될 때 암호가 회전됨.
>
> * 그들은 그들의 TTL 위에있다
> * 요청된다

AD Secrets 엔진은 항상 ttl 값에 관계없이 암호를 회전시킵니다. 지연 회전에 대한 문서에 따르면 볼트는 ttl이 경과 한 후 암호를 회전 합니다. ttl이 통과되지 않은 경우에도 볼트가 비밀번호를 회전시킵니다.



-------------


## escaping 

제대로 escape 된 DN을 제공하는 것은 관리자의 책임. (여기에는 사용자 DN, 검색을 위한 bind DN 등이 포함).

이 방법으로 수행되는 유일한 DN escape는 최종 바인드 DN에 삽입 될 때 로그인시 지정된 사용자 이름에 대한 것이며 RFC 4514에 정의 된 escape 규칙을 사용.

> Active Directory에는 RFC와 약간 다른 escape 규칙이 있음. 
>> DN의 위치에 관계없이 '#'을 escape 처리해야함. 
>> RFC가 백 슬래시로 escape 할 수 있음을 나타내는 '=' 필수 escape 세트에 포함되지 않음.