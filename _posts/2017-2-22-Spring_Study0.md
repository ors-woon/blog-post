---
layout: post
title:  Spring - 초난감 DAO
tags: 초난감 DAO spring 객체지향
categories: spring
---

토비의 spring 을 공부하며 정리했습니다.

## 초난감 DAO   

제가 학교에서 배우고 사용한 DAO의 형태는 다음과 같다.  

	// add query
    public void add(User user) throws ClassNotFoundException, SQLException {

        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/tobi_spring","root","pass");

        PreparedStatement ps = c.prepareStatement("insert into users(id,name,password) values(?,?,?)");
        ps.setString(1,user.getId());
        ps.setString(2,user.getName());
        ps.setString(3,user.getPassword());

        ps.executeUpdate();

        ps.close();
        c.close();
    }

	// select query
    public User get(String id) throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/tobi_spring","root","pass");

        PreparedStatement ps = c.prepareStatement("select * from users where id = ?");
        ps.setString(1,id);

        ResultSet rs = ps.executeQuery();
        rs.next();
        User user = new User();
        user.setId(rs.getString("id"));
        user.setName(rs.getString("name"));
        user.setPassword(rs.getString("password"));

        rs.close();
        ps.close();
        c.close();

        return user;
    }

+
DB 연동의 과정 

	1. Driver를 가져온다    
	2. 서버와 connection      
	3. Statement 생성 후, query 실행     
	4. 받을 게 있으면 받고, 사용한 리소스 닫기.   

 
위 함수를 실행해보면 에러없이 잘 작동하는데, 토비의 spring은 이와같은 코드를 쫒겨나기 좋은 코드라고 말한다.    
**?** 

Driver을 가져오고 , 서버와 연결하고 query 실행 후,  닫기 .   
아무리봐도 문제점은 없는데 ?   
 ...   
굳이 이상한 점을 뽑으라면 try catch를 사용하지 않고 throws을 사용한 점?  
아주 틀린 말은 아닌데,  
try catch를 사용하지 않고 throws를 사용하다면, 쿼리 실행 중 예외가 발생할 때, 리소스를 닫지 않고 나가게 되고, 그 만큼의 리소스 낭비가 발생한다.   
하지만 지금 당장 중요한 내용은 아니다.       
궁금하면 [참조]('http://stackoverflow.com/questions/3203297/throws-or-trycatch')를 확인하자.    

두 함수를 비교해보면 Driver을 가져오고, 서버와 연결하는 부분이 중복되어 있다.  
지금 당장은 문제가 될 건 아니지만, 만약 위와같은 DAO가 100개 이상 있고, DB가 mysql이 아니라 Oracle로 변경된다면 ?    
...        
ctrl + f 를 눌러 하나하나 변경하며 과거의 자신을 욕하고 있는 모습을 볼수 있을것이다.    

그럼 어떻게 작성해야 되는걸까 ?     
Driver와 connection 부분을 함수로 만들어보자 .   


	public Connection getConnect(){
		 Class.forName("com.mysql.jdbc.Driver");
		        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/tobi_spring","root","pass");
	}

	// select query
    public User get(String id) throws ClassNotFoundException, SQLException {
        Connection c = getConnect();
       	....
        return user;
    }


이와같이 작성하면, Oracle로 바뀌어도 한줄만 수정하면 된다.   
( 짝 짝 짝 )   

그런데 또 문제가 있다고한다.  
( 도와줘요 모래반지 빵야빵야 ) 
 
if    
먼 미래에 우리가 작성한 소스들을 여러 기업에서 구매하기로 했고, 소스를 제공하려고한다.    
이때 기업마다 사용하는 Driver가 다르다면 ?   
기업에게 `dao 패키지에 있는 connection 클래스 안에 getConnect 부분을 수정하면 됩니다` 라고 말할 것인가 ?   
다시한번 기업에서 쫒겨나고 공부를 하자 ...    


함수로 분리해도 안된다면, 추상화 클래스를 만들어보자.   

```
	public abstract class DAO {
	
    	// 추상화 함수 
        public abstract Connection getConnection() throws ClassNotFoundException, SQLException;
    
        // add query
        public void add(User user) throws ClassNotFoundException, SQLException {
    
            Connection c = getConnection();
    		***
    	}
	}
```

