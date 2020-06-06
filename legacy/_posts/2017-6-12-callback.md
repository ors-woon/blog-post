---
layout: post
title: 04-CallbackMethod
tags: bookclips callback
categories: 프로젝트 bookclips
---
#### CallbackMethod

토비의 Spring이라는 책에서 DB 접근시 JdbcTemplate를 사용하는 것을 보고 공부한 내용을
도입했습니다.
> 이 부분이 프로젝트를 리펙토링한 계기이기도 합니다.

그동안 Connection 맺고 Query를 날리고, Resource를 닫는 DB의 모든 과정을 한 메소드에 담아서 진행했습니다. 당연히 중복되는 부분이 수 없이 많았고, 변화에 대해 유연하지 못한 코드가 되었습니다. DB에 접근하는 모든 과정이 코드를 버리는 결정적인 이유라고 생각해서 책을 참조하여 리펙토링 하였습니다.

> JdbcTemplate 방식을 공부하면서 CallbackMethod를 배웠고, 현재는 JdbcTemplate으로 변경하고 있습니다.

#### 개요

DB 연결 방식은 다음과 같습니다.


      public void add(User user)  {
            Connection c = null;
            PreparedStatement ps = null;
            try {
                  Class.forName("com.mysql.jdbc.Driver");
                  c = DriverManager.getConnection("db주소","id","password");

                  ps = c.prepareStatement("insert into users(id,name,password)"
                  	+"values(?,?,?)");
                  ps.setString(1,user.getId());
                  ps.setString(2,user.getName());
                  ps.setString(3,user.getPassword());

                  ps.executeUpdate();
            } catch (ClassNotFoundException e) {
                  e.printStackTrace();
            } catch (SQLException e) {
                  e.printStackTrace();
            }finally {
                  if(ps != null){
                      try {
                          ps.close();
                      } catch (SQLException e) {
                          e.printStackTrace();
                      }
                  }
                  if(c != null){
                      try {
                          c.close();
                      } catch (SQLException e) {
                          e.printStackTrace();
                      }
                  }
            }
      }


만약 Select 부분이라면 ResultSet까지 포함되어 꽤 긴 코드가 완성됩니다. 메서드가 단순히  하나라면 크게 문제될건 없습니다. 다만 대부분의 서비스들은 수 많은 DAO(Data Access Object)가 존재하고 그 안에는 위와같은 코드가 4개 이상(CRUD) 포함됩니다. 만약 connection이 Oracle로 바뀐다면 ... 꽤 많은 수정 작업을 진행해야됩니다.

> 유사한 내용으로 [Spring - 초난감 DAO](/spring/2017/02/22/Spring_Study0)를 작성해뒀습니다.


