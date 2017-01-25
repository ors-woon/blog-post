---
layout: post
title: "[Java] Collection"
date: 2016-10-26
categories:
  - Java
description: 
image: https://unsplash.it/2000/1200?image=1003
image-sm: https://unsplash.it/500/300?image=1003
---



###### Java의 Collection Interface    
    
    
[세부내용은 다음을 참고해주세요.](https://docs.oracle.com/javase/8/docs/api/)     

  
###### 개념     

Collection Interface는 Collection 계층의 root interface로, Collection 이란 여러 객체의 모임을 말합니다.
객체의 모임, 즉 Collection은 객체를  더하거나, 빼거나 , 제외하는 등의 객체단위의 기본적인 연산을 지원합니다.
예시로, Collection의 하위 계층인 List 또한 객체 단위의 추가 , 삭제 등의 연산을 지원합니다.    

Java에서 Interface란 하위 class로 생성한 객체들이 사용 할 수 있는 method를 기술해 놓은 것입니다. 
때문에 Collection을 구현한 하위 class들은 해당 Method를 구현해야하며, 객체들의 관계에 있어, 느슨한 결합도를 만드는데 중요한 역할을 합니다.
예시로, Collection type을 인자로 설정해 둘 경우, 하위 type인 List , Set등의 객체도 값으로 넘겨 받을 수 있습니다. 


###### 생성자     

일반적으로 JDK에서는 Collection을 직접적으로 구현하지않고, Set , List 같이 서브인터페이스로의 구현을 제공합니다. 
Collection을 구현하는 클래스들은  default 생성자와 Collection Type의 단일 인자를 받는 2개의 생성자를 갖는데, 강요되는 것은 아니지만 모든 Java Platform Library의 Colletion Interface는 이를 준수하고 있습니다.
인자를 받는 경우 collection을 복제 할 수 있어 원하는 타입의 동등한 collection을 생성 할 수 있습니다.   

ArrayList의 생성자는 다음과 같습니다.    


    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }

    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

    public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            // c.toArray might (incorrectly) not return Object[] (see 6260652)
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // replace with empty array.
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }   



###### Method  

Java 8에 추가된 default method는 제외하고 정리해 보았습니다.      
    
boolean add(E e)     
Collection은 제네릭을 이용하여 선언됩니다. ( ex - List<Integer> list )     
해당 타입의 변수를 넘겨 받아 Collection에 추가하는 Method 입니다. ( ex - list.add(5) )       

의미 그대로 element를 삽입하는 Method 입니다.   
   
boolean addAll(Collection<? extends E> c)   
add와는 다른 인자를 받은걸 볼 수 있는데, Collection<? extends E> c은 제네릭을 사용한 것입니다.
<? extends E>부분을 설명하자면, class 상속받을때 쓰는 extends 의미 그대로,  <? extends E> 는 E를 상속 받은 타입을 말합니다.     
즉 E 의 하위타입 또는 E 타입의 인자를 받겠다는 뜻입니다.      
만약 <? extends List> 로 쓰인다면 List 의 하위 클래스인 ArrayList , Linked List 등의 타입이 인자로 올 수 있습니다.     
다만, 상위 클래스에서 정의된 메소드만 사용 할 수 있고, 하위 클래스에서 정의된 메소드를 사용시 에러를 낸다고합니다.    
   
(예시)   


    interface People {
        void eat();
        void sleep();
    }

    class Student implements  People {
            @Override
            public void eat() {
                System.out.println("nice food  !");
            }
            
            @Override
            public void sleep() {
                System.out.println("zzzzzz !");
            }
            
            public void study(){
                System.out.println("studying !");
            }
    }

    public class Main {
        public static void act(List<? extends People> people){
            people.get(0).eat();
            //people.get(0).study(); 이부분은 컴파일 에러가 나옵니다.
        }
        public static void main(String args[]){
            List<Student> list = new ArrayList();
            Student student = new Student();
            list.add(student);
            act(list);
        }
    }   


다시 method 설명으로 돌아와서 , addAll method 는 인자로 받은 collection 타입의 element들을 모두 추가하는 메소드 입니다.      
세부 구현은 하위 interface를 구현하는 class들에서 확인할 수 있습니다.
 
void clear()   

의미 그대로 해당 collection에 있는 모든 인자를 clear 하는 method 입니다.   

boolean contains(Object o)   

인자로 받은 o 가 collection에 있으면 true를 반환. 없으면 false를 반환합니다.   

boolean containAll(Collection<?> c)   

인자로 받은 collection의 element 모두가  해당 collection에 있으면 true를 반환합니다.   

+ boolean equeals(Object o)   
+ int hashCode()   
동일성과 동등성을 체크하기위한 method입니다.       


boolean isEmpty()   

Collection이 비어있으면 true를 반환. 비어있지 않으면 false를 반환합니다.

Iterator<E> iterator()   

Iterator 를 구현하기 위한 method입니다.   

boolean remove(Object o)

값을 넘겨받은 object element를 삭제하는 method입니다.   

boolean removeAll(Collection<?> c)   
 
넘겨받은 collection의 element들을 삭제하는 method입니다.
   
boolean retainAll(Collection<?> c)    

넘겨받은 collection의 element들을 유지하는 method입니다.
 
ArrayList를 찾아보며 정리하던 중에 removeAll 과 retainAll의 소스부분이 같아, 찾아보니 버전문제(버그)라고 합니다. 
제가 갖고있는 버전에 문제가 있던것 같습니다.
      
[자세한 설명](http://stackoverflow.com/questions/8372576/java-commons-collections-removeall)을 참고해 주세요.   
 
int size()    
   
collection의 size를 반환하는 메소드 입니다 . (가장 많이 접했던 method가 아닐까합니다.)

Object[] toArray()  

collection의 element들을 object 타입의 array로 변환하여 반환하는 method 입니다.

<T> T[] toArray()

이전의 toArray와 같은 방식으로 array를 반환하지만, 제네릭을 사용하여 타입을 사용자가 정할 수 있다는 차이가 있습니다.   



Collection의 정의와 Method부분을 간단히 정리했습니다.  

Collection은 한마디로 정의하면 객체들의 그룹이라고 말할 수 있겠네요. 
Collection의 Method 부분 보다, 설계의도(?)를 이해하기위해 글을 작성하였습니다.  


~ 2016 - 10 -31

(2017-01-25  수정완료)
