---
layout: post
title: "Deploy Vault"
excerpt_separator:  <!--more-->
categories:
- Vault
tags:
- Vault
- Hashicorp
- security
---

<!--more-->

지금까지 자동적으로 인증하는 "dev" 서버, 메모리 내 저장장치 설정 등과 함께 작업해 왔다. 
Vault를 실제 환경에 구축하는 방법을 알아볼 예정

******

## Configuring Vault

1. config.hcl 파일 생성
~~~
vi config.hcl
~~~

2. 아래 내용 붙여 넣기
~~~
storage "consul" {
  address = "127.0.0.1:8500"
  path    = "vault/"
}

listener "tcp" {
 address     = "127.0.0.1:8200"
 tls_disable = 1
}
~~~

`storage` - Vault가 저장소에 사용하는 실제 백엔드입니다. 이때까지 dev 서버는 "인메모리"(메모리)를 사용했지만, 백엔드인 Consul을 사용한다.

`listener` - 하나 이상의 수신기가 Vault가 API 요청을 수신하는 방법을 결정한다. 위의 예는 TLS 없이 localhost 포트 8200에서 수신된다. 환경에서 VAULT_ADDR=http://127.0.0.1:8200을 설정하여 TLS 없이 Vault 클라이언트를 연결하기

3. install consul
[install consul](https://learn.hashicorp.com/consul/getting-started/install.html)

위의 url에서 consul을 다운로드 받은 후, vault와 같이 export path 명령어로 경로에 넣어주기



한 개의 terminal 창에서 아래 시행
~~~
consul agent -dev
~~~

다른 터미널에서 아래 시행
~~~
vault server -config=config.hcl
~~~

제대로 됐다면, 

아래와 같은 결과가 나옴. 
1. <br>
<img width="568" alt="스크린샷 2019-08-06 오후 3 21 28" src="https://user-images.githubusercontent.com/37536415/62515666-f0077280-b85d-11e9-93f4-52d316da53ed.png">

2. <br>
<img width="567" alt="스크린샷 2019-08-06 오후 3 21 10" src="https://user-images.githubusercontent.com/37536415/62515665-ef6edc00-b85d-11e9-8b30-bf88cff37846.png">





******


## 다시 시작하기 - 새로운 terminal
1. server 다시 시작
~~~
$ vault server -dev
~~~

2. client 시작
~~~
$ export VAULT_ADDR='http://127.0.0.1:8200'

$ export VAULT_DEV_ROOT_TOKEN_ID="s.7Npjx4Ta32VtTFotIDEfPqvS"

$ vault status
~~~



client에서 `vault operator unseal` 입력하고, 서버를 실행하면서 알아낸 unseal 입력
1. <br>
<img width="542" alt="스크린샷 2019-08-06 오후 3 31 10" src="https://user-images.githubusercontent.com/37536415/62516196-5b057900-b85f-11e9-8f4c-7f280ded7441.png">
2. <br>
<img width="439" alt="스크린샷 2019-08-06 오후 3 31 19" src="https://user-images.githubusercontent.com/37536415/62516197-5b057900-b85f-11e9-8808-921c96a62102.png">



초기화된 모든 Vault 서버는 unsealing(밀봉된) 상태에서 시작한다. 

구성에서 Vault는 실제 저장소에 액세스할 수 있지만 암호를 해독하는 방법을 모르기 때문에 해당 저장소를 읽을 수 없다. 

Vault에서 데이터의 암호를 해독하는 방법을 가르치는 프로세스는 `unsealing` 이다.

Vault의 잠금을 해제하려면 실행 중지 키의 임계값 수가 있어야 한다. 

<img width="650" alt="스크린샷 2019-08-06 오후 3 38 07" src="https://user-images.githubusercontent.com/37536415/62516514-47a6dd80-b860-11e9-985f-f3ef716df46a.png">
위의 사진에서 Vault의 잠금을 해제하려면 생성된 5개의 키 중 3개가 필요하다.














