# Goal -> Step 06 Create all Services for Survey and Questions
### pom.xml
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.in28minutes.springboot</groupId>
	<artifactId>first-springboot-project</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.4.0.RELEASE</version>
	</parent>

	<properties>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```
# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
//@ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
		for (int i = 0; i < args.length; i++) {
			System.out.println(i);
		}
	}
}
```


### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```
### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}

}


```
### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	public String retriveWelcomeMessage() {
		return "Hi cm good night";
	}
}
```
### src/main/resources/application.properties
```
logging.level.org.springframework: DEBUG
```


# Goal -> Step 07 Theory - Message Converters and Introduction to REST

# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
//@ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
		for (int i = 0; i < args.length; i++) {
			System.out.println(i);
		}
	}
}
```


### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```

### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	public String retriveWelcomeMessage() {
		return "Hi cm good night";
	}
}
```

### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}

}
```

### SurvayController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.service.SurveyService;

@RestController
public class SurvayController {
	
	@Autowired
	SurveyService serveyService ; 
	
	//http://localhost:8080/surveys/Survey1/questions
	@GetMapping("/surveys/{surveyId}/questions")
	public List<Question> retriveQuestionForSurvey(@PathVariable String surveyId){

		List<Question> myList = serveyService.retrieveQuestions(surveyId);
		//Question [id=Question4, description=Second largest english speaking country, correctAnswer=India, options=[India, Russia, United States, China]]
		for (Question question : myList) {
			System.out.println(question);
		}

		return serveyService.retrieveQuestions(surveyId);
	}

}

```
### http://localhost:8080/surveys/Survey1/questions
```js
[{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question2",
	"description": "Most Populus Country in the World",
	"correctAnswer": "China",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question3",
	"description": "Highest GDP in the World",
	"correctAnswer": "United States",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question4",
	"description": "Second largest english speaking country",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}]
```



### src/main/resources/application.properties
```
logging.level.org.springframework: DEBUG
```


# Goal -> Step 8 Creating REST Service with GetMapping and PathVariable (SurvayController)

# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
//@ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
		for (int i = 0; i < args.length; i++) {
			System.out.println(i);
		}
	}
}
```


### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```

### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	public String retriveWelcomeMessage() {
		return "Hi cm good night";
	}
}
```

### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}

}
```

### SurvayController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.service.SurveyService;

@RestController
public class SurvayController {
	
	@Autowired
	SurveyService serveyService ; 
	
	//http://localhost:8080/surveys/Survey1/questions
	@GetMapping("/surveys/{surveyId}/questions")
	public List<Question> retriveQuestionForSurvey(@PathVariable String surveyId){
		
		
		List<Question> myList = serveyService.retrieveQuestions(surveyId);
		
		for (Question question : myList) {
			System.out.println(question);
		}
		
		
		return serveyService.retrieveQuestions(surveyId);
	}
	
	@GetMapping("/surveys/{surveyId}/questions/{questionId}")
	public Question retriveDataildForQuestion(@PathVariable String surveyId, @PathVariable String questionId){
		return serveyService.retrieveQuestion(surveyId, questionId);
	}
}

```
### http://localhost:8080/surveys/Survey1/questions
```js
[{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question2",
	"description": "Most Populus Country in the World",
	"correctAnswer": "China",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question3",
	"description": "Highest GDP in the World",
	"correctAnswer": "United States",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question4",
	"description": "Second largest english speaking country",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}]
```


### http://localhost:8080/surveys/Survey1/questions/Question1
```js
{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}
```




### src/main/resources/application.properties
```
logging.level.org.springframework: DEBUG
```



# Goal -> Step 09 Spring Boot Developer Tools and LiveReload  Develop faster
### this dependency seems not been working well[dont know is it doing well or not] 
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <optional>true</optional>
        </dependency>

# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
//@ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
		for (int i = 0; i < args.length; i++) {
			System.out.println(i);
		}
	}
}
```


### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```

### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	public String retriveWelcomeMessage() {
		return "Hi cm good night";
	}
}
```

### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}

}
```

### SurvayController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.service.SurveyService;

@RestController
public class SurvayController {
	
	@Autowired
	SurveyService serveyService ; 
	
	//http://localhost:8080/surveys/Survey1/questions
	@GetMapping("/surveys/{surveyId}/questions")
	public List<Question> retriveQuestionForSurvey(@PathVariable String surveyId){
		
		
		List<Question> myList = serveyService.retrieveQuestions(surveyId);
		
		for (Question question : myList) {
			System.out.println(question);
		}
		
		
		return serveyService.retrieveQuestions(surveyId);
	}
	
	@GetMapping("/surveys/{surveyId}/questions/{questionId}")
	public Question retriveDataildForQuestion(@PathVariable String surveyId, @PathVariable String questionId){
		return serveyService.retrieveQuestion(surveyId, questionId);
	}
}

```
### http://localhost:8080/surveys/Survey1/questions
```js
[{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question2",
	"description": "Most Populus Country in the World",
	"correctAnswer": "China",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question3",
	"description": "Highest GDP in the World",
	"correctAnswer": "United States",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question4",
	"description": "Second largest english speaking country",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}]
```


### http://localhost:8080/surveys/Survey1/questions/Question1
```js
{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}
```




### src/main/resources/application.properties
```
logging.level.org.springframework: DEBUG
```


# Goal -> Step 10 Create a Service to add a new question to survey PostMapping Postman

# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
//@ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
		for (int i = 0; i < args.length; i++) {
			System.out.println(i);
		}
	}
}
```


### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```

### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	public String retriveWelcomeMessage() {
		return "Hi cm good night";
	}
}
```

### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}

}
```

### SurvayController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.net.URI;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.service.SurveyService;

@RestController
public class SurvayController {

	@Autowired
	SurveyService serveyService;

	// http://localhost:8080/surveys/Survey1/questions
	@GetMapping("/surveys/{surveyId}/questions")
	public List<Question> retriveQuestionForSurvey(@PathVariable String surveyId) {

		List<Question> myList = serveyService.retrieveQuestions(surveyId);

		for (Question question : myList) {
			System.out.println(question);
		}

		return serveyService.retrieveQuestions(surveyId);
	}

	@GetMapping("/surveys/{surveyId}/questions/{questionId}")
	public Question retriveDataildForQuestion(@PathVariable String surveyId, @PathVariable String questionId) {
		return serveyService.retrieveQuestion(surveyId, questionId);
	}

