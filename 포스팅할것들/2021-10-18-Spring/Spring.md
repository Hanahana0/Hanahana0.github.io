---
title:  "Spring"
excerpt: "Spring들어가기전 겉핥기"

categories:
  - blog
  -	spring

tags:
  - blog
  -	spring

toc: true
toc_sticky: true

date: 2021-10-18
last_modified_at: 2021-10-18
---



---

## Spring분석

오늘부터 **spring**수업을 시작햇다...<br/>

심장이 쿵쾅거린다.. 😀<br/>

<br/>

오늘 들어본 수업을 토대로 리뷰를 해보면서 복기를 해보자.<br/>

일단 오늘은 **spring**수업의 첫날이기에 겉핥기메타로 진행을 했다.<br/>

스프링을 들어가기전에 어떤 요소가 필요하고 왜 필요하는지 하나씩 접근을 해보자<br/>

<br/>

<img src="/assets/2021-10-18_img/img1.PNG" width="75%" height="75%" title="10-18img1.PNG" alt="10-18img1.PNG">

<br/>

> ~~욱..우엑~~

점점 교율을 진행할수록 디렉토리들의 키가 커진다. <br/> 어린아이의 성장과정같달까..<br/>

<br/>

 1. **EmpDAO**

    이 클래스의 역할은 MVC패턴과 마찬가지로 비즈니스로직! 구현하는 일이었다. <br/>

    데이터를 조회, 조작 등의 메서드를 만드는 작업!

    <br/>

    ```java
    public class EmpDAO {
    	
    	
    	private SqlSessionTemplate ss; 
    	
    	public void setSs(SqlSessionTemplate ss) {
    		this.ss = ss;
    	}
    	
    	public EmpVO[] total() {
    		EmpVO[]ar = null;
    		
    		List<EmpVO> list = ss.selectList("emp.total");
    		
    		if(list != null && list.size() > 0) {
    			ar= new EmpVO[list.size()];
    			list.toArray(ar);
    		}
    		return ar;
    	}
    	
    }
    ```

    <br/>

    여기서 기존의 MVC패턴의 작업과 달랐던점

    <br/>

    > private SqlSessionTemplate ss; 

    상단에 보면 멤버변수로 SqlSessionTemplate 을 선언하였다.<br/>

    이렇게  선언을 한 이유는 **config.xml**에 현재 DAO를 정의 할 때**propertoes**를 정의하면서 

    <br/>반드시 setSs를 통해 저장되도록 하기 위해서다.<br/>

    <br/>

    > ss.close

    항상 DAO작업을 하다보면 마지막에 return전에 ss.close를 통해 세션을 닫아줬는데 <br/>

    이제는 ss를 닫으면 다른 비즈니스로직기능을 추가할 때 사용할 수 없게 되므로 닫으면 안된다!<br/>

    <br/>

    

 2. **emp.xml**

    수행할 xml문을 작성하는 공간 ! <br/>

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
      PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
      "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="emp">
    
      <select id="total" resultType="mybatis.vo.EmpVO">
        select * from employees
      </select>
    </mapper>
    ```

    간단하게 겉만 핥을계획이라 여러가지 기능을 구현하지는 않았다.

	3. **EmpVO**

    데이터베이스의 연결해서 조작하고 하는 컬럼명과 동일하게 멤버변수들을 선언하고 setter를 만들어서 조회및 조작을 할 수 있게 한다!<br/>

    ```java
    package mybatis.vo;
    
    public class EmpVO {
    	private String employee_id, first_name, job_id
    		,hire_date, department_id;
    
    	public String getEmployee_id() {
    		return employee_id;
    	}
    
    	public void setEmployee_id(String employee_id) {
    		this.employee_id = employee_id;
    	}
    
    	public String getFirst_name() {
    		return first_name;
    	}
    
    	public void setFirst_name(String first_name) {
    		this.first_name = first_name;
    	}
    
    	public String getJob_id() {
    		return job_id;
    	}
    
    	public void setJob_id(String job_id) {
    		this.job_id = job_id;
    	}
    
    	public String getHire_date() {
    		return hire_date;
    	}
    
    	public void setHire_date(String hire_date) {
    		this.hire_date = hire_date;
    	}
    
    	public String getDepartment_id() {
    		return department_id;
    	}
    
    	public void setDepartment_id(String department_id) {
    		this.department_id = department_id;
    	}
    	
    }
    
    ```

	4. **jdbc.properties**

    이 부분에서는 원래 config.xml에 작성했던 드라이버의 경로, 아이디, 비밀번호, 커넥션 설정 부분들을 여기서 정의를 하였다.<br/>

    <br/>

    ```
    jdbc.driver=oracle.jdbc.OracleDriver
    jdbc.url=jdbc:oracle:thin:@localhost:1521:xe
    jdbc.user=hr
    jdbc.pwd=1111
    jdbc.maxActive=30
    jdbc.maxIdle=20
    jdbc.minIdle=10
    ```

    <br/>

    이렇게 config.xml에 정의를 하는 것이 아니라 jdbc.properties에 정의하는 이유는 

    <br/>일반 웹콘텐츠 하위폴더에 작성을 하면 다른 사람이 볼 수 있어 보안에 취약하기에 이렇게 숨겨서 정의를 한다.<br/>

	5. **config.xml**

    오늘 수업을 진행하면서 제일 중요했던 부분이다.<br/>

    본래 MVC수업을 진행할 때는 커넥션의 정보 및 맵퍼의 경로를 지정해주는 역할만 했지만

    <br/>

    여기서는 정말 많은 일을 수행했다.

    <br/>

    1. **ds**

       ```xml
       	<context:property-placeholder location="classpath:jdbc.properties"/>
          	
          
          	<bean id="ds" class="org.apache.commons.dbcp.BasicDataSource" 
          	destroy-method="close">
          		<property name="driverClassName" value="${jdbc.driver}"/>
          		<property name="url" value="${jdbc.url}"/>
          		<property name="username" value="${jdbc.user}"/>
          		<property name="password" value="${jdbc.pwd}"/>
          		
          		
          		<property name="maxActive" value="${jdbc.maxActive}"/>
          		
          	
          		<property name="maxIdle" value="${jdbc.maxIdle}"/>
          		
          	
          		<property name="minIdle" value="${jdbc.minIdle}"/>
          	</bean>
       ```

       <br/>

       맨 위에 **context**태그를 선언하여 이전에 만들었던 **jdbc.properties**를 로드해왔다.

       <br/>

       그리고 나서 **Apache**의 라이브러리가 제공하는 BasicDataSource를 생성해보자.

       <br/>

       ```xml
       <bean id="ds" class="org.apache.commons.dbcp.BasicDataSource" 
          	destroy-method="close">
          		<property name="driverClassName" value="${jdbc.driver}"/>
          		<property name="url" value="${jdbc.url}"/>
          		<property name="username" value="${jdbc.user}"/>
          		<property name="password" value="${jdbc.pwd}"/>
          		
          		<!-- 동시에 사용할 수 있는 최대 커넥션 수 -->
          		<property name="maxActive" value="${jdbc.maxActive}"/>
          		
          		<!-- 커넥션 풀에 반납할 때 최대로 유지되는 커넥션의 수 -->
          		<property name="maxIdle" value="${jdbc.maxIdle}"/>
          		
          		<!-- 커넥션 풀에 반납할 때 최소로 유지되는 커넥션의 수 -->
          		<property name="minIdle" value="${jdbc.minIdle}"/>
          	</bean>
       ```

       <br/>

       위를 보면 원래 config.xml에 정의되는 커넥션의 정보가 다른 사람들이 보아도 보안의 문제가 생길일이없다는걸 바로 알 수 있다.<br/>

       **property**들의 **value**를 보면 **jdbc.properties**의 키값들과 동일하단걸 알 수 있다. <br/>

       즉 EL태그를 활용하여 값들을 가져와서 전달을 했다는 걸 알  수 있다.<br/>

    2.  **factory**

       원래 **factory**는 무슨용도였나 기억을 해보자면 **Sqlsession**을 얻기 위함으로 만들었던 기억이 새록새록떠오른다.<br/>

       여기서도 마찬가지였다. <br/>

       ```xml
       <bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
          		<property name="dataSource" ref="ds"/>
          		
          		<property name="mapperLocations" 
          			value="classpath:mybatis/mapper/*.xml"/>
          	</bean>
       ```

       <br/>

       여기서 두 가지 속성을 재정의해야한다.

       1. **dataSource**

          앞서 생성된 **DateSource**를 현재 객체의 dataSource라는 변수에 저장.

       2. **mapperLocations**

          mapper(emp.xml)들의 위치 지정

          여기서 처음 본점이 있다면<br/>

          경로를 지정할 때 **classpath**라고 처음에 시작을 했다는 점과 ***.xml**이었다.<br/>

          classpath란 위치경로의 시작점을 의미했고, 현재 프로젝트에선 **src**를 의미했다.<br/>

          *.xml은 좀 신박했다...<br/>

          *은 모든데이터를 뜻한다. css파일을 만들때 많이 사용해봤었는데 xml에서도 같은뜻으로 쓰인다는게 신기했다.

          <br/>

          즉 모든 xml파일들을 정할때 이렇게 사용한단다 ! 신기해~ 

          

    3. **sqlSession**

       이젠 정말 친숙한 단어가 되어버렸다.<br/>

       이 친구가 필요한 이유는 sql문들을 호출하고 사용하기 위해서는 필수로 필요하단걸 이제는 알고있다.<br/>

       ```xml
       <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
          		<constructor-arg ref="factory"/>
          	</bean>
       ```

       <br/>

       여기서 중요한건 **sqlsession** 을 호출할때는 인잭션을 setter인잭션이 아닌 constructor인잭션으로 생성을 했다.<br/>

       즉 생성과 동시에 값을 전달했다는 뜻이된다.<br/>

       위로 올라가서 보면 알겠지만 우리는 **EmpDAO**를 만들때 <br/>

       기본생성자가 아닌 **SqlSessionTemplate**를 받는 생성자로 만들어 생성과 동시에 값을 전달하도록 설계하였다.

       <br/>

    4. **DAO**

       이 떄는 정말 단순하다.<br/>

       필요한 DAO를 정의하면 된다.<br/>

       ```xml
       <bean id="empDao" class="mybatis.dao.EmpDAO">
           	<property name="ss" ref="sqlSession"/>
           </bean>    
       ```

       <br/>

       이렇게 **config.xml**의 4단계를 거쳐봤다.<br/>

       여기서 중요한건 각각 생성을 할때마다 하단을 보면**ref**를 통해서 위에서 만든 순서대로 <br/>

       객체들을 참고있다는 점이다. <br/>

       이렇게 참고를 함으로 써 각각 퍼즐이 맞춰지는 느낌이다.<br/>

       요리로 치자면 음.. 뭐 반찬들이 모여서 밥상이 만들어지는 느낌이었다.<br/>

    5. **jsp**

       이제 기능들과 데이터베이스를 연결을 했으니 실제 화면에서 기능을 잘 하는지 확인을 해보자 <br/>

       간단한 기능확인을 하기 위해 만든 view단이기 때문에 디자인은 신경쓰지말도록!<br/>

       ```jsp
       <!DOCTYPE html>
       <html>
       <head>
       <meta charset="UTF-8">
       <title>Insert title here</title>
       </head>
       <body>
       	<%
       		BeanFactory bf=
       		new ClassPathXmlApplicationContext("config.xml");
       	
       		//EmpDAO를 찾아온다.
       		EmpDAO e_dao = (EmpDAO)bf.getBean("empDao");
       		
       		//전체 사원 가져오기
       		EmpVO[] ar =e_dao.total();
       %>
       
       	<ul>
       <%
       		for(EmpVO vo: ar){
       %>
       			<li><%=vo.getEmployee_id() %>, <%=vo.getFirst_name() %>,
       				<%=vo.getJob_id() %>, <%=vo.getDepartment_id() %></li>
       <%
       		}
       %>
       	</ul>
       </body>
       </html>
       ```

       <br/>

       **config.xml**의 경로를 가져오기 위해 beanFactory bf를 선언하여 만들어 주었다.

       그렇게 가져온 bf를 활용해서 아까 만들어뒀던 EmpDAO를 찾아오고 <br/>

       EmpVO[] 타입의 ar을 선언해주면서 그와 동시에 e_dao에 total로 만들어 주었다.<br/>

       <br/>

       for문을 통해 출력결과를 확인해보자.

       <br/>

       <br/>

       <img src="/assets/2021-10-18_img/img2.PNG" width="75%" height="75%" title="10-18img2.PNG" alt="10-18img2.PNG">

    

    <br/>

    말끔하게 잘 출력되는걸 확인할 수 있다. <br/>

    오늘은 겉핥기로 어떤 로직으로 움직여야하는지 살펴만 보았기에 기존의 **spring**으로 작업하는 방법과는 다르지만<br/>

    어디서 어떤 구문과 어떤객체를 참고해야하는지 무었때문에 이것들이 필요하고 왜쓰이는지를 알 수 있었다.<br/>

    
