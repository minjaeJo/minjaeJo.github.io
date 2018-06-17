---
layout: post
title: "궁금한 웹 해킹, 8가지만 알아보자! (1)브루트 포스"
date: 2018-05-29
tags: [모의해킹]
excerpt: ""
comments: true
---

# 궁금한 웹 해킹, 8가지만 알아보자! (1) - 브루트 포스

## 개요

브루트 포스(brute-force) 공격은 가능한 모든 암호 조합을 무차별적으로 대입하는 해킹 기법이다. 간단히 말해서 암호 입력 노다가를 논리적으로 설명 가능한 기법이다. 쉽게 예를 들어보자. 도둑이 집을 침입하려고 한다. 이때 집 비밀번호가 4자리 이면 그 4자리의 경우의 수 256개를 다 치는 것이다. 즉, 충분한 시간만 주어진다면 이 도둑은 비밀번호를 입력할 수 있을 것이다. 이번 포스트는 도둑처럼 무식하지만 아주 간단한 해킹기법으로 홈페이지 로그인을 목표로 한다.

## 원리

<img style="width:500px" src="https://steemitimages.com/0x0/https://i.imgur.com/BD5o5KY.png">
브루트포스를 만드는 방법은 매우 간단하다.<br/>
1부터 100까지 차례대로 더해 나가면서 값을 바꾸면 된다.
컴퓨터는 사람들보다는 계산이 빠를 테고, 100까지 다 더하는 데는 시간도 얼마 안 걸릴 것이다. 하지만, 브루트 포스의 문제점은 **숫자가 커지면서** 발생한다. 시간복잡도가 급격히 상승하게 되고 비효율의 끝을 볼 수 있다.

만약 1부터 100까지가 아닌, 1부터 1000억까지 더한다고 해 보자.

> 일반적으로 컴퓨터는 1초에 약 1억 번의 연산이 가능하다.

물론 컴퓨터마다, 프로그래밍 언어마다, 주어진 상황마다 크게 달라질 것이다.
그래도 앞으로는 1초에 1억 번 연산이라고 가정해보자.
그렇다면, 저 문제를 브루트 포스로 해결한다면
**1000초 = 약 17분**이나 걸리게 된다.
단순 더하기 문제인데 말이다.

브루트 포스를 뉴스 기사 같은 곳에서 접하신 분들도 있으실 것이다.
`*** 사이트, 브루트 포스 공격에 비밀번호 유출되어` 같은 기사 말이다.
그렇다면, 저 사이트가 얼마나 문제가 심각한 것인지 알 수 있다.

이 원리는 다음의 Post를 참고하여 표시하였다.
혹시 자세하게 알고 싶다면, 아래의 링크를 이용하면 되겠다.
[[간단 알고리즘] 2. 모두 다 해본다 - 브루트 포스(Brute Force) — Steemkr](https://steemkr.com/kr-dev/@gyeryak/easyalgo-2-bruteforce)

## 문제점

브루트포스는 문제가 조금만 복잡해져도 시간복잡도가 매우 크게 증가하는 치명적인 단점을 지니고 있다. 특히 경우의 수가 문제의 복잡도에 따라 기하급수적으로 증가하는 경우, 문제를 해결하는 데에 필요한 자원 역시 기하급수적으로 증가한다.

때문에 체스나 바둑과 같이 경우의 수가 많은 경우, 일반적으로 브루트포스를 쓰지 않고, AI나 알고리즘을 통해 보다 효율적으로 접근한다.

이 때문에 실제로 브루트 포스는 문제의 규모가 현재의 자원으로 충분히 커버가 가능한 경우에만 쓰이고, 대부분은 동적 계획법 등으로 많이 우회하는 편이다. 심지어는 정확도를 조금 희생해더라도 어떻게든 '이론상 가능한' 자원으로 해결할 수 있게 알고리즘을 설계하기도 한다. 후에 언급할 사전 공격 역시 정확도가 약간 희생된 것이다.

## 자동 브루트 포스 공격

`이 실습은 버프 스위트를 사용할 수 있다는 가정하에 진행한다.`

### 사전 준비

DVWA의 보안 단계를 row 단계로 내려 놓는다.

<img style="width:500px" src="https://raw.githubusercontent.com/minjaeJo/minjaeJo.github.io/master/assets/img/brute_forcer_00.png"><br/>

### 실행

1. 127.0.0.1/dvwa 접속 후, 로그인 시도
2. Proxy 탭의 HTTP history 하단 탭에서 프록시에 걸린 로그인 리퀘스트 선택
3. 해당 리퀘스트를 우측 클릭하여 `Send to Intruder`
4. Intruder 탭 이동 후, Positions 하단 탭 선택
5. Clear -> password 부분만 Add
6. 옆의 payloads 하단 탭 선택
7. Payload type을 `Brute forcer` 변경
8. Payload options에서 원하는 조건 값을 입력 후, start attack을 실행

<img style="width:500px" src="https://raw.githubusercontent.com/minjaeJo/minjaeJo.github.io/master/assets/img/brute_forcer_1.png"><br/>

<img style="width:500px" src="https://raw.githubusercontent.com/minjaeJo/minjaeJo.github.io/master/assets/img/brute_forcer_01.png"><br/>

<img style="width:500px" src="https://raw.githubusercontent.com/minjaeJo/minjaeJo.github.io/master/assets/img/brute_forcer_2.png"><br/>

## 딕셔너리 브루트 포스 공격

위의 정석적인 공격법은 아주 오랜 시간이 걸리기 때문에 사용자들이 빈도 높은 패스워드 딕셔너리만 돌려서 공격하는 방법이 효율적이라고 한다.

1. 버프 스위트의 Intruder > Payloads의 탭 선택
2. Payload type을 Simple list 선택
3. Payload Options의 Load 버튼 클릭 후 `/usr/share/john/password.lst` 경로 선택
4. Start attack

<img style="width:500px" src="https://raw.githubusercontent.com/minjaeJo/minjaeJo.github.io/master/assets/img/brute_forcer_3.png"><br/>

## 예방법

DVWA의 브루트 포스 공격에 대한 php 코드이다. 주석부분에 각 레벨 대응 방법을 설명해놓았다.

Medium 레벨에서는 sleep(2)를 준다. 로그인 실패를 했을 경우, 시간을 지연시키므로 브루트포스의 최대 시간을 늘리는 법이다. 하지만, 이 방법도 해커가 로그인 실패 지연 시간이 늘어나는 부분을 알게 된다면 충분히 우회할 수 있다.

High 레벨에서는 sleep(rand(0,3))를 준다. 로그인 지연 시간을 랜덤으로 주면서 알 수 없도록 한다.

Impossible 레벨에서는 여러번 로그인 실패시, 15분동안 계정을 막아버린다. 이렇게 되면 도저히 브루트 포스 공격을 쓰지 못한다. 물론 해커가 이를 노려 모든 계정을 막아버릴 수 있다. 이외에도 그림에 있는 숫자를 입력하는 캡챠를 쓰거나 특수문자를 포함 길게 늘려써서 막을 수 있다.


