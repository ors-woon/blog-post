---
layout: post
title:  Annotation에 대하여
tags: Java 
categories: Java
---    

최근 면접을 준비하면서 정리한 글입니다. Annotation에 대한 짧은 글로, 왜 등장했는지에 초점을 맞춰 글을 작성하였습니다.        

> Effective의 Rule.35와 연결됩니다.

#### JAVA Annotation 이란 ?        

Java 1.5 부터 등장하기 시작한것으로 소스코드에 `메타데이터`를 표현하기 위해 사용됩니다.    

메타 데이터란 *"데이터에 대한 설명"*을 말합니다.     

Effective 책에서는 JUnit을 이용해 설명을 이어나가는데, 현재와는 버전이 다름을 유의하고 봐주시길 바랍니다.

#### 이전의 JUnit

(JUnit3였을 땐) TestCase라는 class를 상속받아 사용해야 했으며 함수가 test로 시작되어야만 동작했습니다.


    public class JUnitRunner extends TestCase {

        public void hello(){
            System.out.println("hello");
        }

        public void test(){
            System.out.println("?");
        }

        public void testHello(){
            System.out.println("testHello");
        }


        public void helloTest(){
            System.out.println("testHello");
        }
    }

위 코드를 실행시킬 경우, 2개의 Test만 동작합니다. test의 대상이 되는 함수를 구분한다는 관점에서 위 코드가 나쁘다고 생각할 수는 없습니다. 다만, 개발자의 실수로 `tset` 등의 오타를 입력할 경우 문제가 발생합니다.

> 이 방법은 컴파일 에러가 발생되지 않기 때문입니다.  

이 문제 외에도 몇가지 한계점이 존재합니다.

    1. 특정 요소에만 적용될 수 있게 만들 수 있는 방법이 없다.
    2. 인자를 제한하거나, default 값을 주입할 수 없다.

> 네. 이 한계점을 Annotation으로 끅뽁! 할 수 있습니다.

#### JUnit4   

JUnit4 부터는 상속을 필요로 하지 않으며, naming pattern 역시 사용하지 않습니다.
다만 단 한가지 문구만 붙이면 됩니다.

    @Test
    public void anyThing(){}

@Test 가 바로 Annotation이며, JUnit에서 이를 읽어 메서드를 실행시키게 됩니다.
> 또한 철자가 틀렸을 경우, 컴파일 에러도 뱉습니다.

다만, 이 같은 Annotation이 만능은 아닙니다. 

	@Test
	public static void helloTest(){
		System.out.println("testHello");
	}

일반적인 테스트함수는 static에서는 동작하지 않는데요. 위 코드는 컴파일 에러를 뱉지 않고 런타임 에러를 뱉습니다.
몇가지 상황에 대해서만 컴파일 에러를 뱉을 뿐, 모든 상황에 대응되진 않습니다. 

그럼에도 불구하고, `몇가지 상황`은 꽤 유용하기에, 많은 프레임워크들에서 Annotation을 적극적으로 사용하고 있습니다.

> 대표적으로 Spring/ JUnit / ..

#### 정리하며   

사실 Tool을 제작하지 않는다면, Annotation을 사용할 이유는 없다고 생각합니다. 하지만 Annotation이 생겨난 배경과 어느 분야에서 많이 사용되어지는지 정도는 알아두면 좋을것 같습니다.

(추가적으로)
Annotation 구현시 Reflection이 사용됩니다. :D 
그냥.. 그렇다구요 


lusiue@gmail.com      
2017.09.08


 

