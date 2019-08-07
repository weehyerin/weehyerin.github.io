---
layout: post
title: "Built-in Help"
excerpt_separator:  <!--more-->
categories:
- Vault
tags:
- Vault
- Hashicorp
- security
---

<!--more-->

## 도움말 시스템

AWS 백엔드에는 aws / config와 같은 특수 경로가 있는 것 처럼 각 비밀 엔진의 구조와 사용법이 다르다

**Vault는 사용할 경로를 결정하기 위해 문서를 지속적으로 암기하거나 참조 할 필요없이 내장 된 도움말 시스템을 갖추고 있다.**

API 또는 명령 줄을 통해 접근 할 수 있으며, 모든 경로에 대해 사람이 읽을 수 있는 도움말을 생성함.

>> AWS에 대한 도움말 시스템

~~~
vault path-help aws
~~~

**결과**
~~~
## DESCRIPTION

The AWS backend dynamically generates AWS access keys for a set of
IAM policies. The AWS access keys have a configurable lease set and
are automatically revoked at the end of the lease.

After mounting this backend, credentials to generate IAM keys must
be configured with the "root" path and policies must be written using
the "roles/" endpoints before any access keys can be generated.

## PATHS

The following paths are supported by this backend. To view help for
any of the paths below, use the help command with any route matching
the path pattern. Note that depending on the policy of your auth token,
you may or may not be able to access certain paths.

    ^(creds|sts)/(?P<name>\w(([\w-.]+)?\w)?)$
        Generate AWS credentials from a specific Vault role.

    ^config/lease$
        Configure the default lease information for generated credentials.

    ^config/root$
        Configure the root credentials that are used to manage IAM.

    ^config/rotate-root$
        Request to rotate the AWS credentials used by Vault

    ^roles/(?P<name>\w(([\w-.]+)?\w)?)$
        Read, write and reference IAM policies that access keys can be made for.

    ^roles/?$
        List the existing roles in this backend

~~~


## path help

aws / creds / my-non-existent-role에 액세스하기 위해 아래 도움말을 볼 수 있다.

~~~
vault path-help aws/creds/my-non-existent-role
~~~


<img width="566" alt="스크린샷 2019-08-06 오전 11 06 04" src="https://user-images.githubusercontent.com/37536415/62506089-3519ad80-b83a-11e9-8707-0676e2c8afaf.png">


이 경로에 필요한 매개 변수 제공, 일부 매개 변수는 경로 자체에서 가져옴. 

이 경우 name 매개 변수는 경로 정규 표현식에서 지어진 것. 

경로가 무엇인지에 대한 설명도 있다.


