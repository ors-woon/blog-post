---
layout: post
title: 05-Log4j와System.out.println()
tags: bookclips Log4j
categories: 프로젝트 bookclips
---

프로젝트에서 사용하기도 했고, 면접에서 질문받앗던 Log4j에 대해 정리해볼까 합니다.

관련된 면접 질문은 크게 2가지로 기억합니다.

> Log4j를 왜쓰는가 ?

답변 : System.out.println은 시스템 성능에 영향을 주기도하고 Log4j가 다양한 출력을 제공하기에 사용하는걸로 알고 있습니다.

> 그럼 왜 성능이 느린가 ?

답변 : ...... 그것까진 모르겠습니다.

이번 포스팅은 Sout이 왜 느린가 와 Log4j를 왜 쓰는가에 초점을 맞췄습니다.
설정과 관련된 부분은 포스팅에선 진행하지 않겠습니다.
설정에 관련된 부분은 spring의 경우 [여기](http://addio3305.tistory.com/43)를 확인해 주세요.


#### Log4j 란

Log4j 는 Log for Java의 준말로, Java로 만들어진 프로젝트입니다. 개발을 진행하면서 중간에 값을 확인해야 하는 경우, Log4j를 사용하면 편리하게 값을 확인할 수 있습니다.

log4j는 계층적 구조로 3가지의 계층이 존재하는데 다음과 같습니다.

> Logger -> Appender ->  Layout

Logger 계층은 message를 Appender로 전달하는 역할을 하고,
Appender 계층은 해당 message를 어떻게 출력 할 것인지를 결정합니다.
> txt로 출력,console에 출력, 또는 DB/e-mail로 저장 등 다양한 방법을 제공합니다.

마지막으로 Layout은 어떤 형식으로 출력 할 것인지를 결정합니다.

Log4j를 사용할때, 설정파일로 계층에 대한 설정을 할 수 있는데,

Appender 계층을 통해 txt 파일이나, DB에 에러 로그를 저장하는 형태로 개발을 진행합니다.
또한 Layout 계층을 통해 출력의 형식(날짜 , 로그 발생 근원지 , 색상 )을 설정합니다.

계층뿐만아니라, 5가지 정도의 log level을 설정 할 수 있습니다.

> FATAL / ERROR / WARN / INFO / DEBUG

로그에 대해 치명적일 수록 FATAL 방향의 level을 쓰고, DEBUG 방향으로 갈수록 단순 log를 찍기 위한 level을 의미합니다.

자세한 사항은 [로그 레벨은 어떻게 정하시나요?](https://kldp.org/node/118779)를 확인하시면 편할것 같습니다.


정리하자면 Log4j 를 사용하는 이유는 **다양한 출력양식을 제공**하기 때문이라고 생각합니다.
Level을 개발자가 지정할 수 있을 뿐더러, DB,email 등으로 로그를 보내는 기능을 제공하며, 어디에서 로그가 발생했는지도 보여줍니다.



<img src ="/legacy/public/img/log.jpg">

> Spring의 경우 interceptor부분에 Log를 설정해두면 , 다양한 리소스 요청에대한 Log를 볼 수 있습니다.



#### System.out.println()은 무엇인가 ?

> [System.out.println을 아시나요?](https://okky.kr/article/149762)를 확인하셔도 됩니다.

	System.out.println("Hello World");

간단하게 말해서, 인자로 받은 문자를 출력하는 함수입니다.
세부적으로 [Javadoc](https://docs.oracle.com/javase/8/docs/api/)을 확인하면서 System / out / print 에 대해 분석해 보겠습니다.

System class를 보면 다음과 같습니다.

	public final class System
	extends Object

여기서 알 수 있는 점은 System이 Object를 상속받았다는 점과 final로 선언되었다는 점 입니다.
final로 클래스를 선언하게 되면, 해당 클래스를 상속받을 수 없게됩니다.

> 추가적으로 method를 final로 생성하게되면, override, 재정의를 할 수 없습니다.

즉 System은 Object를 상속받으며 , final로 정의된 상속 불가능한 class 입니다.

java doc의 설명에 의하면, System class는 표준 I/O 와 error output stream을 제공하는 class 입니다. 해당 stream을 제공하기위해, 다음과 같이 변수들을 정의합니다.


	public final static InputStream in = null;
	public final static PrintStream out = null;
	public final static PrintStream err = null;


결국 System.out 은 System class에 있는 out(PrintStream)이라는 변수에 접근하는 코드입니다.

Input**Stream** , Print**Stream** ...
Stream이라는 단어를 많이 들어봤지만, 정확하게 무슨 의미인지는 모르겠습니다 ..

####### Stream 이란  ?

Stream이란 간단히 말해서, 자료의 입출력을 도와주는 중간매개체 라고 합니다.
Stream에 관련된 글들을 찾아봤는데, 빨대로 비유하더군요. 빨대는 음료수 통과 입 사이의 중간매개체 역할을 합니다. 입으로 음료수를 모을때 빨대를 통해 이동하게되고(inputstream), 반대로 입안의 음료를 통으로 보낼때도 빨대를 통해 보내게됩니다(outstream).

> stream은 기본적으로 os에 의해 만들어지고, 사용된다고합니다. 다만 단방향적이기 때문에 in/out은 별도의 stream을 갖게 된다고 합니다.

글들을 보면서 stream과 buffer가 같은게 아닌가 ? 라는 의문을 품었는데, stream은 두 매체간의 데이터를 주고받을때 사용하는 매개체이고, buffer는 그때 데이터를 모앗다가 한번에 전송하는 방식이라고 합니다. stream과 buffer를 함께 사용하는 방식도 존재한다고합니다.

>PrintStream class 내부에서 BufferedWriter를 사용하기도 합니다.


> 정리하자면, Stream은 자료의 입출력을 도와주는 중간매개체입니다.


#### System.out.println()이 느린 이유?
다시 돌아와서, System.out은 System class 안에 있는 PrintStream의 변수를 가르킴니다.
PrintStream을 통해 출력을 제공한다는 뜻입니다. 더 깊게 들어가서, println()을 보면,
다음과 같습니다.


    public void println(String x) {
        synchronized (this) {
            print(x);
            newLine();
        }
    }


입력받은 String에 대하여, synchronized를 통해 출력을 하는 method 입니다.
synchronized는 동기화를 보장하는 method 입니다.
동기화란 상호배제를 보장하기위해, 수행순서를 결정하는 것을 의미합니다. 상호배제란 한 순간에 한 프로세스만 접근할 수 있는 공유자원에 대해, 자원이 사용중이면, 다른 프로세스가 접근하지 못하도록 막는것을 의미합니다.

즉 두개 이상의 프로세스가 공유자원 (임계영역)에 접근할 때, 자원이 사용중이면 다른 프로세스는 앞의 작업이 완료될때 까지 기다리는것을 의미합니다.

여기서 System.out이 느린 첫번째 이유를 발견할 수 있습니다.
많은 프로세스/스레드들이 println에 동시에 접근할때, 한순간 하나의 프로세스만 사용할 수 있으므로, 나머지 프로세스들은 작업이 끝날때까지  다른 작업을 수행하지 못하고 대기하는 현상이 발생합니다.
이와같은 현상은 결국 성능저하를 야기하게 되죠.


> System.out.println()이 느린 1번째 이유 synchronized


2번째이유는 Java의 I/O와 관련된 내용입니다. (이건 개인적인 제 생각일 수 있습니다. )
최근 [[NIO] JAVA NIO의 ByteBuffer와 Channel로 File Handling에서 더 좋은 Perfermance 내기!](http://eincs.com/2009/08/java-nio-bytebuffer-channel-file/)를 읽으면서, java I/O에 대해 생각하는 계기가 되었습니다.

간단하게 정리하자면, Java의 표준 I/O가 느린 이유는 2가지라고 합니다. 커널 버퍼에 직접 접근하지 못한다는 점과, Blocking 하다는 점입니다. ( Blocking이 방금 위에서 설명한 synchronized라고 생각하시면 될거같습니다. )
stream은 커널버퍼에 쓰여지게되는데, code상으로 직접 접근하는 방법이 불가능 했다고합니다. 때문에 커널버퍼에 쓰여진 값은 JVM 메모리에 불러와 사용하게 되는데, 이때 2가지의 overhead가 발생합니다.
커널버퍼에서 JVM으로 불러오는 overhead(Blocking)와 불러온 버퍼 메모리가 GC의 대상이 된다는 점입니다. 또한 JVM으로 불러오는 과정에서 CPU가 관여하는데, 이또한 큰 overhead 라고 합니다.

이와 같은 overhead를 줄이기위해 나온것이 NIO(New I/O)라고합니다.

간단하게 말하면, 커널 버퍼에 직접 접근할 수 있고, Non-Blocking을 사용 할 수 있으며, 단방향 stream과 달리 양방향 channel을 사용한다고 합니다. 이때 channel은 stream과 다르게 byte 가 아닌 buffer 형식만을 지원한다고합니다.
(모든 NIO가 Non-Blocking을 사용하진 않는다고합니다.)


> 결국 System.out.println이 느린 2번째 이유는 표준 I/O를 사용하기 때문이라고 생각합니다.



#### 마치며

표준 I/O가 느리다 정도로 이해하면 될것같습니다.

추후, NIO를 공부하게되면 한번더 정리해 보도록 하겠습니다.

개발을 처음 시작할때 입력하는 코드인데, 어떻게 동작하는지 생각해 본적이 없습니다.
이번 포스팅을 통해 약간의 개념이 잡히지 않았나, 생각해봅니다.

잘못된 개념이 적혀있으면 피드백 부탁드립니다.
감사합니다.
06-25
lusiue@gmail.com






