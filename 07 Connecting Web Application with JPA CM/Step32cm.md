# Goal ->  103 Step 32 Connecting JPA to other databases
# project name springBoot2-0
> https://grokonez.com/spring-framework/perform-form-validation-spring-boot
> https://stackoverflow.com/questions/32968527/hibernate-sequence-doesnt-exist
> https://www.baeldung.com/spring-security-5-default-password-encoder

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		
		<dependency>
    		<groupId>mysql</groupId>
    		<artifactId>mysql-connector-java</artifactId>
		</dependency>


		Configure application.properties
		spring.jpa.hibernate.ddl-auto=none
		spring.datasource.url=jdbc:mysql://localhost:3306/todo_example?useSSL=false
		spring.datasource.username=todouser
		spring.datasource.password=YOUR_PASSWORD
		Snippet -   com.cmabdullah.springBoot20


# Table 

		create table todo 
		(id integer not null, 
		desc varchar(255), 
		is_done boolean not null, 
		target_date timestamp, 
		user varchar(255), 
		primary key (id));


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

# WelcomeController.java
```java
package com.cmabdullah.springBoot20.controller;

import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.SessionAttributes;

@Controller
@SessionAttributes("name")
public class WelcomeController {

    @RequestMapping(value = "/", method = RequestMethod.GET)
    //@ResponseBody
    public String showWelcomePage(ModelMap model) {
    	model.put("name", getLoggedInUserName());
    	return "welcome";
    }
    
    private String getLoggedInUserName() {
		Object principal = SecurityContextHolder.getContext()
				.getAuthentication().getPrincipal();

		if (principal instanceof UserDetails)
			return ((UserDetails) principal).getUsername();

		return principal.toString();
	}
}

```

# TodoController.java
```java

```

# LogoutController.java
```java
package com.cmabdullah.springBoot20.controller;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Optional;

import javax.validation.Valid;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.propertyeditors.CustomDateEditor;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.WebDataBinder;
import org.springframework.web.bind.annotation.InitBinder;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;

import com.cmabdullah.springBoot20.model.Todo;
import com.cmabdullah.springBoot20.service.TodoRepository;
import com.cmabdullah.springBoot20.service.TodoService;
@Controller
//@SessionAttributes("name")
public class TodoController {
	
//	@Autowired
//	TodoService todoService;
	
	@Autowired
	TodoRepository repository;
	@InitBinder
	public void initBinder(WebDataBinder binder) {
		// Date - dd/MM/yyyy
		SimpleDateFormat dateFormat = new SimpleDateFormat("dd/MM/yyyy");
		binder.registerCustomEditor(Date.class, new CustomDateEditor(
				dateFormat, false));
	}
	
	@RequestMapping(value = "/list-todos", method = RequestMethod.GET)
    //@ResponseBody
    public String showTodo(ModelMap model) {
		String name = getLoggedInUserName(model);
		System.out.println(name);
		model.put("todos", repository.findByUser(name));
//		model.put("todos", todoService.retrieveTodos(name));
    	return "list-todos";
    }

	private String getLoggedInUserName(ModelMap model) {
		Object principal = SecurityContextHolder.getContext()
				.getAuthentication().getPrincipal();

		if (principal instanceof UserDetails)
			return ((UserDetails) principal).getUsername();

		return principal.toString();
	}
	
	@RequestMapping(value = "/add-todo", method = RequestMethod.GET)
	//@ResponseBody
	public String addTodo(ModelMap model) {
		model.addAttribute("todo", new Todo(0, getLoggedInUserName(model), "", new Date(), false));
		return "todo";
	}
	
	@RequestMapping(value="/delete-todo", method = RequestMethod.GET)
	public String deleteTodo(@RequestParam int id){
		//exception handle
//		if(id==1)
//			throw new RuntimeException("Something went wrong");
		
		repository.deleteById(id);
		//in mamory database
		//todoService.deleteTodo(id);
		return "redirect:/list-todos";
	}
	
	@RequestMapping(value="/add-todo", method = RequestMethod.POST)
	public String addTodo(ModelMap model, @Valid Todo todo, BindingResult result){
		if (result.hasErrors()) {
			return "todo";
		}
		
		
		//save databases
		todo.setUser(getLoggedInUserName(model));
		todo.setId((int)Math.random());
		repository.save(todo);
		
		//in memory databases
		//todoService.addTodo(getLoggedInUserName(model), todo.getDesc(), todo.getTargetDate(), false);
		return "redirect:/list-todos";
	}
	
	
	@RequestMapping(value = "/update-todo", method = RequestMethod.GET)
	public String showUpdateTodoPage(@RequestParam int id, ModelMap model) {
		Optional<Todo> todo = repository.findById(id);
		//Todo todo = todoService.retrieveTodo(id);
		model.put("todo", todo);
		return "todo";
	}

	@RequestMapping(value = "/update-todo", method = RequestMethod.POST)
	public String updateTodo(ModelMap model, @Valid Todo todo,
			BindingResult result) {

		if (result.hasErrors()) {
			return "todo";
		}

		todo.setUser(getLoggedInUserName(model));

		//real orm 
		repository.save(todo);
//		todoService.updateTodo(todo);

		return "redirect:/list-todos";
	}

}

```