	//post Mapping
	// /surveys/{surveyId}/questions
	@PostMapping("/surveys/{surveyId}/questions")
	public ResponseEntity<Void> addQuestionToSurvey(@PathVariable String surveyId, @RequestBody Question newQuestion) {

		Question question = serveyService.addQuestion(surveyId, newQuestion);

		if (question == null)//return status code 204 no content
			return ResponseEntity.noContent().build();

		// Success - URI of the new resource in Response Header
		// Status - created
		// URI -> /surveys/{surveyId}/questions/{questionId}
		// question.getQuestionId()
		URI location = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}").buildAndExpand(question.getId())
				.toUri();

		// Status
		//if success return status code 201 created
		return ResponseEntity.created(location).build();
	}
}

```
### http://localhost:8080/surveys/Survey1/questions
```js
[{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question2",
	"description": "Most Populus Country in the World",
	"correctAnswer": "China",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question3",
	"description": "Highest GDP in the World",
	"correctAnswer": "United States",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question4",
	"description": "Second largest english speaking country",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}]
```


### http://localhost:8080/surveys/Survey1/questions/Question1
```js
{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}
```

# Post json data through postman
### http://localhost:8080/surveys/Survey1/questions
```js
{
	"description": "Second Most Populous Country in the World",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}
```



### src/main/resources/application.properties
```
logging.level.org.springframework: DEBUG
```


# Goal -> Step 11 Understand Content Negotiation. Deliver XML Responses.

# Dependency

		<!-- return xml data if needed -->
		<dependency>
            <groupId>com.fasterxml.jackson.dataformat</groupId>
            <artifactId>jackson-dataformat-xml</artifactId>
        </dependency>

# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
//@ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
		for (int i = 0; i < args.length; i++) {
			System.out.println(i);
		}
	}
}
```


### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```

### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	public String retriveWelcomeMessage() {
		return "Hi cm good night";
	}
}
```

### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}

}
```

### SurvayController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.net.URI;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.service.SurveyService;

@RestController
public class SurvayController {

	@Autowired
	SurveyService serveyService;

	// http://localhost:8080/surveys/Survey1/questions
	@GetMapping("/surveys/{surveyId}/questions")
	public List<Question> retriveQuestionForSurvey(@PathVariable String surveyId) {

		List<Question> myList = serveyService.retrieveQuestions(surveyId);

		for (Question question : myList) {
			System.out.println(question);
		}

		return serveyService.retrieveQuestions(surveyId);
	}

	@GetMapping("/surveys/{surveyId}/questions/{questionId}")
	public Question retriveDataildForQuestion(@PathVariable String surveyId, @PathVariable String questionId) {
		return serveyService.retrieveQuestion(surveyId, questionId);
	}

	//post Mapping
	// /surveys/{surveyId}/questions
	@PostMapping("/surveys/{surveyId}/questions")
	public ResponseEntity<Void> addQuestionToSurvey(@PathVariable String surveyId, @RequestBody Question newQuestion) {

		Question question = serveyService.addQuestion(surveyId, newQuestion);

		if (question == null)//return status code 204 no content
			return ResponseEntity.noContent().build();

		// Success - URI of the new resource in Response Header
		// Status - created
		// URI -> /surveys/{surveyId}/questions/{questionId}
		// question.getQuestionId()
		URI location = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}").buildAndExpand(question.getId())
				.toUri();

		// Status
		//if success return status code 201 created
		return ResponseEntity.created(location).build();
	}
}

```
### http://localhost:8080/surveys/Survey1/questions
```js
[{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question2",
	"description": "Most Populus Country in the World",
	"correctAnswer": "China",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question3",
	"description": "Highest GDP in the World",
	"correctAnswer": "United States",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question4",
	"description": "Second largest english speaking country",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}]
```


### http://localhost:8080/surveys/Survey1/questions/Question1
```js
{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}
```

# Post json data through postman
### http://localhost:8080/surveys/Survey1/questions
```js
{
	"description": "Second Most Populous Country in the World",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}
```
# Get xml data

> key Accept

> value application/xml

### http://localhost:8080/surveys/Survey1/questions
```xml
<List>
    <item>
        <id>Question1</id>
        <description>Largest Country in the World</description>
        <correctAnswer>Russia</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question2</id>
        <description>Most Populus Country in the World</description>
        <correctAnswer>China</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question3</id>
        <description>Highest GDP in the World</description>
        <correctAnswer>United States</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question4</id>
        <description>Second largest english speaking country</description>
        <correctAnswer>India</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
</List>
```


### src/main/resources/application.properties
```
logging.level.org.springframework: DEBUG
```

Goal -> Step 12 Create more project

## What You Will Learn during this Step:
- Spring Initializr
- https://start.spring.io
- Create a few projects!

## Exercises
- Create more projects with Spring Initializr and play around with it!


# Goal -> Step 12 theory Step 13 Spring Boot Actuator  Monitor your Spring Boot applications

# Dependency

		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-rest-hal-browser</artifactId>
        </dependency>

# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
//@ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
		for (int i = 0; i < args.length; i++) {
			System.out.println(i);
		}
	}
}
```


### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```

### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	public String retriveWelcomeMessage() {
		return "Hi cm good night";
	}
}
```

### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}

}
```

### SurvayController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.net.URI;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.service.SurveyService;

@RestController
public class SurvayController {

	@Autowired
	SurveyService serveyService;

	// http://localhost:8080/surveys/Survey1/questions
	@GetMapping("/surveys/{surveyId}/questions")
	public List<Question> retriveQuestionForSurvey(@PathVariable String surveyId) {

		List<Question> myList = serveyService.retrieveQuestions(surveyId);

		for (Question question : myList) {
			System.out.println(question);
		}

		return serveyService.retrieveQuestions(surveyId);
	}

	@GetMapping("/surveys/{surveyId}/questions/{questionId}")
	public Question retriveDataildForQuestion(@PathVariable String surveyId, @PathVariable String questionId) {
		return serveyService.retrieveQuestion(surveyId, questionId);
	}

	//post Mapping
	// /surveys/{surveyId}/questions
	@PostMapping("/surveys/{surveyId}/questions")
	public ResponseEntity<Void> addQuestionToSurvey(@PathVariable String surveyId, @RequestBody Question newQuestion) {

		Question question = serveyService.addQuestion(surveyId, newQuestion);

		if (question == null)//return status code 204 no content
			return ResponseEntity.noContent().build();

		// Success - URI of the new resource in Response Header
		// Status - created
		// URI -> /surveys/{surveyId}/questions/{questionId}
		// question.getQuestionId()
		URI location = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}").buildAndExpand(question.getId())
				.toUri();

		// Status
		//if success return status code 201 created
		return ResponseEntity.created(location).build();
	}
}

```
### http://localhost:8080/surveys/Survey1/questions
```js
[{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question2",
	"description": "Most Populus Country in the World",
	"correctAnswer": "China",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question3",
	"description": "Highest GDP in the World",
	"correctAnswer": "United States",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question4",
	"description": "Second largest english speaking country",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}]
