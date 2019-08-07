---
layout: post
title: "Authentication"
excerpt_separator:  <!--more-->
categories:
- Vault
tags:
- Vault
- Hashicorp
- security
---

<!--more-->

## Vault 자체를 인증하는 방법을 이해하기

지금까지는 Vault에 로그인하지 않았습니다. 개발자 모드에서 Vault 서버를 시작하면 관리자 권한을 가진 루트 사용자로 자동 로그인됩니다. 

`dev` 설치 프로그램에서는 먼저 인증해야합니다.

인증(Authentication)은 Vault 사용자에게 ID를 할당하는 메커니즘. 

이 페이지에서는 토큰 인증 방법과 GitHub 인증 방법을 사용합니다.

********

## Background


인증은 사용자 또는 시스템 제공 정보를 확인하고 Vault 토큰으로 변환하는 프로세스

사용자가 웹 사이트를 인증하면 사용자 이름, 비밀번호 및 2FA 코드가 입력. 
--> 이 정보는 외부 소스 (대부분의 데이터베이스)에 대해 확인되며 웹 사이트는 성공 또는 실패로 응답. 
--> 성공하면 웹 사이트는 해당 사용자를 고유하게 식별하는 세션 ID가 포함 된 서명 된 쿠키 반환. 
--> 쿠키와 세션 ID는 사용자가 인증 할 수 있도록 브라우저에 의해 자동으로 향후 요청에 전달. 

> 너무 복잡함, Vault는 표준 웹 사이트보다 훨씬 유연

**Vault는 모두 우리가 `Vault token`이라고 부르는 하나의 `session token`으로 깔린다.**

인증은 단순히 사용자나 기계가 Vault 토큰을 얻는 과정이다.



*******



## Token

Vault에서는 토큰 인증이 기본적으로 활성화되어 있으며 비활성화 할 수 없다. 

Vault Server -dev로 dev 서버를 시작하면 루트 토큰이 인쇄됩니다. 루트 토큰은 Vault를 구성하는 초기 액세스 토큰입니다. 

<<서버르 처음 실행할 때, root token이 나옴.>>
<img width="571" alt="스크린샷 2019-08-06 오전 11 32 17" src="https://user-images.githubusercontent.com/37536415/62507052-ec63f380-b83d-11e9-8270-7c4535a2032a.png">

루트 권한이 있으므로 Vault 내에서 모든 작업을 수행 할 수 있다.



*******



## Token 생성하기

~~~
vault token create
~~~

<img width="404" alt="스크린샷 2019-08-06 오전 11 34 57" src="https://user-images.githubusercontent.com/37536415/62507156-4f558a80-b83e-11e9-97a1-93444db18f4b.png">


> **"자식"개념**

토큰에는 항상 부모가 있으며 해당 부모 토큰이 취소되면 한 번의 작업으로 자식도 모두 취소 할 수 있다. 
이를 통해 사용자에 대한 액세스를 제거하고 사용자가 작성한 모든 하위 토큰에 대한 액세스를 쉽게 제거 할 수 있다.


~~~
vault login s.<token create 하면서 생성된 key value 값>
~~~

<img width="529" alt="스크린샷 2019-08-06 오전 11 44 38" src="https://user-images.githubusercontent.com/37536415/62507517-a9a31b00-b83f-11e9-8b4d-a6b32eb8ef9a.png">


이것은 Vault로 인증된다. 토큰을 확인하고 토큰과 관련된 액세스 정책을 알려준다.


********


## 토큰 해지


~~~
vault token revoke  <token key value>
~~~

<img width="569" alt="스크린샷 2019-08-06 오전 11 48 19" src="https://user-images.githubusercontent.com/37536415/62507717-35b54280-b840-11e9-89f7-a6147ce63d80.png">


## 토큰을 이용하여 다시 로그인

~~~
vault login $VAULT_DEV_ROOT_TOKEN_ID
~~~

<img width="543" alt="스크린샷 2019-08-06 오전 11 51 26" src="https://user-images.githubusercontent.com/37536415/62507851-993f7000-b840-11e9-84d6-400243067346.png">



********

 
 
## 권장 패턴

실제로 운영자는 토큰 생성 명령을 사용하여 사용자나 시스템의 Vault 토큰을 생성해서는 안 된다. 
대신 이러한 사용자 또는 시스템은 GitHub, LDAP, AppRole 등과 같이 Vault의 구성된 인증 방법을 사용하여 Vault에 인증해야 한다. 

## 인증 방법

Vault를 사용하기 전에 실행해야 한다.
인증 방법 실행 및 구성은 일반적으로 Vault 운영자 또는 보안 팀에 의해 수행된다.

