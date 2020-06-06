---
layout: post
title: toStringBuilder에 대하여
tags:  사용법
categories:  bookclips
---    

         
최근 6개월간 Json을 이용해 Data를 보내기 시작하면서, toString을 구현하기 시작했고 Test시 VO 및 DTO의 내용을 찍기 위해 지금까지 사용하고 있었습니다.    

	
	@Override
	public String toString() {
		return "productId : \"" + productId + "\", fileId : \"" + fileId + "\", saveFileName : \"" + saveFileName
				+ "\", type : \"" + type;
	}

> 하지만 Json 형태에 맞추기위해 다음과 같이 만들어줘야한다는 불편함이 존재합니다.    
     
apache에서 불편함을 덜기위해 commons-lang에 다양한 Util을 구현해 놨습니다. 그중 하나가 ToStringBuilder 입니다.   

사용방법에 초점을 맞춘 포스팅입니다. 

### ToStringBuilder 적용  

ToStringBuilder 란 toString을 좀더 우아하고(?) 편하게 만들 수 있도록 제공하는 Class 입니다.       


	<dependency>
		<groupId>commons-lang</groupId>
		<artifactId>commons-lang</artifactId>
		<version>version</version>
	</dependency>      

> maven에 commons-lang을 추가해줍니다.       

그 후, 사용을 원하는 class에 다음과 같은 메소드를 작성해줍니다.    

	public String toString() {
    	return ToStringBuilder.reflectionToString(this).toString();
	}
  

위 방법 외에 append를 이용하여 toString을 구현하는 방법이 있지만, 만약 column이 추가되면 toString까지 변경을 해야되기때문에 불편한감이 있습니다.    
> 위와 같이 사용하는게 가장 깔끔한것 같습니다.      
> (+) reflectionToString은 static method 입니다. 

     
해당 결과를 출력하면 다음과 같은 결과를 뱉습니다.    

	kr.or.reservation.dto.UserReservationDTO@38cccef[id=4,name=<null>,displayStart=<null>,displayEnd=<null>,generalTicketCount=0,youthTicketCount=0,childTicketCount=0,reservationType=0,totalPrice=0]     


조금 이쁘진(?) 않네요. ToStringBuilder에서는 toString style을 변경할 수 있도록 방법을 제공합니다.     

	public String toString() {
    	return ToStringBuilder.reflectionToString(this, ToStringStyle.JSON_STYLE).toString();
	}

reflectionToString의 2번째 인자로 style을 넘겨주면 됩니다. 다양한 style을 제공함으로 맞는 style을 찾아 적용하시면 될것같습니다.     
> 또한 style을 custom할 수 있다고 합니다.


	{"id":4,"name":null,"displayStart":null,"displayEnd":null,"generalTicketCount":0,"youthTicketCount":0,"childTicketCount":0,"reservationType":0,"totalPrice":0}

> Json으로 잘 나오네요


자세한 내용은 [호우의 블로그](https://vnthf.github.io/blog/Java-ToStringBuild%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC/)를 참조하시면 됩니다.    


수업중에 toStringBuilder를 추천받아 적용해봤습니다.      
엄청 편합니다 .. ! 

2017-08-02       
lusiue@naver.com      