여기서 조금 더 유연하게 수정한 코드는 다음과 같습니다.

    private void closeResouce(Connection c, PreparedStatement ps) {
        if(ps != null){
            try {
                ps.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if(c != null){
            try {
                c.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
	}

    private Connection getConnection() throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        return DriverManager.getConnection("db주소","id","password");
    }

    // add query
    public void add(User user)  {
        Connection c = null;
        PreparedStatement ps = null;
        try {
            c = getConnection();

            ps = c.prepareStatement("insert into users(id,name,password) values(?,?,?)");
            ps.setString(1,user.getId());
            ps.setString(2,user.getName());
            ps.setString(3,user.getPassword());

            ps.executeUpdate();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            closeResouce(c, ps);
        }
    }

Connection을 맺는 부분과 Resource를 close 하는 부분을 별도의 메서드로 분리했습니다. 여러개의 DAO를 사용할 경우 해당 메소드들을 별로의 class로 분리하여 사용합니다. 이 경우, Connection 및 close 부분에 변화가 있을 때, 한 메서드만 수정하면 되므로 변화에 유연하게 대응할 수 있습니다.

예전 코드에서는 여기까지만 분리를 해서 사용했습니다. 다만 토비의 Spring에서는 try/catch 부분까지도 중복이 되므로 분리를 하자고 합니다. 이때 CallbackMethod를 사용하게 됩니다.

#### CallbackMethod 란

대부분 코드의 동작방식은 호출자가 피호출자를 호출하는 방식입니다.

> A라는 메서드가 B라는 메서드를 호출하는 방식

Callback은 반대로 피호출자가 호출자를 호출하는 방식입니다.

> A 메서드가 B를 호출하면서 동작을 넘겨주고, B는 진행하면서 다시 돌아와 넘겨받은 호출자의 동작을 수행하는 방식.

callback은 다음과 같은 방식으로 동작합니다.

<img src ="/legacy/public/img/callback.jpg"/>

B 함수가 진행 중에 A로 넘겨 받은 함수 부분을 읽으면, 다시 A로 돌아와 해당 부분을 수행하고
나머지 부분을 수행하게 됩니다.

> 말로만 정리를하니 조금 난감합니다. 코드로 보여드리겠습니다.


	public class Main {

	    public static void A(){
	        callback callback = new callback() {
	            @Override
	            public void print() {
	                System.out.println("This callback");
	            }
	        };
	        B(callback);
	    }

	    public static void B(callback callback){
	        System.out.println("before code");
	        callback.print();
	        System.out.println("after code");

	    }
	    public static void main(String args[]){
	        A();
	    }
	}

	interface callback{
	     void print();
	}

출력 결과는 다음과 같습니다.

before code
This callback
after code

interface를 통해 callback을 구현한 방식입니다.
A에서 print()함수의 기능을 정의하고, B로 넘겨줍니다. B는 수행중에 callback부분을 만나면, 다시 A로 넘어와 해당 부분을 수행하고 돌아옵니다.
>해당 코드를 디버깅하면 동작 방식을 이해할 수 있습니다.

이제 Callback을 이용하여 DB 로직을 수정해 보겠습니다.


	interface DB_TemUpdate {
        PreparedStatement QueryTemplate(Connection con) throws SQLException;
    }

    // add query
    public void add(User user)  {
        DB_TemUpdate temUpdate = new DB_TemUpdate() {
            @Override
            public PreparedStatement QueryTemplate(Connection con) throws SQLException {
                PreparedStatement ps = null;
                ps = con.prepareStatement("insert into users(id,name,password)"
				+"values(?,?,?)");
                ps.setString(1,user.getId());
                ps.setString(2,user.getName());
                ps.setString(3,user.getPassword());
                return ps;
            }
        };
        QueryUpdate(temUpdate);
    }

    public void QueryUpdate(DB_TemUpdate temp)  {
        Connection c = null;
        PreparedStatement ps = null;
        try {
            c = getConnection();
            ps = temp.QueryTemplate(c);
            ps.executeUpdate();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            closeResouce(c, ps);
        }
    }

DB_TemUpdate interface를 정의하고, PreparedStatement를 반환하는 메서드를 선언합니다.
그 후, PreparedStatement와 관련된 부분 (query를 생성하고 해당 쿼리에 변수를 매핑하는 과정)을 익명클래스 방식으로 선언하고 , QueryUpdate 메서드로 넘겨줍니다.

QueryUpdate 메서드는 넘겨받은 temp의 동작을 만나면 다시 돌아와 해당 부분을 수행하고 결과를 반환받아 나머지 부분을 수행합니다.

> 익명클래스 부분이 상당히 길고, 더럽(?)지만 JAVA8의 ramda를 이용하면 조금더 깔끔하게 진행 할 수 있습니다.

이렇게까지 메서드 동작들을 분리하는 이유는 변화에 대해 유연하게 반응하기 위함입니다. 진행하는 서비스의 모든 DAO가 QueryUpdate 부분을 바라보고 있다면, 추후 Log를 넣는 등의 메서드 일부를 수정할 경우 한 부분만 수정하면 됩니다.

JdbcTemplate도 이와같은 callback 방식을 사용한다고합니다. 공부를 이유로 이전 까지의 프로젝트는 위와같은 방식으로 구현을 했지만, 현재 JdbcTemplate방식으로 코드를 수정하고 있습니다.



callback 은 결국 함수의 동작을 넘겨주기 위함이라고 생각합니다 .
java8의 도입으로 다양한 방식으로 사용될 수 있을거 같습니다.


2017 -06 - 12
lusiue@naver.com