```


### http://localhost:8080/surveys/Survey1/questions/Question1
```js
{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}
```

# Post json data through postman
### http://localhost:8080/surveys/Survey1/questions
```js
{
	"description": "Second Most Populous Country in the World",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}
```
# Get xml data

> key Accept

> value application/xml

### http://localhost:8080/surveys/Survey1/questions
```xml
<List>
    <item>
        <id>Question1</id>
        <description>Largest Country in the World</description>
        <correctAnswer>Russia</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question2</id>
        <description>Most Populus Country in the World</description>
        <correctAnswer>China</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question3</id>
        <description>Highest GDP in the World</description>
        <correctAnswer>United States</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question4</id>
        <description>Second largest english speaking country</description>
        <correctAnswer>India</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
</List>
```


### src/main/resources/application.properties
```
logging.level.org.springframework: DEBUG
management.endpoints.web.exposure.include=*
```

> spring init -d=web,actuator -n=actuator-demo actuator-demo

> brew install springboot

> mvn install




# Goal -> Step 14 Understanding Embedded servlet containers  Switch to Jetty or Undertow

# Dependency

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
			<exclusions>
				<exclusion>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-starter-tomcat</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jetty</artifactId>
		</dependency>

# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
//@ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
		for (int i = 0; i < args.length; i++) {
			System.out.println(i);
		}
	}
}
```


### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```

### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	public String retriveWelcomeMessage() {
		return "Hi cm good night";
	}
}
```

### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}

}
```

### SurvayController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.net.URI;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.service.SurveyService;

@RestController
public class SurvayController {

	@Autowired
	SurveyService serveyService;

	// http://localhost:8080/surveys/Survey1/questions
	@GetMapping("/surveys/{surveyId}/questions")
	public List<Question> retriveQuestionForSurvey(@PathVariable String surveyId) {

		List<Question> myList = serveyService.retrieveQuestions(surveyId);

		for (Question question : myList) {
			System.out.println(question);
		}

		return serveyService.retrieveQuestions(surveyId);
	}

	@GetMapping("/surveys/{surveyId}/questions/{questionId}")
	public Question retriveDataildForQuestion(@PathVariable String surveyId, @PathVariable String questionId) {
		return serveyService.retrieveQuestion(surveyId, questionId);
	}

	//post Mapping
	// /surveys/{surveyId}/questions
	@PostMapping("/surveys/{surveyId}/questions")
	public ResponseEntity<Void> addQuestionToSurvey(@PathVariable String surveyId, @RequestBody Question newQuestion) {

		Question question = serveyService.addQuestion(surveyId, newQuestion);

		if (question == null)//return status code 204 no content
			return ResponseEntity.noContent().build();

		// Success - URI of the new resource in Response Header
		// Status - created
		// URI -> /surveys/{surveyId}/questions/{questionId}
		// question.getQuestionId()
		URI location = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}").buildAndExpand(question.getId())
				.toUri();

		// Status
		//if success return status code 201 created
		return ResponseEntity.created(location).build();
	}
}

```
### http://localhost:8080/surveys/Survey1/questions
```js
[{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question2",
	"description": "Most Populus Country in the World",
	"correctAnswer": "China",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question3",
	"description": "Highest GDP in the World",
	"correctAnswer": "United States",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question4",
	"description": "Second largest english speaking country",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}]
```


### http://localhost:8080/surveys/Survey1/questions/Question1
```js
{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}
```

# Post json data through postman
### http://localhost:8080/surveys/Survey1/questions
```js
{
	"description": "Second Most Populous Country in the World",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}
```
# Get xml data

> key Accept

> value application/xml

### http://localhost:8080/surveys/Survey1/questions
```xml
<List>
    <item>
        <id>Question1</id>
        <description>Largest Country in the World</description>
        <correctAnswer>Russia</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question2</id>
        <description>Most Populus Country in the World</description>
        <correctAnswer>China</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question3</id>
        <description>Highest GDP in the World</description>
        <correctAnswer>United States</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question4</id>
        <description>Second largest english speaking country</description>
        <correctAnswer>India</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
</List>
```


### src/main/resources/application.properties
```
logging.level.org.springframework: DEBUG
management.endpoints.web.exposure.include=*
```

> spring init -d=web,actuator -n=actuator-demo actuator-demo

> brew install springboot

> mvn install




# Goal -> Step 15 Adding Dynamic Configuration to your application  YAML and More..
# Dependency

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
			<exclusions>
				<exclusion>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-starter-tomcat</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jetty</artifactId>
		</dependency>

# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
//@ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
		for (int i = 0; i < args.length; i++) {
			System.out.println(i);
		}
	}
}
```


### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```

### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	@Value("${welcome.message}")//data comes from properties
	private String welcomeString;
	public String retriveWelcomeMessage() {
		return welcomeString;
	}
}
```

### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}

}
```

### SurvayController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.net.URI;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.service.SurveyService;

@RestController
public class SurvayController {

	@Autowired
	SurveyService serveyService;

	// http://localhost:8080/surveys/Survey1/questions
	@GetMapping("/surveys/{surveyId}/questions")
	public List<Question> retriveQuestionForSurvey(@PathVariable String surveyId) {

		List<Question> myList = serveyService.retrieveQuestions(surveyId);

		for (Question question : myList) {
			System.out.println(question);
		}

		return serveyService.retrieveQuestions(surveyId);
	}

	@GetMapping("/surveys/{surveyId}/questions/{questionId}")
	public Question retriveDataildForQuestion(@PathVariable String surveyId, @PathVariable String questionId) {
		return serveyService.retrieveQuestion(surveyId, questionId);
	}

	//post Mapping
	// /surveys/{surveyId}/questions
	@PostMapping("/surveys/{surveyId}/questions")
	public ResponseEntity<Void> addQuestionToSurvey(@PathVariable String surveyId, @RequestBody Question newQuestion) {

		Question question = serveyService.addQuestion(surveyId, newQuestion);

		if (question == null)//return status code 204 no content
			return ResponseEntity.noContent().build();

		// Success - URI of the new resource in Response Header
		// Status - created
		// URI -> /surveys/{surveyId}/questions/{questionId}
		// question.getQuestionId()
		URI location = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}").buildAndExpand(question.getId())
				.toUri();

		// Status
		//if success return status code 201 created
		return ResponseEntity.created(location).build();
	}
}

```
### http://localhost:8080/surveys/Survey1/questions
```js
[{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question2",
	"description": "Most Populus Country in the World",
	"correctAnswer": "China",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question3",
	"description": "Highest GDP in the World",
	"correctAnswer": "United States",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question4",
	"description": "Second largest english speaking country",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}]
