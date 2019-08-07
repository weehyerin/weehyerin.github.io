---
layout: post
title: "Secret Engine Details"
excerpt_separator:  <!--more-->
categories:
- Vault
tags:
- Vault
- Hashicorp
- security
---

<!--more-->

# What is Secret Engine

Secret Engine : 데이터를 저장, 생성 또는 암호화하는 구성 요소. 일부 데이터 세트가 제공되며 해당 데이터에 대해 조치를 취하고 결과를 리턴.

일부 Secret Engine은 단순히 암호화 된 Redis / Memcached와 같은 데이터를 저장하고 읽습니다. 
Secret Engine은 다른 서비스에 연결하고 요청시 동적 자격 증명을 생성하며 암호화, totp 생성, 인증서 등을 제공합니다.

> Vault의 "경로"에서 secret engine을 사용할 수 있다.<br>
> * 요청이 Vault에 도착 --> 라우터는 경로가 있는 항목을 seret engine으로 자동 라우팅. <br>
>
>   * 이러한 방식으로 각 secret engine은 고유 한 경로와 속성을 정의


-------------


## Secrets Engines Lifecycle 

secret engine은 CLI 또는 API를 통해 활성화, 비활성화, 조정 및 이동 가능.


> `Enable` - 지정된 경로에서 secret engine을 활성화. secret engine은 여러 경로에서 사용할 수 있음. 각 secret engine은 경로와 격리되어 있다.
> 
> `Disalbe` - 기존 secret engine을 비활성화. secret engine이 비활성화되면 모든 secret이 취소, 해당 엔진에 저장된 모든 데이터 삭제.
> 
> `Move` - 기존 secret engine의 경로를 이동. 비밀 임대가 생성 된 경로에 연결되어 있기 때문에 이 프로세스를 시행하면 모든 secret을 취소함. 엔진에 저장된 구성 데이터는 이동을 통해 지속됨.
> 
> `Tune` - TTL과 같은 secret engine의 글로벌 구성을 조정합니다.

secret engine이 활성화되면 자체 API에 따라 경로에서 직접 엔진과 상호 작용할 수 있다.





-------------


## Barrier View


secret engine은 구성된 Vault 물리적 storage에 대한 Barrier view를 받는다. 

> secret engine이 활성화 되면 임의의 UUID가 생성. --> 이것이 해당 엔진의 데이터 루트가 됨. 
> 
> 해당 엔진이 실제 스토리지 계층에 쓸 때마다 해당 UUID 폴더가 접두어로 붙음. 
> --> Vault 스토리지 계층은 상대 액세스 (예 : ../)를 지원하지 않기 때문에 활성화 된 secret engine이 다른 데이터에 액세스 할 수 없습니다.

**악의적인 엔진이라도 다른 엔진의 데이터에 액세스 할 수 없다.**

#### Barrier View == Chroot과 비슷

##### chroot : 현재 실행중인 프로세스 및 해당 자식의 루트 디렉토리를 변경하는 작업. 

>> 이러한 수정 된 환경에서 실행되는 프로그램은 지정된 디렉토리 트리 외부의 파일 이름을 지정할 수 없으므로 일반적으로 액세스 할 수 없다.