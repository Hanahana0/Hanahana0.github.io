---
title:  "10-01"
excerpt: "jsp액션요소"

categories:
  - Blog
tags:
  - [Blog,jsp]

toc: true
toc_sticky: true
 
date: 2021-10-02
last_modified_at: 2021-10-02
---



##  액션태그(액션요소)

<br/>

## 1. **useBean**

```jsp
<jsp:useBean id="v1" class="ex1.Count" scope="page">
```

<br/>

jsp를 쓰고 : (세미콜론)을 붙이면 액션태그를 의미한다.

<br/>

***

<br/>

## 속성설정

<br/>

1. id: 변수명

2. class: 자료형

3. scope: 사용범위

   ㄱ. page : 현재페이지에만 사용

   ㄴ. session : 사용자가 브라우저를 종료할 때 까지

   ㄷ. application : 서버가 내려갈 떄 까지

<br/>

***

<br/>

## 사용방법

<br/>

움직임을 확인해보기 위해 자바클래스와 jsp를 만들자.

<br/>

```java
public class Count {
	private int count; // 0

	public void inc() {
		++count; // 1씩 증가하는 함수
	}

	public int getCount() {
		return count; // 카운트를 반환하는 기능
	}
```

<br/>

첫 번째 페이지

```jsp
<body>
	<jsp:useBean id="v1" class="ex1.Count" scope="page"/>
	<jsp:useBean id="v2" class="ex1.Count" scope="session"/>
	<jsp:useBean id="v3" class="ex1.Count" scope="application"/>
<%
	v1.inc();
	v2.inc();
	v3.inc();
%>
	<h1>ex1_useBean.jsp</h1>
	<h1>v1.count - page : <%=v1.getCount() %></h1>
	<h1>v2.count - session : <%=v2.getCount() %></h1>
	<h1>v3.count - application : <%=v3.getCount() %></h1>
	
	<a href="ex1_useBean2.jsp">다음</a>
</body>
```

<br/>

두 번째 페이지

```jsp
<body>
	<jsp:useBean id="v1" class="ex1.Count" scope="page">
	</jsp:useBean>
	<jsp:useBean id="v2" class="ex1.Count" scope="session">
	</jsp:useBean>
	<jsp:useBean id="v3" class="ex1.Count" scope="application">
	</jsp:useBean>
<%
	v1.inc();
	v2.inc();
	v3.inc();
%>
	<h1>ex2_useBean.jsp</h1>
	<h1>v1.count - page : <%=v1.getCount() %></h1>
	<h1>v2.count - session : <%=v2.getCount() %></h1>
	<h1>v3.count - application : <%=v3.getCount() %></h1>
	
	<a href="ex1_useBean.jsp">이전</a>
</body>
```

<br/>



## 2.활용

<br/>

활용을 위해서 다른 액션태그를 활용해보자

<br/>

```jsp
<jsp:setProperty name="vo" property="id" param="id"/>
```

## jsp:setProperty

​	**name** : useBean의 id 이름을 의미
<br/>
​	**property** : id이름이 가리키는 객체의 멤버변수명을 의미
<br/>
​	**param **: 현재페이지로 전달되는 파라미터 이름을 의미
<br/>

<br/>

여기서 꿀팁이 있다면

<br/>

```jsp
<jsp:setProperty name="vo" property="*"/>
```

<br/>

이렇게 객체를 생성할 수 있다는 점이다. 
<br/>
이 객체를 해석해보자면 간단하다.<br/>

**모든 setter를 호출하라는 뜻이다.**

<br/>

이렇게 객체를 생성하면 

<br/>

```jsp
<h1>아이디:<%=vo.getId() %></h1>
	<h1>비밀번호:<%=vo.getPw() %></h1>
	<h1>이름:<%=vo.getName() %></h1>
	<ul> 
		<h3>취미</h3>
		<%
			for(String str : vo.getHobby()){
		%>
			<li><%=str %></li>
		<%
			}
		%>
	</ul>
```

이 부분처럼 쉽게 데이터에 접근이 가능하다는 장점이 있다.

***

<br/>

다만 이런 액션태그들을 사용 할 떄 유의할 점이 있다.
<br/>

멤버변수이름과 파라미터 이름이 같아야 한고 <br/>
파일첨부는 할 수 없다는 점을 유의하도록 하자.



<br/>

이렇게 해석하는게 맞나 싶기도 한데 일단 이렇게 해석하자 😂

<br/>

