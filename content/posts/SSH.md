+++ 
author = "chulwoon.jang" 
title = "ssh 동작 방식" 
date = "2020-03-14" 
description = "ssh 동작 방식에 대해 간단히 정리한글" 
tags = ["ssh"] 
series = ["Linux"] 
categories = ["Linux"] 
summary = " "
+++

# SSH Protocol

ssh 이란  `Secure Shell Protocol` 의 약자로,  FTP / Telnet 에 비해 **보안**에 초점을 맞춘  protocol 입니다. 

## 인증 방식

ssh 은 `비대칭키` 방식을 이용하여 사용자 인증을 진행합니다.  client / server 간의 통신 시 보안을 위해 packet을 암호화하는데, 이때 암호화/복호화의 수단으로 key 를 이용합니다.  

하나의 key로 암호화 / 복호화를 진행하는 방식을 `대칭키`,  하나의 key로 암호화한 내용을, 다른 key로 복호화 할 수 있는 방식을 `비대칭키`라 말합니다.

대칭키는 키 유출 시, client / server 간의 내용이 쉽게 노출될 수 있어 바로 사용되진 않으며,  비대칭키는 컴퓨터 자원을 많이 소모합니다. 때문에 ssh / https 는  비대칭키 / 대칭키를  모두 사용하는 방식을 이용합니다.

## 비대칭키

비대칭키란, public / private key 로 이루어진 인증방식입니다. private key 로 암호화한 내용을 public key로 복호화 할 수 있으며, 반대로 public key로 암호화한 내용을 private key로만 복호화가능한 방식을 의미합니다. 

> 한쪽의 key만 안다고해서 packet을 모두 복호화할 수 없습니다.

client 에서 private key를,  server 에서 public key를 저장하고 있는 상태에서 ssh 인증을 진행하게 됩니다.

<center>
    <img src="/images/ssh-key-auth-flow.png">
</center>


    1. client 는 server 에게 ssh connection 요청을 보냅니다.
    2. server 는 랜덤한 message 를 생성하여 저장합니다.
    그리고 이를 client에게 전송합니다.
    3. client 는 받은 message + 대칭키를 암호화(private key)하여
    server 에게 전송합니다.
    4. server 는 받은 packet을 복호화(public key)하여, 
    저장하고있던 값과 비교합니다. 값이 동일할 경우, 인증이 완료됩니다.


위 인증이 완료된 경우,  비대칭 키를 이용해, 대칭키를 서로 교환하게 됩니다.  이후 통신되는 모든 데이터는 대칭키를 통해 암호화가 이루어집니다.

## Linux 에서 ssh 설정

첫 번째로, `ssh-keygen` 명령어를 사용해, 공개키 / 비공개키를 생성합니다.

    ssh-keygen -t rsa 

현재 디렉터리에 id_rsa / id_rsa.pub 한쌍이 생성되며, `id_rsa` 가 비공개 키 `id_rsa.pub`  가 공개키로 id_rsa는 절대 유출되어서는 안됩니다. 

그후  공개키를 원하는 서버 `$HOME/.ssh` 경로에  `authorized_keys` 이라는 이름으로 생성하고 알맞은 권한을 설정하면 ssh 접근이 가능합니다. 

## 설정 요약

    1. client 에서   ssh-keygen -t rsa  를 입력한다.
    2. .ssh 경로에 생긴 id_rsa / id_rsa.pub 을 확인한다.
    3. id_rsa.pub 을 원하는 서버 $HOME/.ssh 에 authorized_keys 라는 이름으로 옮겨둔다.
    4. chmod 를 통해 알맞은 권한을 부여한다.
    5. client 에서 ssh 접속을 진행한다. (특정 계정으로 접근해야하는 경우, ssh user@host 로 접근한다.)
    
    끝  

> 또한 Linux 뿐만 아니라, github 등 여러 서비스에서도 ssh을 이용하여 인증을 진행하고 있습니다.