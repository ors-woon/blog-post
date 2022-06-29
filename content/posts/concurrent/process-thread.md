+++ 
author = "chulwoon.jang" 
title = "스레드와 프로세스 정리" 
date = "2021-09-25" 
description = "스레드와 프로세스에 대한 간단한 정리" 
tags = ["OŚ","CS","Concurrent"] 
series = ["CS"] 
categories = ["CS"] 
summary = " "
+++

[자바 병렬 프로그래밍](http://www.yes24.com/Product/Goods/3015162) 책을 읽기 전, 간단하게 프로세스와 스레드에 대한 개념을 정리하고자합니다.
개인적으로 잊지 않기 위해 정리하는 것으로, 보다 정돈된 글은 [프로세스와 스레드의 차이](https://velog.io/@raejoonee/%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4%EC%99%80-%EC%8A%A4%EB%A0%88%EB%93%9C%EC%9D%98-%EC%B0%A8%EC%9D%B4)를 보셔도 좋을거 같습니다.

## 한줄 정리 

`Process`란 운영 체제로 부터 자원을 할당받은 작업의 단위입니다.   
`Thread`란 프로세스가 할당받은 자원을 이용하는 실행 흐름 단위입니다.

`Process`는 OS로 부터 메모리 자원을 할당받아 실행되며, 최소 1개의 main Thread를 갖게 됩니다. 
할당받은 자원은 다른 스레드에서 `직접 접근`이 불가능하다는 특징을 갖고 있습니다.
`Thread`는 Process 내에서 실행되며, 일부 Process의 자원을 공유합니다. 

## Process 메모리 자원 

Process 가 OS 로 부터 할당받은 자원은 크게 4가지(`Heap`, `Stack`, `Code`, `Data`) 로 나뉘어지며, 다른 프로세스에서 접근할 수 없습니다.

<center>
    <img src="/images/process-area.png">
</center>

위처럼 Process들은 독립된 자원을 이용하여 기능을 수행하게됩니다. 다만, 서로 독립된 자원이기에, OS 에서 Process 스케줄링 수행 시, OverHead 가 존재하게 됩니다.

> 일반 사용자에겐 여러 프로세스가 동시에 실행되는것 처럼 보이나, 사실은 짧은 시간 동안 서로 cpu를 점유하여 기능을 수행하고 있습니다.

이러한 Overhead (`Contetx Switching`)를 줄이기 위해 Thread 를 사용하게되는데요.

Thread 는 앞서 말했듯, Process 의 자원(`Heap`, `Data`, `Code`)을 공유합니다.

<center>
    <img src="/images/thread.png">
</center>

Process의 일부 자원을 공유하기에, Context Switching 시 보다 적은 OverHead를 갖지만 동기화 이슈가 발생할 수 있다는 단점이 있습니다.

또한 프로세스끼리는 서로 격리되기에 오류 발생 시, 다른 프로세스 영향을 줄 가능성이 낮지만, 스레드는 자원을 공유하기에, 오류 발생 시 다른 스레드에 영향을 줄 수 있습니다.

## Context Swtching 

여러 프로그램 실행 시, 사용자에게는 동시에 실행하는 것처럼 보이나, 실제로는 프로세스들이 순차적으로 cpu를 점유하면서 실행되고 있습니다.

때문에 OS서는 프로세스의 cpu 할당 순서 및 방법을 결정하여, 스케줄링하고 있는데요.

> 장기, 중기, 단기 스케줄링이 있다 정도로만 기억하고 있습니다.

프로세스를 실행하면 cpu 레지스터에 관련 데이터를 올려두어 사용하며, 다른 프로세스 실행 시, 이전 데이터(`프로세스를 이어서 실행하기 위함`)를 메모리에 저장하여 관리합니다.

즉, 현재 실행 중인 프로세스의 데이터는 레지스터에 올려두고, 이전에 실행한 프로세스의 데이터들은 메모리에 저장하게 됩니다. cpu는 이전에 실행한적있는 프로세스라면 메모리로부터 데이터를 레지스터에 적재시키는 작업을합니다. 
이 작업을 context switching 이라 말하며, 이때 cpu는 다른 작업을 처리하지못합니다. 때문에 보다 많은 Overhead 가 발생하게됩니다. 

위같은 이유로, 복수개의 Process 보단, 복수개의 Thread 로 프로그램을 운영합니다.