위와 같이 작성하면, DAO class를 상속 받고, getConnect를 재정의 하여 사용 할 수 있는데 이와 같은 디자인 패턴을 템플릿 메서드 패턴이라고 한다.  

> 템플릿 메서드 패턴이란, 상위 클래스에서 흐름을 제어하고 하위클래스에서 처리내용을 구체화 하는 패턴이다.    
> 말 그대로 '템플릿' 패턴.   


이와 같이 사용하면 여러 기업들도 자신들의 Driver을 사용 할 수 있다.    

다만 또 문제가 생겼다.  

!?   
 
자바에선 다중상속을 지원하지 않는다. 즉, DAO를 상속한다면 다른 상속의 기회를 잃는 것이다.    
또 다른 문제점은 DAO가 변경되면 발생한다.    
하위 클래스들에게도 영향을 미친다. ( 결합도가 높다 )   

조금 더 수정해보자 .... 

이번엔 메서드가 아닌 클래스를 사용한다.    

```
	public class SimpleConnectionMaker {
	    public Connection getConnection() throws ClassNotFoundException, SQLException {
	        Class.forName("com.mysql.jdbc.Driver");
	        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/tobi_spring","root","pass");
	        return c;
	    }
	}

	public class DAO{
		SimpleConnectionMaker maker = new SimpleConnectionMaker();
		// add query
	    public void add(User user) throws ClassNotFoundException, SQLException {
	        Connection c = maker.getConnection();
			***
	    	}
		}
	
	}
```
	

이렇게 작성하면 상속의 기회는 살게되지만, 마찬가지로 ConnectionMaker 변경에 영향을 받게 된다.  
getConnection() 함수가 사라지거나 바뀌면 문제가 생긴다.    

이제 슬슬 책을 덮고싶어진다 ...  
도대체 어떻게 하라는거지 ?
   
( 외쳐! 모래반지 빵야빵야 )     


이번엔 class 대신 interface를 도입한다.    

```
	public interface  ConnectionMaker {
     	Connection getConnection() throws ClassNotFoundException, SQLException;
	}

	public class SimpleConnectionMaker  implements ConnectionMaker {
	    public Connection getConnection() throws ClassNotFoundException, SQLException {
	        Class.forName("com.mysql.jdbc.Driver");
	        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/tobi_spring","root","pass");
	        return c;
	    }
	}


	public class DAO{
		ConnectionMaker maker = new SimpleConnectionMaker();
		// add query
	    public void add(User user) throws ClassNotFoundException, SQLException {
	        Connection c = maker.getConnection();
			***
	    	}
		}
	}
```
이제 getConnection() 함수는 보장하고 있다. 다만, 아직까지 SimpleConnectionMaker에 의존하고 있다.    
클래스에서 구체적인 다른 클래스를 사용하고 있다면 결합도가 높다고 판단 할 수 있다.  즉, 좋지 않은 코드이다.    

그럼 SimpleConnectionMaker을 어떻게 없앨 수 있을까 ?   

###의존성 주입

```
	public class DAO{
		ConnectionMaker maker;

		public UserDao(ConnectionMaker maker){
        	this.maker = maker;
    	}

		// add query
	    public void add(User user) throws ClassNotFoundException, SQLException {
	        Connection c = maker.getConnection();
			***
	    	}
		}
	}
```

DAO를 생성할 때, 외부에서 해당 connection을 주입받으면 된다.   
책에서는 이와같은 방법을 의존성 주입이라고 설명한다.   

이렇게 코드를 작성할 경우, ConnectionMaker을 상속받은 모든 class들을 DAO에 사용할 수 있게된다.  
즉 , DAO class를 수정하지 않고, 확장 할 수 있게 된다.    

  
많은 시행착오들을 통해, 변경에는 닫혀있고 확장에는 열려있는 DAO를 만들어 봤다.    

해당 내용은 Spring의 '기술'은 아니다.   
다만 우리가 spring을 사용하기에 앞서 객체지향을 이해하기 위한 좋은 예시라고 생각된다.   

그런면에서 토비의 스프링은 정말 좋은 (어려운) 책이다 ..



+덧   
3개월마다 책을 열고 덮고를 반복하고 있습니다.   

lusiue@gmail.com 
