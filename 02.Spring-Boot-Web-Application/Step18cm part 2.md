# Goal ->  Using JSR 349 Validations

> https://grokonez.com/spring-framework/perform-form-validation-spring-boot


			<dependency>
				<groupId>org.webjars</groupId>
				<artifactId>bootstrap</artifactId>
				<version>3.3.6</version>
			</dependency>
			<dependency>
				<groupId>org.webjars</groupId>
				<artifactId>jquery</artifactId>
				<version>1.9.1</version>
			</dependency>

Snippet -   com.cmabdullah.springBoot20

# SpringBoot20Application.java
```java
package com.cmabdullah.springBoot20;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SpringBoot20Application {

	public static void main(String[] args) {
		SpringApplication.run(SpringBoot20Application.class, args);
	}
}

```

Snippet -  com.cmabdullah.springBoot20.controller

# LoginController.java
```java
package com.cmabdullah.springBoot20.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.SessionAttributes;

import com.cmabdullah.springBoot20.service.LoginService;

@Controller
@SessionAttributes("name")
public class LoginController {
	
	@Autowired
    private LoginService service;

    @RequestMapping(value = "/login", method = RequestMethod.GET)
    //@ResponseBody
    public String showLoginPage(ModelMap model) {
    	return "login";
    }
    
    @RequestMapping(value = "/login", method = RequestMethod.POST)
    public String showWelcomePage(ModelMap model, @RequestParam String name, String password) {
    	boolean isValidUser = service.validateUser(name, password);
    	if (isValidUser) {
            model.put("name", name);
            return "welcome";
        } else {
            model.put("errorMessage", "Invalid Credentials!!");
            return "login";
        }
    }

}

```

# TodoController.java
```java
package com.cmabdullah.springBoot20.controller;

import java.util.Date;

import javax.validation.Valid;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.SessionAttributes;

import com.cmabdullah.springBoot20.model.Todo;
import com.cmabdullah.springBoot20.service.TodoService;
@Controller
@SessionAttributes("name")
public class TodoController {
	
	@Autowired
	TodoService todoService;
	
	@RequestMapping(value = "/list-todos", method = RequestMethod.GET)
    //@ResponseBody
    public String showTodo(ModelMap model) {
		String name = (String) model.get("name");
		System.out.println(name);
		model.put("todos", todoService.retrieveTodos(name));
    	return "list-todos";
    }
	@RequestMapping(value = "/add-todo", method = RequestMethod.GET)
	//@ResponseBody
	public String addTodo(ModelMap model) {
		model.addAttribute("todo", new Todo(0, (String) model.get("name"), "", new Date(), false));
		return "todo";
	}
	
	@RequestMapping(value="/delete-todo", method = RequestMethod.GET)
	public String deleteTodo(@RequestParam int id){
		todoService.deleteTodo(id);
		return "redirect:/list-todos";
	}
	
	@RequestMapping(value="/add-todo", method = RequestMethod.POST)
	public String addTodo(ModelMap model, @Valid Todo todo, BindingResult result){
		if (result.hasErrors()) {
			return "todo";
		}
		
		todoService.addTodo((String) model.get("name"), todo.getDesc(), new Date(), false);
		return "redirect:/list-todos";
	}

}

```
#
```java
```


Snippet -  com.cmabdullah.springBoot20.model

# Todo.java
```java
package com.cmabdullah.springBoot20.model;

import java.util.Date;

import javax.validation.constraints.Size;

public class Todo {
	private int id;
	private String user;
	@Size(min=10, message="Enter at least 10 Characters...")
	private String desc;
	private Date targetDate;
	private boolean isDone;
	
	

	public Todo() {
		super();
		// TODO Auto-generated constructor stub
	}

	public Todo(int id, String user, String desc, Date targetDate, boolean isDone) {
		super();
		this.id = id;
		this.user = user;
		this.desc = desc;
		this.targetDate = targetDate;
		this.isDone = isDone;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getUser() {
		return user;
	}

	public void setUser(String user) {
		this.user = user;
	}

	public String getDesc() {
		return desc;
	}

	public void setDesc(String desc) {
		this.desc = desc;
	}

	public Date getTargetDate() {
		return targetDate;
	}

	public void setTargetDate(Date targetDate) {
		this.targetDate = targetDate;
	}

	public boolean isDone() {
		return isDone;
	}

	public void setDone(boolean isDone) {
		this.isDone = isDone;
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + id;
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj) {
			return true;
		}
		if (obj == null) {
			return false;
		}
		if (getClass() != obj.getClass()) {
			return false;
		}
		Todo other = (Todo) obj;
		if (id != other.id) {
			return false;
		}
		return true;
	}

	@Override
	public String toString() {
		return String.format("Todo [id=%s, user=%s, desc=%s, targetDate=%s, isDone=%s]", id, user, desc, targetDate,
				isDone);
	}

}

```
#
```java
```
#
```java
```