## GitHub을 통해 인증

1. GitHub 인증 방법을 활성화

~~~
vault auth enable -path=github github
~~~

루트 라우터에서 활성화된 secret engine과 달리, 인증 방법은 항상 경로에 auth/가 붙는다. 
그래서 우리가 방금 활성화한 GitHub 인증 방법은 auth/github에서 접근할 수 있다.

~~~
vault path-help github
~~~
이렇게 찾으면 오류 남.


~~~
vault path-help auth/github
~~~
<img width="525" alt="스크린샷 2019-08-06 오후 12 02 22" src="https://user-images.githubusercontent.com/37536415/62508292-115a6580-b842-11e9-955b-62ccf0992714.png">


2. GitHub 인증 방법을 사용하여 다음에 조직 사용자가 어떤 조직 사용자의 일부가 되어야 하는지 설명하고 팀을 정책에 매핑하기.


  - Vault가 GitHub의 "hashicorp" 조직에서 인증 데이터를 가져오도록 구성한다. 
  
  ~~~
  vault write auth/github/config organization=hashicorp
  ~~~
  
  
  - Vault에게 팀원 "my-team"(hashicorp 조직에 속한)을 "default" 및 "my-policy" 정책에 매핑. 
  
  ~~~
  vault write auth/github/map/teams/my-team value=default,my-polic
  ~~~

결과 확인 : `vault auth list`

<img width="571" alt="스크린샷 2019-08-06 오후 12 08 45" src="https://user-images.githubusercontent.com/37536415/62508537-f63c2580-b842-11e9-86ec-5bdaee71ea8f.png">


 -- CLI를 통해 특정 인증 메서드에 인증하는 방법에 대한 자세한 내용 알아보기 : `vault auth help github`
 
 
 ## git login 사용하기 
 
 1. github homepage 들어가기
 <img width="571" alt="스크린샷 2019-08-06 오후 12 08 45" src="https://user-images.githubusercontent.com/37536415/62508537-f63c2580-b842-11e9-86ec-5bdaee71ea8f.png">
 
 2. setting 클릭
 <img width="1122" alt="스크린샷 2019-08-06 오후 12 59 00" src="https://user-images.githubusercontent.com/37536415/62510514-32bf4f80-b84a-11e9-9652-6f58ded41a9a.png">

 3. developer setting 클릭
 <img width="1121" alt="스크린샷 2019-08-06 오후 12 59 12" src="https://user-images.githubusercontent.com/37536415/62510517-33f07c80-b84a-11e9-8159-dcb64b59d57e.png">

 4. personal token 클릭
 <img width="1117" alt="스크린샷 2019-08-06 오후 1 02 50" src="https://user-images.githubusercontent.com/37536415/62510705-1ff94a80-b84b-11e9-99cd-13d3ebb13730.png">

 5. generate new token 클릭
 <img width="1122" alt="스크린샷 2019-08-06 오후 1 06 12" src="https://user-images.githubusercontent.com/37536415/62510708-225ba480-b84b-11e9-8956-c8e8141ee740.png">
  
  -- repo, admin:repo_hook, delete_repo 클릭 후 `generate token` 클릭
 
 > 생성 완료
 <img width="1121" alt="스크린샷 2019-08-06 오후 1 10 46" src="https://user-images.githubusercontent.com/37536415/62510836-aada4500-b84b-11e9-9154-a1bf5aa7ac19.png">
 
 ~~~
 vault login -method=github
 ~~~
 
 ~~~
 GitHub Personal Access Token (will be hidden): <위에서 알아낸 personal access token 입력>
 ~~~

 <img width="538" alt="스크린샷 2019-08-06 오후 1 49 11" src="https://user-images.githubusercontent.com/37536415/62511982-0c50e280-b851-11e9-9e52-f248894c72b6.png">
 
출력에 표시된 것처럼 Vault는 token helper에 결과 토큰을 이미 저장했으므로 볼트 로그인을 다시 실행할 필요가 없다. 
그러나 방금 만든 이 새 사용자는 Vault에서 많은 권한을 가지고 있지 않다.
 
 *****
 
 -- root token으로 다시 인증하기
 
 ~~~
 vault login <initial-root-token, 아까 서버를 실행하며 알아둔 root token 입력 s.>
 ~~~
 
 -- 취소하기
 
 ~~~
 vault token revoke -mode path auth/github
 ~~~
 
  -- GitHub 인증 방법을 완전히 사용하지 않도록 설정
  
  ~~~
  vault auth disable github
  ~~~
 