```


### http://localhost:8080/surveys/Survey1/questions/Question1
```js
{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}
```

# Post json data through postman
### http://localhost:8080/surveys/Survey1/questions
```js
{
	"description": "Second Most Populous Country in the World",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}
```
# Get xml data

> key Accept

> value application/xml

### http://localhost:8080/surveys/Survey1/questions
```xml
<List>
    <item>
        <id>Question1</id>
        <description>Largest Country in the World</description>
        <correctAnswer>Russia</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question2</id>
        <description>Most Populus Country in the World</description>
        <correctAnswer>China</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question3</id>
        <description>Highest GDP in the World</description>
        <correctAnswer>United States</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question4</id>
        <description>Second largest english speaking country</description>
        <correctAnswer>India</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
</List>
```


### src/main/resources/application.properties
```.properties

management.endpoints.web.exposure.include=*
app.name=Abdullah Khan
welcome.message=Hi ${app.name}, this message comes from properties file
```

### src/main/resources/application.yaml
```yaml
logging:
  level:
    org.springframework: INFO
    org.springframework.web.servlet: DEBUG
```



> spring init -d=web,actuator -n=actuator-demo actuator-demo

> brew install springboot

> mvn install



# Goal -> Step 16 Basics of Profiles

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
			<exclusions>
				<exclusion>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-starter-tomcat</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jetty</artifactId>
		</dependency>

# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Profile;

@SpringBootApplication
// @ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
	}
	//http://localhost:8080/browser/index.html#http://localhost:8080/actuator/beans
	@Profile("prod")//this bean activeonly prod profile
	@Bean
	public String dummy() {
		return "something";
	}
}
```


### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```

### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	@Value("${welcome.message}")//data comes from properties
	private String welcomeString;
	public String retriveWelcomeMessage() {
		return welcomeString;
	}
}
```

### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}

}
```

### SurvayController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.net.URI;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.service.SurveyService;

@RestController
public class SurvayController {

	@Autowired
	SurveyService serveyService;

	// http://localhost:8080/surveys/Survey1/questions
	@GetMapping("/surveys/{surveyId}/questions")
	public List<Question> retriveQuestionForSurvey(@PathVariable String surveyId) {

		List<Question> myList = serveyService.retrieveQuestions(surveyId);

		for (Question question : myList) {
			System.out.println(question);
		}

		return serveyService.retrieveQuestions(surveyId);
	}

	@GetMapping("/surveys/{surveyId}/questions/{questionId}")
	public Question retriveDataildForQuestion(@PathVariable String surveyId, @PathVariable String questionId) {
		return serveyService.retrieveQuestion(surveyId, questionId);
	}

	//post Mapping
	// /surveys/{surveyId}/questions
	@PostMapping("/surveys/{surveyId}/questions")
	public ResponseEntity<Void> addQuestionToSurvey(@PathVariable String surveyId, @RequestBody Question newQuestion) {

		Question question = serveyService.addQuestion(surveyId, newQuestion);

		if (question == null)//return status code 204 no content
			return ResponseEntity.noContent().build();

		// Success - URI of the new resource in Response Header
		// Status - created
		// URI -> /surveys/{surveyId}/questions/{questionId}
		// question.getQuestionId()
		URI location = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}").buildAndExpand(question.getId())
				.toUri();

		// Status
		//if success return status code 201 created
		return ResponseEntity.created(location).build();
	}
}

```
### http://localhost:8080/surveys/Survey1/questions
```js
[{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question2",
	"description": "Most Populus Country in the World",
	"correctAnswer": "China",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question3",
	"description": "Highest GDP in the World",
	"correctAnswer": "United States",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question4",
	"description": "Second largest english speaking country",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}]
```


### http://localhost:8080/surveys/Survey1/questions/Question1
```js
{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}
```

# Post json data through postman
### http://localhost:8080/surveys/Survey1/questions
```js
{
	"description": "Second Most Populous Country in the World",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}
```
# Get xml data

> key Accept

> value application/xml

### http://localhost:8080/surveys/Survey1/questions
```xml
<List>
    <item>
        <id>Question1</id>
        <description>Largest Country in the World</description>
        <correctAnswer>Russia</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question2</id>
        <description>Most Populus Country in the World</description>
        <correctAnswer>China</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question3</id>
        <description>Highest GDP in the World</description>
        <correctAnswer>United States</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question4</id>
        <description>Second largest english speaking country</description>
        <correctAnswer>India</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
</List>
```


### src/main/resources/application.properties
```.properties
spring.profiles.active=prod

logging.level.org.springframework: DEBUG

management.endpoints.web.exposure.include=*
app.name=Abdullah Khan
welcome.message=Hi ${app.name}, this message comes from properties file
```

### src/main/resources/application-prod.properties
```properties
logging.level.org.springframework: INFO
```
### src/main/resources/application-dev.properties
```properties
logging.level.org.springframework: TRACE
```


> spring init -d=web,actuator -n=actuator-demo actuator-demo

> brew install springboot

> mvn install


# Goal -> Step 16 Basics of Profiles

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
			<exclusions>
				<exclusion>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-starter-tomcat</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jetty</artifactId>
		</dependency>

# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Profile;

@SpringBootApplication
// @ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
	}
	//http://localhost:8080/browser/index.html#http://localhost:8080/actuator/beans
	@Profile("prod")//this bean activeonly prod profile
	@Bean
	public String dummy() {
		return "something";
	}
}
```


### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```

### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	@Value("${welcome.message}")//data comes from properties
	private String welcomeString;
	public String retriveWelcomeMessage() {
		return welcomeString;
	}
}
```

### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}

}
```

### SurvayController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.net.URI;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.service.SurveyService;

@RestController
public class SurvayController {

	@Autowired
	SurveyService serveyService;

	// http://localhost:8080/surveys/Survey1/questions
	@GetMapping("/surveys/{surveyId}/questions")
	public List<Question> retriveQuestionForSurvey(@PathVariable String surveyId) {

		List<Question> myList = serveyService.retrieveQuestions(surveyId);

		for (Question question : myList) {
			System.out.println(question);
		}

		return serveyService.retrieveQuestions(surveyId);
	}

	@GetMapping("/surveys/{surveyId}/questions/{questionId}")
	public Question retriveDataildForQuestion(@PathVariable String surveyId, @PathVariable String questionId) {
		return serveyService.retrieveQuestion(surveyId, questionId);
	}

	//post Mapping
	// /surveys/{surveyId}/questions
	@PostMapping("/surveys/{surveyId}/questions")
	public ResponseEntity<Void> addQuestionToSurvey(@PathVariable String surveyId, @RequestBody Question newQuestion) {

		Question question = serveyService.addQuestion(surveyId, newQuestion);

		if (question == null)//return status code 204 no content
			return ResponseEntity.noContent().build();

		// Success - URI of the new resource in Response Header
		// Status - created
		// URI -> /surveys/{surveyId}/questions/{questionId}
		// question.getQuestionId()
		URI location = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}").buildAndExpand(question.getId())
				.toUri();

