---
layout: post
title:  Annotation에 대하여
tags: Java 
categories: Java
---    

최근 면접을 준비하면서 정리한 글입니다.      
Annotation에 대한 짧은 글입니다.        

> JAVA Annotation 이란 ?        

Java 1.5 부터 등장하기 시작한것으로 소스코드에 메타데이터를 표현하기 위해 사용된다.    

메타 데이터란 *"데이터에 대한 설명을 의미한다."*     

1.5 이전 Java에서는 설정파일들을 별도의 XML 파일을 이용하여 관리한다.   
> 예시로, 최근 진행한 프로젝트에서 DB에 접근하기 위한 Url / Id / Password 정보들을 XML로 관리했었다.   

XML로 필요한 정보들을 관리하면, 추후 변경이 용이하고 데이터가 변경되더라도 재컴파일 없이 데이터를 수정할 수 있다는 장점이 존재한다. 다만, XML파일들이 무수히 많아진다면 추후 어디에 필요한 정보가 적혀있는지 찾기도 힘들뿐더러 파일들을 관리하기 어렵다는 단점이 존재한다.        

그래서 Java 1.5부터 Annotation이 등장했다 !  
작은 예시로, 예전 JSP 개발 환경에서는 WEB.xml(DD)에 servlet의 경로와 url을 등록했어야만 했었다. 하지만 Servlet 3.0 부터 @Servlet Annotation을 지원함으로서, Web.xml에 설정하지 않아도 url 과 Servlet을 자동으로 등록해준다 ! 추가적으로 잘못된 Annotation 사용 시, 컴파일 에러를 뱉는다. 
     
> 깔끔하다 .. !   

이렇게 깔끔한 Annotation 에도 단점이 존재한다.    

XML과 달리 수정시 재 컴파일을 해야할 뿐더러 Annotation이 등록된 파일을 보기 전까지 해당 파일에 어떤 Annotation이 등록되어 있는지 알 수 없다는 단점이 존재한다.    

#### 결론        

항상 그렇듯 정해진 답은 없고, 목적에 맞게 적절히 사용해야 된다 ....    



#### 참고      
[[JAVA애노테이션]자바 어노테이션(Java Annotation) 이란, 커스텀 어노테이션](http://ellehu.com/java/3727)     

lusiue@gmail.com      
2017.09.08


 