Snippet -  com.cmabdullah.springBoot20.service

# LoginService.java
```java
package com.cmabdullah.springBoot20.service;

import org.springframework.stereotype.Component;

@Component
public class LoginService {
	public boolean validateUser(String user, String password) {
        return user.equalsIgnoreCase("cmaa") && password.equals("1234");
    }
}


```
# TodoService.java
```java
package com.cmabdullah.springBoot20.service;

import java.util.ArrayList;
import java.util.Date;
import java.util.Iterator;
import java.util.List;

import org.springframework.stereotype.Service;

import com.cmabdullah.springBoot20.model.Todo;


@Service
public class TodoService {
	private static List<Todo> todos = new ArrayList<Todo>();
	private static int todoCount = 3;

	static {
		todos.add(new Todo(1, "cmaa", "Learn Spring MVC", new Date(), false));
		todos.add(new Todo(2, "cmaa", "Learn Struts", new Date(), false));
		todos.add(new Todo(3, "cmaa", "Learn Hibernate", new Date(), false));
	}

	public List<Todo> retrieveTodos(String user) {
		List<Todo> filteredTodos = new ArrayList<Todo>();
		for (Todo todo : todos) {
			if (todo.getUser().equals(user)) {
				filteredTodos.add(todo);
			}
		}
		return filteredTodos;
	}

	public void addTodo(String name, String desc, Date targetDate, boolean isDone) {
		todos.add(new Todo(++todoCount, name, desc, targetDate, isDone));
	}

	public void deleteTodo(int id) {
		Iterator<Todo> iterator = todos.iterator();
		while (iterator.hasNext()) {
			Todo todo = iterator.next();
			if (todo.getId() == id) {
				iterator.remove();
			}
		}
	}
}
```
#
```java
```



Snippet -  /springBoot2-0/src/main/resources

# application.properties
```properties
    logging.level.org.springframework.web: DEBUG
    spring.mvc.view.prefix: /WEB-INF/jsp/
    spring.mvc.view.suffix: .jsp
```
#
```java
```
#
```java
```



Snippet -  /springBoot2-0/src/main/webapp/WEB-INF/jsp

# login.jsp
```java
<html>
<head>
<title>Yahoo!!</title>
</head>
<body>
<font color="red">${errorMessage}</font>
<form method="post">
		Name : <input type="text" name="name" />
		Password : <input type="password" name="password" /> 
		<input type="submit" />
	</form>
</body>
</html>
```
# welcome.jsp
```java
<html>

<head>
<title>First Web Application</title>
</head>

<body>
	Welcome ${name}!!<br>
	 <a href="list-todos">click here </a> to manage todos....
	
</body>

</html>
```
# list-todos.jsp
```java
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<html>

<head>
<title>todo's for ${name}</title>
<link href="webjars/bootstrap/3.3.6/css/bootstrap.min.css"
	rel="stylesheet">
</head>

<body>
	<h1>Your Todos</h1>
	<div class="container">
		<table class="table table-striped">
			<caption>Your todos</caption>
			<thead>
				<tr>
					<th>Description</th>
					<th>Target Date</th>
					<th>Is it Done?</th>
					<th>Delete</th>
				</tr>
			</thead>
			<tbody>
				<c:forEach items="${todos }" var="todo">
					<tr>
						<td>${todo.desc}</td>
						<td>${todo.targetDate}</td>
						<td>${todo.done}</td>
						<td><a type="button" class="btn btn-warning"
							href="/delete-todo?id=${todo.id}">Delete</a></td>
					</tr>
				</c:forEach>
			</tbody>
		</table>
		
		<div>
			<a class="button" href="/add-todo">Add a Todo</a>
		</div>

		<script src="webjars/jquery/1.9.1/jquery.min.js"></script>
		<script src="webjars/bootstrap/3.3.6/js/bootstrap.min.js"></script>

	</div>
</body>
</html>
```
# todo.jsp
```java
<%@taglib uri="http://www.springframework.org/tags/form" prefix="form"%>
<html>

<head>
<title>First Web Application</title>
<link href="webjars/bootstrap/3.3.6/css/bootstrap.min.css"
	rel="stylesheet">

</head>
<!--  commandName="todo" -->
<body>
	<div class="container">
		<form:form method="post" modelAttribute="todo">
			<fieldset class="form-group">
				<form:label path="desc">Description</form:label> 
				<form:input name="desc" path="desc" type="text"
					class="form-control" required="required"/>
					<form:errors path="desc" cssClass="text-warning"/>
			</fieldset>

			<button type="submit" class="btn btn-success">Add</button>
		</form:form>
	</div>

	<script src="webjars/jquery/1.9.1/jquery.min.js"></script>
	<script src="webjars/bootstrap/3.3.6/js/bootstrap.min.js"></script>

</body>

</html>
```