		// Status
		//if success return status code 201 created
		return ResponseEntity.created(location).build();
	}
}

```
### http://localhost:8080/surveys/Survey1/questions
```js
[{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question2",
	"description": "Most Populus Country in the World",
	"correctAnswer": "China",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question3",
	"description": "Highest GDP in the World",
	"correctAnswer": "United States",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question4",
	"description": "Second largest english speaking country",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}]
```


### http://localhost:8080/surveys/Survey1/questions/Question1
```js
{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}
```

# Post json data through postman
### http://localhost:8080/surveys/Survey1/questions
```js
{
	"description": "Second Most Populous Country in the World",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}
```
# Get xml data

> key Accept

> value application/xml

### http://localhost:8080/surveys/Survey1/questions
```xml
<List>
    <item>
        <id>Question1</id>
        <description>Largest Country in the World</description>
        <correctAnswer>Russia</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question2</id>
        <description>Most Populus Country in the World</description>
        <correctAnswer>China</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question3</id>
        <description>Highest GDP in the World</description>
        <correctAnswer>United States</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question4</id>
        <description>Second largest english speaking country</description>
        <correctAnswer>India</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
</List>
```


### src/main/resources/application.properties
```.properties
spring.profiles.active=prod

logging.level.org.springframework: DEBUG

management.endpoints.web.exposure.include=*
app.name=Abdullah Khan
welcome.message=Hi ${app.name}, this message comes from properties file
```

### src/main/resources/application-prod.properties
```properties
logging.level.org.springframework: INFO
```
### src/main/resources/application-dev.properties
```properties
logging.level.org.springframework: TRACE
```


> spring init -d=web,actuator -n=actuator-demo actuator-demo

> brew install springboot

> mvn install



# Goal -> Step 17 Advanced Application Configuration - Type Safe Configuration Properties

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
			<exclusions>
				<exclusion>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-starter-tomcat</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jetty</artifactId>
		</dependency>

# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Profile;

@SpringBootApplication
// @ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
	}
	//http://localhost:8080/browser/index.html#http://localhost:8080/actuator/beans
	@Profile("prod")//this bean activeonly prod profile
	@Bean
	public String dummy() {
		return "something";
	}
}
```


### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```

### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	@Value("${welcome.message}")//data comes from properties
	private String welcomeString;
	public String retriveWelcomeMessage() {
		return welcomeString;
	}
}
```

### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.util.HashMap;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.abdullah.khan.firstApplicationRestL50.configuration.BasicConfiguration;
import com.abdullah.khan.firstApplicationRestL50.service.WelcomeService;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	@Autowired
	BasicConfiguration configuration;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}
	
	@RequestMapping("/dynamic-configuration")
	public Map dynamicConfiguration() {
		Map map = new HashMap();
		map.put("message", configuration.getMessage());
		map.put("number", configuration.getNumber());
		map.put("value", configuration.isValue());

		return map;
	}
}
```

### SurvayController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.net.URI;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.service.SurveyService;

@RestController
public class SurvayController {

	@Autowired
	SurveyService serveyService;

	// http://localhost:8080/surveys/Survey1/questions
	@GetMapping("/surveys/{surveyId}/questions")
	public List<Question> retriveQuestionForSurvey(@PathVariable String surveyId) {

		List<Question> myList = serveyService.retrieveQuestions(surveyId);

		for (Question question : myList) {
			System.out.println(question);
		}

		return serveyService.retrieveQuestions(surveyId);
	}

	@GetMapping("/surveys/{surveyId}/questions/{questionId}")
	public Question retriveDataildForQuestion(@PathVariable String surveyId, @PathVariable String questionId) {
		return serveyService.retrieveQuestion(surveyId, questionId);
	}

	//post Mapping
	// /surveys/{surveyId}/questions
	@PostMapping("/surveys/{surveyId}/questions")
	public ResponseEntity<Void> addQuestionToSurvey(@PathVariable String surveyId, @RequestBody Question newQuestion) {

		Question question = serveyService.addQuestion(surveyId, newQuestion);

		if (question == null)//return status code 204 no content
			return ResponseEntity.noContent().build();

		// Success - URI of the new resource in Response Header
		// Status - created
		// URI -> /surveys/{surveyId}/questions/{questionId}
		// question.getQuestionId()
		URI location = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}").buildAndExpand(question.getId())
				.toUri();

		// Status
		//if success return status code 201 created
		return ResponseEntity.created(location).build();
	}
}

```


### BasicConfiguration.java
```js
package com.abdullah.khan.firstApplicationRestL50.configuration;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@ConfigurationProperties("basic")
@Component
public class BasicConfiguration {
	private boolean value;
    private String message;
    private int number;
	public boolean isValue() {
		return value;
	}
	public void setValue(boolean value) {
		this.value = value;
	}
	public String getMessage() {
		return message;
	}
	public void setMessage(String message) {
		this.message = message;
	}
	public int getNumber() {
		return number;
	}
	public void setNumber(int number) {
		this.number = number;
	}
}
```



### http://localhost:8080/surveys/Survey1/questions
```js
[{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question2",
	"description": "Most Populus Country in the World",
	"correctAnswer": "China",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question3",
	"description": "Highest GDP in the World",
	"correctAnswer": "United States",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question4",
	"description": "Second largest english speaking country",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}]
```


### http://localhost:8080/surveys/Survey1/questions/Question1
```js
{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}
```

# Post json data through postman
### http://localhost:8080/surveys/Survey1/questions
```js
{
	"description": "Second Most Populous Country in the World",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}
```
# Get xml data

> key Accept

> value application/xml

### http://localhost:8080/surveys/Survey1/questions
```xml
<List>
    <item>
        <id>Question1</id>
        <description>Largest Country in the World</description>
        <correctAnswer>Russia</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question2</id>
        <description>Most Populus Country in the World</description>
        <correctAnswer>China</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question3</id>
        <description>Highest GDP in the World</description>
        <correctAnswer>United States</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question4</id>
        <description>Second largest english speaking country</description>
        <correctAnswer>India</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
</List>
```


### src/main/resources/application.properties
```.properties
spring.profiles.active=prod

logging.level.org.springframework: DEBUG

management.endpoints.web.exposure.include=*
app.name=Abdullah Khan
welcome.message=Hi ${app.name}, this message comes from properties file