# ExceptionController.java
```java
package com.cmabdullah.springBoot20.controller;

import javax.servlet.http.HttpServletRequest;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.servlet.ModelAndView;

@Controller("error")
public class ExceptionController {

    private Log logger = LogFactory.getLog(ExceptionController.class);

    @ExceptionHandler(Exception.class)
    public ModelAndView handleError(HttpServletRequest req, Exception ex) {
        logger.error("Request: " + req.getRequestURL() + " raised " + ex);

        ModelAndView mav = new ModelAndView();
        mav.addObject("exception", ex);
        mav.addObject("url", req.getRequestURL());
        mav.setViewName("error");
        return mav;
    }
}
```



Snippet -  com.cmabdullah.springBoot20.model

# Todo.java
```java
package com.cmabdullah.springBoot20.model;

import java.util.Date;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.validation.constraints.Size;
@Entity
public class Todo {
	@Id
	@GeneratedValue
	private int id;
	private String user;
	@Size(min=10, message="Enter at least 10 Characters...")
	private String description;
	private Date targetDate;
	private boolean isDone;
	
	

	public Todo() {
		super();
		// TODO Auto-generated constructor stub
	}

	public Todo(int id, String user, String description, Date targetDate, boolean isDone) {
		super();
		this.id = id;
		this.user = user;
		this.description = description;
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

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
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
		return String.format("Todo [id=%s, user=%s, description=%s, targetDate=%s, isDone=%s]", id, user, description, targetDate,
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
	
	public Todo retrieveTodo(int id) {
        for (Todo todo : todos) {
            if (todo.getId()==id) {
                return todo;
            }
        }
        return null;
    }

    public void updateTodo(Todo todo){
    		todos.remove(todo);
    		todos.add(todo);
    }
}
```
Snippet -  com.cmabdullah.springBoot20.service

# TodoRepository.java
```java
package com.cmabdullah.springBoot20.service;

import java.util.List;

import org.springframework.data.jpa.repository.JpaRepository;

import com.cmabdullah.springBoot20.model.Todo;

public interface TodoRepository extends JpaRepository<Todo, Integer>{
	List<Todo> findByUser(String user);
}

```
Snippet -  com.cmabdullah.springBoot20.security

# SecurityConfiguration.java
```java
package com.cmabdullah.springBoot20.security;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.factory.PasswordEncoderFactories;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
public class SecurityConfiguration extends WebSecurityConfigurerAdapter{
	//Create User - in28Minutes/dummy
	@Autowired
    public void configureGlobalSecurity(AuthenticationManagerBuilder auth)
            throws Exception {
		
		
		PasswordEncoder encoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();
        auth.inMemoryAuthentication()
        .withUser("cmaa")
        .password(encoder.encode("1234"))
        .roles("USER", "ADMIN");
    }
	
	@Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
        .antMatchers("/login", "/h2-console/**")
        .permitAll()
        .antMatchers("/", "/*todo*/**")
        .access("hasRole('USER')")
        .and()
        .formLogin();
        
        http.csrf().disable();
        http.headers().frameOptions().disable();
    }
}


```

Snippet -  /springBoot2-0/src/main/java

# data.sql
```sql
insert into todo
values (10001, 'lets learn coding', false, sysdate(), 'cmaa');
insert into todo
values (10002, 'lets learn JAVA', false, sysdate(), 'cmaa');
insert into todo
values (10003, 'lets learn JS', false, sysdate(), 'cmaa');
insert into todo
values (10004, 'lets learn Spring', false, sysdate(), 'cmaa');


```

Snippet -  /springBoot2-0/src/main/resources

# application.properties
```properties
		logging.level.org.springframework.web: INFO

		spring.mvc.view.prefix: /WEB-INF/jsp/
		spring.mvc.view.suffix: .jsp
		spring.jpa.show-sql=true
		spring.h2.console.enabled=true


		spring.jpa.hibernate.ddl-auto=none
		spring.datasource.url=jdbc:mysql://localhost:3306/todo_example?useSSL=false
		spring.datasource.username=root
		spring.datasource.password=rootcm

		spring.jpa.properties.hibernate.id.new_generator_mappings=false
```
#
```java
```
#
```java
```

