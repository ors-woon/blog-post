---
title: "Namespace Cgroup"
date: 2024-01-28T18:45:12+09:00
tags: []
draft: true
author: "ors"
description: "namespace-cgroup"
---

Kubernetes in Action 의 일부를 정리한 내용입니다.

---

guest OS(가상머신) 위에서 application 을 실행시키는 vm 과 달리, container 는 guest OS 를 사용하지 않고 Host OS 의 커널 시스템을 호출하여 Application 을 실행시킨다.

그렇다면, container 는 같은 Host OS 를 같이 사용하게 될텐데, container 끼리 어떻게 자원을 격리할까 ?

> 가상 머신을 사용 할 경우, guestOS 끼리 완전한 분리를 할 수 있다는 장점이 있으나 그만큼 오버헤드가 발생한다.

## Linux NameSpace (System Resource 분리)

NameSpace 란, 각 프로세스가 "**파일, 프로세스, 네트워크 인터페이스, 호스트 이름**" 등 시스템에 독립 뷰를 제공하는 매커니즘을 말한다.  
Linux system 은 초기 구동 시 하나의 nameSpace 를 갖으며, 이후 nameSpace 를 추가하여 리소스를 구성할 수 있다.

프로세스는 실행 시, NameSpace 중 하나에서 실행되며 동일한 NameSpace의 리소스만 참조할 수 있다.

여러 종류의 NameSpace 가 존재하며 프로세스는 각 종류의 NameSpace 에 속한다.

-   mnt (mount)
-   pid (process Id)
-   net (network)
-   ipc (Inter Process Communication)
-   UTS (host name)
-   user (user Id)

각 NameSpace 를 이용하여 Process 간 System Resource를 분리 할 수 있다.

## cgroup

NameSpace 가 process 간 사용 할 수 있는 resource 를 격리했다면, cgroup 은 해당 resource 사용을 제한하는 개념이다.  
process 는 cgroup 에 설정된 값 이상의 cpu / memory / network bandwitdh 등을 사용할 수 없다.

> 각 process가 별도의 시스템에서 실행되는 것과 유사하게 동작하게 된다.

linux container 기술들은 위 NameSpace / cgroup을 사용하여 process 간의 자원을 격리하고 있다.