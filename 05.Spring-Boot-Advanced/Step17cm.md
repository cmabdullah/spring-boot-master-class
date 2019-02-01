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