Snippet -  /springBoot2-0/src/main/webapp/WEB-INF/jsp/common/

# header.jspf
```java
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt"%>
<%@taglib uri="http://www.springframework.org/tags/form" prefix="form"%>

<html>

<head>
<title>First Web Application</title>
<link href="webjars/bootstrap/3.3.6/css/bootstrap.min.css"
	rel="stylesheet">

</head>

<body>
```



Snippet -  /springBoot2-0/src/main/webapp/WEB-INF/jsp/common/
# navigation.jspf
```java
<nav role="navigation" class="navbar navbar-default">
	<div class="">
		<a href="http://www.cmabdullah.me" class="navbar-brand">C M Abdullah</a>
	</div>
	<div class="navbar-collapse">
		<ul class="nav navbar-nav">
			<li class="active"><a href="/">Home</a></li>
			<li><a href="/list-todos">Todos</a></li>

		</ul>
		
		<ul class="nav navbar-nav navbar-right">
			<li><a href="/logout">Logout</a></li>
		</ul>
	</div>
</nav>
```

Snippet -  /springBoot2-0/src/main/webapp/WEB-INF/jsp/common/
# footer.jspf
```java
<script src="webjars/jquery/1.9.1/jquery.min.js"></script>
<script src="webjars/bootstrap/3.3.6/js/bootstrap.min.js"></script>
<script
	src="webjars/bootstrap-datepicker/1.0.1/js/bootstrap-datepicker.js"></script>
<script>
	$('#targetDate').datepicker({
		format : 'dd/mm/yyyy'
	});
</script>

</body>
</html>
```


Snippet -  /springBoot2-0/src/main/webapp/WEB-INF/jsp

# login.jsp
```java
<%@ include file="common/header.jspf" %>
<%@ include file="common/navigation.jspf" %>

<div class="container">
<font color="red">${errorMessage}</font>
<form method="post">
		Name : <input type="text" name="name" />
		Password : <input type="password" name="password" /> 
		<input type="submit" />
	</form>
</div>
<%@ include file="common/footer.jspf" %>
```
# welcome.jsp
```java
<%@ include file="common/header.jspf"%>
<%@ include file="common/navigation.jspf"%>
<div class="container">
	Welcome ${name}!!<br> <a href="list-todos">click here </a> to
	manage todos....
</div>
<%@ include file="common/footer.jspf"%>
```
# list-todos.jsp
```java
<%@ include file="common/header.jspf" %>
<%@ include file="common/navigation.jspf" %>
	<div class="container">
		<table class="table table-striped">
			<caption>Your todos</caption>
			<thead>
				<tr>
					<th>Description</th>
					<th>Target Date</th>
					<th>Is it Done?</th>
					<th>Update</th>
					<th>Delete</th>
				</tr>
			</thead>
			<tbody>
				<c:forEach items="${todos }" var="todo">
					<tr>
						<td>${todo.description}</td>
						<td><fmt:formatDate value="${todo.targetDate}" pattern="dd/MM/yyyy"/></td>
						<td>${todo.done}</td>
						<td><a type="button" class="btn btn-success"
							href="/update-todo?id=${todo.id}">Update</a></td>
						<td><a type="button" class="btn btn-warning"
							href="/delete-todo?id=${todo.id}">Delete</a></td>
					</tr>
				</c:forEach>
			</tbody>
		</table>
		<div>
			<a class="button" href="/add-todo">Add a Todo</a>
		</div>
	</div>
<%@ include file="common/footer.jspf" %>
```
# todo.jsp
```java
<%@ include file="common/header.jspf" %>
<%@ include file="common/navigation.jspf" %>
	<div class="container">
		<form:form method="post" modelAttribute="todo">
			<form:hidden path="id" />
			<fieldset class="form-group">
				<form:label path="description">Description</form:label>
				<form:input name="description" path="description" type="text" class="form-control"
					required="required" />
				<form:errors path="description" cssClass="text-warning" />
			</fieldset>
			<fieldset class="form-group">
				<form:label path="targetDate">Target Date</form:label>
				<form:input path="targetDate" type="text" class="form-control"
					required="required" />
				<form:errors path="targetDate" cssClass="text-warning" />
			</fieldset>
			<button type="submit" class="btn btn-success">Add</button>
		</form:form>
</div>
<%@ include file="common/footer.jspf" %>
```


# error.jsp
```java
<%@ include file="common/header.jspf"%>
<%@ include file="common/navigation.jspf"%>
<div class="container">
An exception occurred! Please contact Support!
</div>
<%@ include file="common/footer.jspf"%>
```