이 태그를 사용하기 위해 VO객체를 하나 만들어 보자

```java
public class TestVO {
	// 중요 !파라미터 이름들과 동일한 이름으로 멤버변수를 선언!
	private String id, pw, name;
	private String[] hobby;
	
	public String getId() {
		return id;
	}
	public void setId(String id) {
		this.id = id;
	}
	public String getPw() {
		return pw;
	}
	public void setPw(String pw) {
		this.pw = pw;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String[] getHobby() {
		return hobby;
	}
	public void setHobby(String[] hobby) {
		this.hobby = hobby;
	}
```

<br/>

이 객체의 자원들은 사용자가 입력한 값을 저장한 객체로 생각하자.
<br/>

이제 사용자가 자원들을 입력할 수 있는 페이지를 만들어보자

<br/>

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
	<style type="text/css">
		*{
		margin: 0 auto;
		text-align: center;
		margin: 15px;
		}
	</style>
</head>
<body>
	<div id="wrap">
		<header>
			<h1>폼 자원을 useBean활용</h1>
		</header>
		<article>
			<form action="ex2.jsp" method="post">
				<label for="id">아이디:</label>
				<input id="id" name="id"/><br/>
	
				<label for="pw">비밀번호:</label>
				<input type="password" id="pw" name="pw"/><br/>
				
				<label for="name">이름:</label>
				<input id="name" name="name"/><br/>
				
				<label for="hobby">취미</label>
				<input type="checkbox" id="hobby" name="hobby" 
						value="독서"/>독서&nbsp;
				<input type="checkbox" id="hobby2" name="hobby" 
						value="영화감상"/>영화감상&nbsp;
				<input type="checkbox" id="hobby3" name="hobby" 
						value="음악감상"/>음악감상&nbsp;
				<input type="checkbox" id="hobby4" name="hobby" 
						value="게임"/>게임&nbsp;
						<br/>
						
				<button type="button" onclick="exe(this.form)">보내기</button>
			</form>
		</article>
	</div>
	<script type="text/javascript">
		function exe(frm){
			frm.submit();
		}
	</script>
</body>
</html>
```

<br/>

<br/>

<img src="/assets/2021-10-01_img/b_image_3.PNG" width="75%" height="75%" title="10-01-img1" alt="10-01-img1">

<br/>

<br/>

대략 이런모습니다.
<br/>

이제 사용자가 입력하는 데이터를 제대로 받는지 확인을 해보자
<br/>

확인을 위한 페이지도 따로 준비한다.

<br/>



```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
    <%
    	request.setCharacterEncoding("UTF-8");	
    %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<jsp:useBean id="vo" class="ex2.TestVO" scope="page"/> 
	<jsp:setProperty name="vo" property="*"/>

	<h1>아이디:<%=vo.getId() %></h1>
	<h1>비밀번호:<%=vo.getPw() %></h1>
	<h1>이름:<%=vo.getName() %></h1>
	<ul> 
		<h3>취미</h3>
		<%
			for(String str : vo.getHobby()){
		%>
			<li><%=str %></li>
		<%
			}
		%>
	</ul>
	
</body>
</html>

```



<br/>

<br/>

이제 개발자의 집인 로컬호스트에서<br/>
직접 입력을 해보고 데이터가 잘 전달이 되는지 확인을 해보자.

<br/>

<br/>

<img src="/assets/2021-10-01_img/b_image_2.PNG" width="75%" height="75%" title="10-01-img2" alt="10-01-img2">

여기서 보내기를 누르면!

<br/>

<img src="/assets/2021-10-01_img/b_image_3.PNG" width="75%" height="75%" title="10-01-img3" alt="10-01-img3">

<br/>

데이터가 잘 전달되는걸 볼 수 있다.

<br/>

**여기서 중요한 점.**

<br/>

 <br/>

### useBean , setProperty

이 두가지 액션태그를 활용하여 원할하게 자원을 받고 보낼 수 있었다.<br/>

원래같았으면 스크립트릿 (<%%>)을 열고<br/>

```java
String id = request.getParameter("id")
	 	
	 	TestVO vo = new Test();
	 	vo.setId(id);
```

<br/>

이런 과정들을 거쳐서 받아야 했다..<br/>

이 두 가지 태그를 사용해 작업의 속도와 데이터의 전달과정을 배워보았다.

<br/>

잊지 말고 꼭 기억하자 !😀

<br/>

<br/>



<br/>



****