basic.value: true
basic.message: Dynamic Message
basic.number: 100
```

### src/main/resources/application-prod.properties
```properties
logging.level.org.springframework: INFO
```
### src/main/resources/application-dev.properties
```properties
logging.level.org.springframework: TRACE
```


> spring init -d=web,actuator -n=actuator-demo actuator-demo

> brew install springboot

> mvn install




# Goal -> Step 18 Spring Boot Starter  Spring Data JPA with CommandLineRunner(part 1 & 2)
 
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
        </dependency>

# package com.abdullah.khan.firstApplicationRestL50;
# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Profile;

@SpringBootApplication
// @ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
	}
	//http://localhost:8080/browser/index.html#http://localhost:8080/actuator/beans
	@Profile("prod")//this bean activeonly prod profile
	@Bean
	public String dummy() {
		return "something";
	}
}
```

# package com.abdullah.khan.firstApplicationRestL50.model;
### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
# package com.abdullah.khan.firstApplicationRestL50.service;
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```

### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	@Value("${welcome.message}")//data comes from properties
	private String welcomeString;
	public String retriveWelcomeMessage() {
		return welcomeString;
	}
}
```
# package com.abdullah.khan.firstApplicationRestL50.controller;
### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.util.HashMap;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.abdullah.khan.firstApplicationRestL50.configuration.BasicConfiguration;
import com.abdullah.khan.firstApplicationRestL50.service.WelcomeService;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	@Autowired
	BasicConfiguration configuration;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}
	
	@RequestMapping("/dynamic-configuration")
	public Map dynamicConfiguration() {
		Map map = new HashMap();
		map.put("message", configuration.getMessage());
		map.put("number", configuration.getNumber());
		map.put("value", configuration.isValue());

		return map;
	}
}
```

### SurvayController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.net.URI;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.service.SurveyService;

@RestController
public class SurvayController {

	@Autowired
	SurveyService serveyService;

	// http://localhost:8080/surveys/Survey1/questions
	@GetMapping("/surveys/{surveyId}/questions")
	public List<Question> retriveQuestionForSurvey(@PathVariable String surveyId) {

		List<Question> myList = serveyService.retrieveQuestions(surveyId);

		for (Question question : myList) {
			System.out.println(question);
		}

		return serveyService.retrieveQuestions(surveyId);
	}

	@GetMapping("/surveys/{surveyId}/questions/{questionId}")
	public Question retriveDataildForQuestion(@PathVariable String surveyId, @PathVariable String questionId) {
		return serveyService.retrieveQuestion(surveyId, questionId);
	}

	//post Mapping
	// /surveys/{surveyId}/questions
	@PostMapping("/surveys/{surveyId}/questions")
	public ResponseEntity<Void> addQuestionToSurvey(@PathVariable String surveyId, @RequestBody Question newQuestion) {

		Question question = serveyService.addQuestion(surveyId, newQuestion);

		if (question == null)//return status code 204 no content
			return ResponseEntity.noContent().build();

		// Success - URI of the new resource in Response Header
		// Status - created
		// URI -> /surveys/{surveyId}/questions/{questionId}
		// question.getQuestionId()
		URI location = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}").buildAndExpand(question.getId())
				.toUri();

		// Status
		//if success return status code 201 created
		return ResponseEntity.created(location).build();
	}
}

```

# package com.abdullah.khan.firstApplicationRestL50.configuration;
### BasicConfiguration.java
```java
package com.abdullah.khan.firstApplicationRestL50.configuration;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@ConfigurationProperties("basic")
@Component
public class BasicConfiguration {
	private boolean value;
    private String message;
    private int number;
	public boolean isValue() {
		return value;
	}
	public void setValue(boolean value) {
		this.value = value;
	}
	public String getMessage() {
		return message;
	}
	public void setMessage(String message) {
		this.message = message;
	}
	public int getNumber() {
		return number;
	}
	public void setNumber(int number) {
		this.number = number;
	}
}
```


# package com.abdullah.khan.firstApplicationRestL50.jpa;
### User
```java
package com.abdullah.khan.firstApplicationRestL50.jpa;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
@Entity
public class User {
	@Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    private String name;// Not perfect!! Should be a proper object!
    private String role;// Not perfect!! An enum should be a better choice!
	
    public User() {

	}

    
	public User(String name, String role) {
		super();
		this.name = name;
		this.role = role;
	}


	public User(Long id, String name, String role) {
		super();
		this.id = id;
		this.name = name;
		this.role = role;
	}


	public Long getId() {
		return id;
	}


	public String getName() {
		return name;
	}


	public String getRole() {
		return role;
	}


	@Override
	public String toString() {
		return "User [id=" + id + ", name=" + name + ", role=" + role + "]";
	}
	
}
```
### UserRepository
```java
package com.abdullah.khan.firstApplicationRestL50.jpa;

import org.springframework.data.repository.CrudRepository;

public interface UserRepository extends CrudRepository<User, Long> {

}
```
### UserCommandLineRunner
```java
package com.abdullah.khan.firstApplicationRestL50.jpa;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;
@Component
public class UserCommandLineRunner implements CommandLineRunner {

	private static final Logger log = LoggerFactory
            .getLogger(UserCommandLineRunner.class);

    @Autowired
    private UserRepository repository;

    @Override
    public void run(String... args) {
        // save a couple of customers
        repository.save(new User("C M Abdullah", "Admin"));
        repository.save(new User("Rafid", "User"));
        repository.save(new User("Abul khayer", "Admin"));
        repository.save(new User("Taklu", "User"));

        log.info("-------------------------------");
        log.info("Finding all users");
        log.info("-------------------------------");
        for (User user : repository.findAll()) {
            log.info(user.toString());
        }
        
    }

}

```
### 
```java

```
### 
```java

```





### http://localhost:8080/surveys/Survey1/questions
```js
[{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question2",
	"description": "Most Populus Country in the World",
	"correctAnswer": "China",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question3",
	"description": "Highest GDP in the World",
	"correctAnswer": "United States",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question4",
	"description": "Second largest english speaking country",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}]
```


### http://localhost:8080/surveys/Survey1/questions/Question1
```js
{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}
```

# Post json data through postman
### http://localhost:8080/surveys/Survey1/questions
```js
{
	"description": "Second Most Populous Country in the World",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}
```
# Get xml data

> key Accept

> value application/xml

### http://localhost:8080/surveys/Survey1/questions
```xml
<List>
    <item>
        <id>Question1</id>
        <description>Largest Country in the World</description>
        <correctAnswer>Russia</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question2</id>
        <description>Most Populus Country in the World</description>
        <correctAnswer>China</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question3</id>
        <description>Highest GDP in the World</description>
        <correctAnswer>United States</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question4</id>
        <description>Second largest english speaking country</description>
        <correctAnswer>India</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
</List>
```


