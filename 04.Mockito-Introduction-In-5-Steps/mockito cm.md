# Mockito

## Step 2
# Simple unit test using junit

# MockitoSpringBoot212Application
```java
package com.abdullah.khan.mockitospringboot212;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MockitoSpringBoot212Application {

	public static void main(String[] args) {
		SpringApplication.run(MockitoSpringBoot212Application.class, args);
	}
}

```
# DataService
```java
package com.abdullah.khan.mockitospringboot212;

public interface DataService {
	int[] retrieveAllData();

}

```

# SomeBusinessImpl
```java
package com.abdullah.khan.mockitospringboot212;

public class SomeBusinessImpl {
	private DataService dataService;
	
	
	
	public SomeBusinessImpl(DataService dataService) {
		super();
		this.dataService = dataService;
	}



	int findTheGreatestFromAllData() {
		int[] data = dataService.retrieveAllData();
		int greatest = Integer.MIN_VALUE;

		for (int value : data) {
			if (value > greatest) {
				greatest = value;
			}
		}
		return greatest;
	}

}

```
# SomeBusinessTest
```java
package com.abdullah.khan.mockitospringboot212;

import static org.junit.Assert.*;

import org.junit.Test;

public class SomeBusinessTest {

	@Test
	public void testFindTheGreatestFromAllData() {
		SomeBusinessImpl businessImpl = new SomeBusinessImpl(new DataServiceStub());
		int result = businessImpl.findTheGreatestFromAllData();
		assertEquals(24, result);
	}
}
class DataServiceStub implements DataService {
	@Override
	public int[] retrieveAllData() {
		return new int[] { 24, 6, 15 };
	}
}

```

## Step 3

# Unit test usnig Mokito mock data
# MockitoSpringBoot212Application
```java
package com.abdullah.khan.mockitospringboot212;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MockitoSpringBoot212Application {

	public static void main(String[] args) {
		SpringApplication.run(MockitoSpringBoot212Application.class, args);
	}
}

```
# DataService
```java
package com.abdullah.khan.mockitospringboot212;

public interface DataService {
	int[] retrieveAllData();

}

```

# SomeBusinessImpl
```java
package com.abdullah.khan.mockitospringboot212;

public class SomeBusinessImpl {
	private DataService dataService;
	
	
	
	public SomeBusinessImpl(DataService dataService) {
		super();
		this.dataService = dataService;
	}



	int findTheGreatestFromAllData() {
		int[] data = dataService.retrieveAllData();
		int greatest = Integer.MIN_VALUE;

		for (int value : data) {
			if (value > greatest) {
				greatest = value;
			}
		}
		return greatest;
	}

}

```
# SomeBusinessMockTest
```java
package com.abdullah.khan.mockitospringboot212;

import static org.junit.Assert.assertEquals;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.when;

import org.junit.Test;

public class SomeBusinessMockTest {
	@Test
	public void testFindTheGreatestFromAllData() {
		DataService dataServiceMock = mock(DataService.class);
		when(dataServiceMock.retrieveAllData()).thenReturn(new int[] { 24, 15, 3 });
		SomeBusinessImpl businessImpl = new SomeBusinessImpl(dataServiceMock);
		int result = businessImpl.findTheGreatestFromAllData();
		assertEquals(24, result);
	}

	@Test
	public void testFindTheGreatestFromAllData_ForOneValue() {
		DataService dataServiceMock = mock(DataService.class);
		when(dataServiceMock.retrieveAllData()).thenReturn(new int[] { 15 });
		SomeBusinessImpl businessImpl = new SomeBusinessImpl(dataServiceMock);
		int result = businessImpl.findTheGreatestFromAllData();
		assertEquals(15, result);
	}

}

```

# Step 4

# Unit test usnig Mokito annotation
# MockitoSpringBoot212Application
```java
package com.abdullah.khan.mockitospringboot212;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MockitoSpringBoot212Application {

	public static void main(String[] args) {
		SpringApplication.run(MockitoSpringBoot212Application.class, args);
	}
}

```
# DataService
```java
package com.abdullah.khan.mockitospringboot212;

public interface DataService {
	int[] retrieveAllData();

}

```

# SomeBusinessImpl
```java
package com.abdullah.khan.mockitospringboot212;

public class SomeBusinessImpl {
	private DataService dataService;
	
	
	
	public SomeBusinessImpl(DataService dataService) {
		super();
		this.dataService = dataService;
	}



	int findTheGreatestFromAllData() {
		int[] data = dataService.retrieveAllData();
		int greatest = Integer.MIN_VALUE;

		for (int value : data) {
			if (value > greatest) {
				greatest = value;
			}
		}
		return greatest;
	}

}

```
# SomeBusinessMockAnnotationsTest
```java
package com.abdullah.khan.mockitospringboot212;

import static org.junit.Assert.assertEquals;
import static org.mockito.Mockito.when;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.MockitoJUnitRunner;

@RunWith(MockitoJUnitRunner.class)
public class SomeBusinessMockAnnotationsTest {

	@Mock
	DataService dataServiceMock;

	@InjectMocks
	SomeBusinessImpl businessImpl;

	@Test
	public void testFindTheGreatestFromAllData() {
		when(dataServiceMock.retrieveAllData()).thenReturn(new int[] { 24, 15, 3 });
		assertEquals(24, businessImpl.findTheGreatestFromAllData());
	}

	@Test
	public void testFindTheGreatestFromAllData_ForOneValue() {
		when(dataServiceMock.retrieveAllData()).thenReturn(new int[] { 15 });
		assertEquals(15, businessImpl.findTheGreatestFromAllData());
	}

	@Test
	public void testFindTheGreatestFromAllData_NoValues() {
		when(dataServiceMock.retrieveAllData()).thenReturn(new int[] {});
		assertEquals(Integer.MIN_VALUE, businessImpl.findTheGreatestFromAllData());
	}
}
```
# Step 5
# List

# ListTest
```java
import static org.junit.Assert.assertEquals;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.when;

import java.util.List;

import org.junit.Test;
import org.mockito.Mockito;

public class ListTest {

	@Test
	public void testSize() {
		List listMock = mock(List.class);
		when(listMock.size()).thenReturn(10);
		assertEquals(10, listMock.size());
		assertEquals(10, listMock.size());
	}

	@Test
	public void testSize_multipleReturns() {
		List listMock = mock(List.class);
		when(listMock.size()).thenReturn(10).thenReturn(20);
		assertEquals(10, listMock.size());
		assertEquals(20, listMock.size());
		assertEquals(20, listMock.size());
	}

	@Test
	public void testGet_SpecificParameter() {
		List listMock = mock(List.class);
		when(listMock.get(0)).thenReturn("SomeString");
		assertEquals("SomeString", listMock.get(0));
		assertEquals(null, listMock.get(1));
	}

	@Test
	public void testGet_GenericParameter() {
		List listMock = mock(List.class);
		when(listMock.get(Mockito.anyInt())).thenReturn("SomeString");
		assertEquals("SomeString", listMock.get(0));
		assertEquals("SomeString", listMock.get(1));
	}
}
```
