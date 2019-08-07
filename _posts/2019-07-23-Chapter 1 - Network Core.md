---
layout: post
title: "Chapter 1 - Network Core"
excerpt_separator:  <!--more-->
categories:
 - Computer Network
tags:
  - computer
  - network
  - computer network
  - network core
---

<!--more-->

# network core
*****
> 가장 자리에 있는 host 끼리 상호 연결될 수 있도록 grobal 하게 연결하고 싶어함<br>
> 그래서 **network core**에 router가 그물망 처럼 연결되어 있음
>> 이렇게 함으로써
>> - data를 신뢰성있게 보낼 수 있다
>> - 한 곳의 연결이 끊기더라도 다른 길로 갈 수 있다.
*******
## packet switching
> packet 마다 목적지 판단해서 보냄

## store and forward
> 보내는 쪽에서 데이터 한 덩어리로 전달하는 것이 아니라, packet으로 한 조각씩 잘라서 보냄 <br><br> 받는 쪽도 한 조각씩 받아서 application에 올리기 전에 한 번에 모아서 처리함.<br><br> 즉, 물흐르듯이 흘러가는게 아님. packet 마다 갈 길이 정해져있고, network core에 위치한 router가 forwarding을 함 <br> --> 받는 쪽에서 packet을 저장(store)한 다음 합쳐줌 + router가 forwarding을 함

> packet에 실어 보내는 data 양은 다 다를 수 있으나, 최대 실어보낼 수 있는 data의 양은 정해져 있음.(최대 실어서 보낼 수 있는 데이터의 양에 따라 처음 보내야 하는 데이터를 잘라서 보냄.)

- L : 최대 실어서 보낼 수 있는 데이터의 양
- R : 초당 몇 bit를 다른 곳에 보낼 수 있느냐(One-hop마다 속도는 다 다른 수 있음. 다 다른 link layer(광캐이블, 구리 캐이블 등 다 다르기 때문))
> One - hop : 현재 packet을 가지고 있는 곳부터 다음 router(또는 host)까지
<img width="1344" alt="스크린샷 2019-07-24 오후 5 28 36" src="https://user-images.githubusercontent.com/37536415/61777911-c64f5400-ae38-11e9-8b56-68527e2937c3.png">

*******
## queing delay
#### packet 도착 --> queue에 도착한 pakcet들을 대기 --> 도착한 애들 하나씩 내보냄
#### 용량을 초과하면, 버려지는 상황 발생 : packet loss(최악의 상황)
#### queue에 너무 많은 애가 쌓이면, 다시 내보낼 때, Queueing delay 가 심해짐
*******
## forwarding
**data를 갖고 있는 녀석 부터 필요로 하는 애까지 보내주는 역할**

## routing
**목적지에 대해서 어느 경로로 내보내는 것이 최선인가**
> 경로 결정 : 어떤 목적지로 가는데 최선의 경로 결정
> forwarding : routing으로 결정된 것을 근거로 하여 내보내줌

## forwarding table
> 각 router마다 forwarding table을 가지고 있음
> routing protocol에 의해서, forwarding table이 만들어지고 이것을 근거로 forwarding 하는 것
#### forwarding table을 어떻게 만들까?
> 각 router들이 소문을 냄. **나와 누가 연결 되어있는지** router들이 누가 어느 목적지에 연결되어 있는 지 앎
> 서로 어떤 것과 연결되어 있는 지를 알고 path algorithm으로 최선의 경로 찾아냄. 
>> 블로그 뒤에서 다시 설명할 예정
<br><br><br>
*******
## circuit switching
> 보통 전화할 떄 사용
> 전화하는 동안 circuit(회로)이 끊어지면 안됨.
> 중간에 아무도 관여하지 못함.
> 물 흐르는 듯한 전송
> 그 길은 나만 쓰고 다른 사람이 못씀
>> 길을 독점해서 자원을 낭비하는 시간이 생길 수 있음
>> 전화는 계속 말하기 때문에 자원을 독점하는 것은 문제가 되지 않음
>> 하지만, data load와 같이 간헐적으로 자원을 원할 때, 한 곳에서 자원을 독점하면 문제가 생김
<br><br>
### circuit switching의 종류
여러 사람이 회로를 같이 쓸 수 있어야 함. 따라서 어떻게 공유할 것인가.

#### FDM
신호를 보낼 때, 명확히 구분할 수 있는 대역폭으로 신호를 나눠서 보냄. <br>
신호가 섞이지 않는 frequency(대역폭)이 4개라면, 같은 회로를 4명이 쓸 수 있음
* 즉, 사용자 수가 제한적임.

#### TDM

******
