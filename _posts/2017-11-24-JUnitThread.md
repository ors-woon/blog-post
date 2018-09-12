---
layout: post
title:  JUnit Thread Test 에 대하여
tags: JUnit 
categories: JUnit
---   


 
####  [스프링캠프 2017 [Day2 A4] : Reactive Programming with RxJava ](https://www.youtube.com/watch?v=0zVwXszDk88)를 보면서...

Rx에 대한 컨셉이나 Java에서 이를 어떻게 활용하는지, 혹은 기본적인 개념들을 알고싶어서 Spring Camp에서 진행한 세미나 영상을 보며 학습을 진행했습니다.   

> RX에 대해 전혀 모른 상태에서 봤을때는 비동기의 문제점과 Blocking에 대한 내용 외엔 이해하기가 어려웠습니다...   

그래서 RX는 추후 개인적으로 학습하기로 하고, 영상을 보면서 새로 알게된 내용+ 삽질(?)내용을 정리해보겠습니다.

#### JUnit 에서의 Thread Test   

	@Test
	public void contextLoads() {
		
		Executor es = Executors.newFixedThreadPool(1);
		log.info("Start");
		es.execute(()->{
			log.info("IN");
			try {
				Thread.sleep(1000L);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			log.info("OUT");
		});
		log.info("END");
	}
위 코드는 단순히 Thread를 하나 만든 뒤, Runnable로 log를 찍는 TestCase입니다.  위 코드를 작성했을 때, 예상되는 outPut으로는  ...   

```
 [main] INFO com.package.blabla - Start
 [main] INFO com.package.blabla - END
 [pool-1-thread-1] INFO com.package.blabla - IN
 [pool-1-thread-1] INFO com.package.blabla - OUT
```

입니다. 

**Start**가 찍히고, Context Switching의 이유로 Thread의 **IN** 출력보다, **END** 가 먼저 출력된 후,  **OUT**이 찍히는 걸 예상하게 됩니다. 다만 실제로 코드를 돌려보면 조금 다른 결과를 볼 수 있습니다. 

```
 [main] INFO com.package.blabla - Start
 [main] INFO com.package.blabla - END
 [pool-1-thread-1] INFO com.package.blabla - IN
```

**OUT 찍히지 않습니다.**  

위 예제에서는 코드를 단순히하기 위해 Sleep을 걸었지만, Sleep과 유사한 동작, 즉 1초 이상 걸리는 로직을 동작시킬때 JUnit을 사용하면 Thread가 모두 동작을 끝마치지 않았음에도 함수가 끝날 수도 있습니다.  

왜 그럴까 .. 싶어서 JUnit에 대한 ~구글링~을 진행했고 해답을 얻을 수 있었습니다.   

```
Java의 UserThread 와 DaemonThread ! 
```

#### Thread 종류 

Thread 종류에 대해 예전 OS를 학습할때 간략하게 들은적이 있습니다. 

```
0. Thread에는 Kernel/User로 나뉜다.   
1. Kernel Thread 인 경우 User / Kernle 모드 전환이 빈번할 경우 Overhead가 발생한다.
2. User Thread는 Kernel의 존재를 모른체 User영역에서 돌다가, Kernel의해 Blocking 될 경우 전체 프로세스가 Blocking 될 수 있다. 
```

정도로 학습한 적이 있는데, Daemon Thread에 대해서는 들어본적이 없었습니다.  그래서 관련 부분들을 찾아 봤는데 Java에서 제공하는 Thread 종류(?)이더군요.

간단하게 정리하면, Java Thread는 UserThread/DaemonThread가 있고  DaemonThread는  UserThread를 보조하는 역할을 합니다. 이때 중요한점은 UserThread가 모두 종료되면, 남아있는 DaemonThread는 작업완료 여부와는 상관없이, 종료하게 됩니다.

이 부분을 통해 한가지 추측을 할 수 있었습니다.

>  가설1.  Junit의 내부에 생성된 Thread는 DaemonThread이며, 위 코드에서 UserThread가 모두 종료되었기 때문에 멈췄을 것이다.

그래서 생성된 Thread를 만들고 setDaemon을 통해 **false**설정까지 해서 돌려봤지만, 결과는 마찬가지였고..

심지어 생성된 Thread는 default 로 UserThread가 된다고 합니다. 

(추가적으로)

 Java 1.5 부터 제공되는 executors에 의해 생성되는 Thread는 non-Daemon Thraed 이며, 때문에 해당 문제와는 전혀 상관없었습니다.  [관련링크](https://stackoverflow.com/questions/10030808/making-callable-threads-as-daemon#answer-10030824)

`Executor es = Executors.newFixedThreadPool(1);`  

즉, 이 부분에서 이미, non-Daemon이 생성됩니다.

결국, DaemonThread의 문제가 아닌것으로 판단하고, 구글링과 더불어 JUnit 쪽을 들여다보기 시작했습니다.



(추가적으로)

 Java thread는 User thread 이지만, JVM은 Kernel Thread  사용하고,  Kernel Thread Pool의 각 Thread에 User Thread CPU 시간을 위임한다고 합니다. 

>   User Thread 이지만, Kernel 영역에 접근할 수 있다고 이해하고 있습니다.

[Are Java threads created in user space or kernel space?](https://stackoverflow.com/questions/18278425/are-java-threads-created-in-user-space-or-kernel-space)  



#### System.exit()

```
public static void main(String... args) {
	Result result = new JUnitCore().runMain(new RealSystem(), args);
	System.exit(result.wasSuccessful() ? 0 : 1);
}
```

junit.runner 안에 있는 JUnitCore 클래스를 보면, Main 함수가 위와 같은걸 볼 수 있습니다.

한마디로 Main Thread에서 runMain()함수가 끝날 경우, System.exit를 호출하여 현재 실행중인 JVM을 종료하게 됩니다.  이 과정에서 실행 중인 Thread는 모두 종료하게 되며 (User / Daemon 상관없이), 예상치 못한 동작을 하게 됩니다. 


#### 결론   

'왜 Test Code의 MainThread가 모두 끝나면 System을 종료시키는 로직을 작성했을까?'라는 생각과 함께 개인적으로 몇가지 추측(?)을 해봤습니다. 

	1. 의도치않은 Thread Dead rock 으로 Test가 끝나지 않을 수 있다.
	2. JUnit 개발자가 Thread 까지 신경 쓰고 싶지 않았다.
	3. Java 7 이여서 Thread를 관리하기 어려웠다.
	(CompletableFuture의 부재)

이 중, 가장 생각과 근접했던 부분은 	~~2. JUnit 개발자가 Thread 까지 신경 쓰고 싶지 않았다. 입니다.~~ 

> Thread 까지 신경쓰면 얼마나 어렵겠어요 .. 

조금 더 진지하게 바라봐도 사실 `2번`이 그럴 듯 합니다.

> 여기부턴 개인적은 견해이므로, `아닌데..?`라는 생각이 들면 댓글이나 메일 주시면 감사합니다 ..:D   
한줄로 요약하자면 다음과 같습니다.

	테스트 로직을 작성하는데 Thread까지 써야하나 ? 

Thread는 이미 `그 존재 자체만으로 제어하기 어렵다.`고 생각합니다.     
상황에 따라 다른 결과가 나올 수 있으며, 그 Test case를 검증하는 또다른 Test가 필요 할 지도 모릅니다. 

> Effective Test 라는 책에서는 네트워크 같이 제어 하기 어려운 부분에 대해서 Test를 진행하지 말라고(?) 합니다. (Mock 객체를 사용하라.)

때문에 JUnit 에서는 Thread를 지원하지 않는게 아닐까 ? 라는 추측을 조심스레 ... 해봅니다.

즉, Test case에 Thread를 사용하지 않는게 좋을 것 같습니다.    

읽어주셔서 감사합니다 :D 

lusiue@gmail.com	
~ 2018.05.15     





 