### src/main/resources/application.properties
```.properties
spring.profiles.active=prod

logging.level.org.springframework: DEBUG

management.endpoints.web.exposure.include=*
app.name=Abdullah Khan
welcome.message=Hi ${app.name}, this message comes from properties file

basic.value: true
basic.message: Dynamic Message
basic.number: 100

spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```

### src/main/resources/application-prod.properties
```properties
logging.level.org.springframework: INFO
```
### src/main/resources/application-dev.properties
```properties
logging.level.org.springframework: TRACE
```


> spring init -d=web,actuator -n=actuator-demo actuator-demo

> brew install springboot

> mvn install



# Goal -> Step 19 In Memory Database H2 Console and add a new JPA Repository Method
 
## What You Will Learn during this Step:
- Look at H2 Console : http://localhost:8080/h2-console
 - Use db url jdbc:h2:mem:testdb
- Add findByRole method 

## Some Notes
- Useful Properties
 - spring.datasource.driver-class-name=com.mysql.jdbc.Driver
 - spring.datasource.url=jdbc:mysql://localhost:3306/test 
 - spring.datasource.username=root
 - spring.datasource.password=admin
 - spring.datasource.initialize=true 
 - spring.jpa.hibernate.ddl-auto=update
 - spring.jpa.show-sql=true

# package com.abdullah.khan.firstApplicationRestL50;
# Application
```java
package com.abdullah.khan.firstApplicationRestL50;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Profile;

@SpringBootApplication
// @ComponentScan("com.abdullah.khan.firstApplicationRestL50")
public class Application {

	public static void main(String[] args) {
		ApplicationContext ctx = SpringApplication.run(Application.class, args);
	}
	//http://localhost:8080/browser/index.html#http://localhost:8080/actuator/beans
	@Profile("prod")//this bean activeonly prod profile
	@Bean
	public String dummy() {
		return "something";
	}
}
```

# package com.abdullah.khan.firstApplicationRestL50.model;
### Question.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Question {
	private String id;
	private String description;
	private String correctAnswer;
	private List<String> options;

	// Needed by Caused by: com.fasterxml.jackson.databind.JsonMappingException:
	// Can not construct instance of com.in28minutes.springboot.model.Question:
	// no suitable constructor found, can not deserialize from Object value
	// (missing default constructor or creator, or perhaps need to add/enable
	// type information?)
	public Question() {

	}

	public Question(String id, String description, String correctAnswer,
			List<String> options) {
		super();
		this.id = id;
		this.description = description;
		this.correctAnswer = correctAnswer;
		this.options = options;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getDescription() {
		return description;
	}

	public String getCorrectAnswer() {
		return correctAnswer;
	}

	public List<String> getOptions() {
		return options;
	}

	@Override
	public String toString() {
		return String
				.format("Question [id=%s, description=%s, correctAnswer=%s, options=%s]",
						id, description, correctAnswer, options);
	}

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Question other = (Question) obj;
		if (id == null) {
			if (other.id != null)
				return false;
		} else if (!id.equals(other.id))
			return false;
		return true;
	}

}
```
### Survey.java
```java
package com.abdullah.khan.firstApplicationRestL50.model;

import java.util.List;

public class Survey {
	private String id;
	private String title;
	private String description;
	private List<Question> questions;

	public Survey(String id, String title, String description,
			List<Question> questions) {
		super();
		this.id = id;
		this.title = title;
		this.description = description;
		this.questions = questions;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getDescription() {
		return description;
	}

	public void setDescription(String description) {
		this.description = description;
	}

	public List<Question> getQuestions() {
		return questions;
	}

	public void setQuestions(List<Question> questions) {
		this.questions = questions;
	}

	@Override
	public String toString() {
		return "Survey [id=" + id + ", title=" + title + ", description="
				+ description + ", questions=" + questions + "]";
	}

}
```
# package com.abdullah.khan.firstApplicationRestL50.service;
### SurveyService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import java.math.BigInteger;
import java.security.SecureRandom;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import org.springframework.stereotype.Component;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.model.Survey;

@Component
public class SurveyService {
	private static List<Survey> surveys = new ArrayList<>();
	static {
		Question question1 = new Question("Question1", "Largest Country in the World", "Russia",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question2 = new Question("Question2", "Most Populus Country in the World", "China",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question3 = new Question("Question3", "Highest GDP in the World", "United States",
				Arrays.asList("India", "Russia", "United States", "China"));
		Question question4 = new Question("Question4", "Second largest english speaking country", "India",
				Arrays.asList("India", "Russia", "United States", "China"));

		List<Question> questions = new ArrayList<>(Arrays.asList(question1, question2, question3, question4));

		Survey survey = new Survey("Survey1", "My Favorite Survey", "Description of the Survey", questions);

		surveys.add(survey);
	}

	public List<Survey> retrieveAllSurveys() {
		return surveys;
	}

	public Survey retrieveSurvey(String surveyId) {
		for (Survey survey : surveys) {
			if (survey.getId().equals(surveyId)) {
				return survey;
			}
		}
		return null;
	}

	public List<Question> retrieveQuestions(String surveyId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		return survey.getQuestions();
	}

	public Question retrieveQuestion(String surveyId, String questionId) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		for (Question question : survey.getQuestions()) {
			if (question.getId().equals(questionId)) {
				return question;
			}
		}

		return null;
	}

	private SecureRandom random = new SecureRandom();

	public Question addQuestion(String surveyId, Question question) {
		Survey survey = retrieveSurvey(surveyId);

		if (survey == null) {
			return null;
		}

		String randomId = new BigInteger(130, random).toString(32);
		question.setId(randomId);

		survey.getQuestions().add(question);

		return question;
	}
}
```

### WelcomeService.java
```java
package com.abdullah.khan.firstApplicationRestL50.service;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class WelcomeService{
	@Value("${welcome.message}")//data comes from properties
	private String welcomeString;
	public String retriveWelcomeMessage() {
		return welcomeString;
	}
}
```
# package com.abdullah.khan.firstApplicationRestL50.controller;
### WelcomeController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.util.HashMap;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.abdullah.khan.firstApplicationRestL50.configuration.BasicConfiguration;
import com.abdullah.khan.firstApplicationRestL50.service.WelcomeService;

@RestController
public class WelcomeController {
	@Autowired
	WelcomeService welcomeService;
	@Autowired
	BasicConfiguration configuration;
	
	@RequestMapping("/welcome")
	public String welcome() {
		return welcomeService.retriveWelcomeMessage();
	}
	
	@RequestMapping("/dynamic-configuration")
	public Map dynamicConfiguration() {
		Map map = new HashMap();
		map.put("message", configuration.getMessage());
		map.put("number", configuration.getNumber());
		map.put("value", configuration.isValue());

		return map;
	}
}
```

### SurvayController.java
```java
package com.abdullah.khan.firstApplicationRestL50.controller;

import java.net.URI;
import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.support.ServletUriComponentsBuilder;

import com.abdullah.khan.firstApplicationRestL50.model.Question;
import com.abdullah.khan.firstApplicationRestL50.service.SurveyService;

@RestController
public class SurvayController {

	@Autowired
	SurveyService serveyService;

	// http://localhost:8080/surveys/Survey1/questions
	@GetMapping("/surveys/{surveyId}/questions")
	public List<Question> retriveQuestionForSurvey(@PathVariable String surveyId) {

		List<Question> myList = serveyService.retrieveQuestions(surveyId);

		for (Question question : myList) {
			System.out.println(question);
		}

		return serveyService.retrieveQuestions(surveyId);
	}

	@GetMapping("/surveys/{surveyId}/questions/{questionId}")
	public Question retriveDataildForQuestion(@PathVariable String surveyId, @PathVariable String questionId) {
		return serveyService.retrieveQuestion(surveyId, questionId);
	}

	//post Mapping
	// /surveys/{surveyId}/questions
	@PostMapping("/surveys/{surveyId}/questions")
	public ResponseEntity<Void> addQuestionToSurvey(@PathVariable String surveyId, @RequestBody Question newQuestion) {

		Question question = serveyService.addQuestion(surveyId, newQuestion);

		if (question == null)//return status code 204 no content
			return ResponseEntity.noContent().build();

		// Success - URI of the new resource in Response Header
		// Status - created
		// URI -> /surveys/{surveyId}/questions/{questionId}
		// question.getQuestionId()
		URI location = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}").buildAndExpand(question.getId())
				.toUri();

		// Status
		//if success return status code 201 created
		return ResponseEntity.created(location).build();
	}
}

```

# package com.abdullah.khan.firstApplicationRestL50.configuration;
### BasicConfiguration.java
```java
package com.abdullah.khan.firstApplicationRestL50.configuration;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@ConfigurationProperties("basic")
@Component
public class BasicConfiguration {
	private boolean value;
    private String message;
    private int number;
	public boolean isValue() {
		return value;
	}
	public void setValue(boolean value) {
		this.value = value;
	}
	public String getMessage() {
		return message;
	}
	public void setMessage(String message) {
		this.message = message;
	}
	public int getNumber() {
		return number;
	}
	public void setNumber(int number) {
		this.number = number;
	}
}
```


# package com.abdullah.khan.firstApplicationRestL50.jpa;
### User
```java
package com.abdullah.khan.firstApplicationRestL50.jpa;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
@Entity
public class User {
	@Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    private String name;// Not perfect!! Should be a proper object!
    private String role;// Not perfect!! An enum should be a better choice!
	
    public User() {

	}

    
	public User(String name, String role) {
		super();
		this.name = name;
		this.role = role;
	}


	public User(Long id, String name, String role) {
		super();
		this.id = id;
		this.name = name;
		this.role = role;
	}


	public Long getId() {
		return id;
	}


	public String getName() {
		return name;
	}


	public String getRole() {
		return role;
	}


	@Override
	public String toString() {
		return "User [id=" + id + ", name=" + name + ", role=" + role + "]";
	}
	
}
```
### UserRepository
```java
package com.abdullah.khan.firstApplicationRestL50.jpa;

import java.util.List;

import org.springframework.data.repository.CrudRepository;

public interface UserRepository extends CrudRepository<User, Long> {
	List<User> findByRole(String role);
}
```
### UserCommandLineRunner
```java
package com.abdullah.khan.firstApplicationRestL50.jpa;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;
@Component
public class UserCommandLineRunner implements CommandLineRunner {

	private static final Logger log = LoggerFactory
            .getLogger(UserCommandLineRunner.class);

    @Autowired
    private UserRepository repository;

    @Override
    public void run(String... args) {
        // save a couple of customers
        repository.save(new User("C M Abdullah", "Admin"));
        repository.save(new User("Rafid", "User"));
        repository.save(new User("Abul khayer", "Admin"));
        repository.save(new User("Taklu", "User"));

        log.info("-------------------------------");
        log.info("Finding all users");
        log.info("-------------------------------");
        for (User user : repository.findAll()) {
            log.info(user.toString());
        }
        
        log.info("-------------------------------");
        log.info("Finding all Admins");
        log.info("-------------------------------");
        for (User admin : repository.findByRole("Admin")) {
            log.info(admin.toString());
            // Do something...
        }   
    }
}
```
### 
```java

```
### 
```java

```





### http://localhost:8080/surveys/Survey1/questions
```js
[{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question2",
	"description": "Most Populus Country in the World",
	"correctAnswer": "China",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question3",
	"description": "Highest GDP in the World",
	"correctAnswer": "United States",
	"options": ["India", "Russia", "United States", "China"]
}, {
	"id": "Question4",
	"description": "Second largest english speaking country",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}]
```


### http://localhost:8080/surveys/Survey1/questions/Question1
```js
{
	"id": "Question1",
	"description": "Largest Country in the World",
	"correctAnswer": "Russia",
	"options": ["India", "Russia", "United States", "China"]
}
```

# Post json data through postman
### http://localhost:8080/surveys/Survey1/questions
```js
{
	"description": "Second Most Populous Country in the World",
	"correctAnswer": "India",
	"options": ["India", "Russia", "United States", "China"]
}
```
# Get xml data

> key Accept

> value application/xml

### http://localhost:8080/surveys/Survey1/questions
```xml
<List>
    <item>
        <id>Question1</id>
        <description>Largest Country in the World</description>
        <correctAnswer>Russia</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question2</id>
        <description>Most Populus Country in the World</description>
        <correctAnswer>China</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question3</id>
        <description>Highest GDP in the World</description>
        <correctAnswer>United States</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
    <item>
        <id>Question4</id>
        <description>Second largest english speaking country</description>
        <correctAnswer>India</correctAnswer>
        <options>
            <options>India</options>
            <options>Russia</options>
            <options>United States</options>
            <options>China</options>
        </options>
    </item>
</List>
```


### src/main/resources/application.properties
```.properties
spring.profiles.active=prod

logging.level.org.springframework: DEBUG

management.endpoints.web.exposure.include=*
app.name=Abdullah Khan
welcome.message=Hi ${app.name}, this message comes from properties file

basic.value: true
basic.message: Dynamic Message
basic.number: 100

spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```

### src/main/resources/application-prod.properties
```properties
logging.level.org.springframework: INFO
```
### src/main/resources/application-dev.properties
```properties
logging.level.org.springframework: TRACE
```


> spring init -d=web,actuator -n=actuator-demo actuator-demo

> brew install springboot

> mvn install