---
title: "Powermock with JUnit 4 to Test Spring+Hibernate Architecture"
date: "2013-11-20T17:28:42"
template: "post"
draft: false
slug: "/posts/powermock-with-junit-4-to-test-spring-hibernate-architecture"
category: "Programming"
tags:
  - mock
  - java
description: "I've been working on developing this webapp project. Basically, we break the logical parts of the code into entity layer, service layer and UI layer. The data structure is not that complex, so we thought that we use Hibernate ORM to do its magic. All the plumbing works between these layers were managed via Spring dependency injection."
---

I've been working on developing this webapp project. Basically, we break the logical parts of the code into entity layer, service layer and UI layer. The data structure is not that complex, so we thought that we use Hibernate ORM to do its magic. All the plumbing works between these layers were managed via Spring dependency injection.

We use Vaadin (built on top GWT) as our presentation layer where I've tried to enforce supervising view controller design pattern for better view and the associated view controller separation. I hope, this will make unit and integration testing easier in the future.

Now, I have taken the approach where I create the application without unit test, simply because we need to deliver the application for prelimanary review in shortest amount of time. So now, I'm starting to get a little bit serious on unit testing the components in the application.

For my testing requirement, I am planning to use JUnit 4 that play nicely with Spring frameworks. But, there are things that are still coupled between service and presentation layer, for example.

To be more specific, the web presentation layer will automatically capture the logged in user details (including things like IP address and web browser details). We need a mocking framework to work with current JUnit assertion tests.

I have reviewed some awesome Java mocking libraries such as [EasyMock](http://easymock.org) and [Mockito](https://code.google.com/p/mockito/), but I found the best of mocking libraries called: [PowerMock](https://code.google.com/p/powermock/).

What really nice for `PowerMock` is (taken directly from their website):

> PowerMock uses a custom classloader and bytecode manipulation to enable **mocking of static methods**, constructors, final classes and methods, private methods, removal of static initializers and more...

So, I can mock my static method to retrieve the logged in user information with just 2 simple line of code:

```java
User currentlyLoggedUser = new User('Some dummy user');
mockStatic(SpringSecurityHelper.class);
when(SpringSecurityHelper.getCurrentUser())
        .thenReturn(currentlyLoggedUser);
```

However, PowerMock does not play nicely with `SpringJUnit4ClassRunner.class` JUnit runner. Even though PowerMock tried to mitigate this problem by bootstraping JUnit rule  [PowerMockRule](https://code.google.com/p/powermock/wiki/PowerMockRule), Hibernate does not like this. I suspect because Hibernate relies on on [CGLib](http://cglib.sourceforge.net) to create some proxies to the entity classes by manipulating bytecode during runtime, which exactly what PowerMock do. If you're interested on sample code, somebody wrote a blog post on how to do that http://www.jayway.com/2010/12/28/using-powermock-with-spring-integration-testing/. I think, if you're not using Hibernate, it's a really nice, clean way of writing a unit test.

So, ultimately, what I did was to run the JUnit test with `PowerMockRunner.class`, but manually initiate the Spring context on `@Before` method. It works beautifully.

I wrote a simple example below to demonstrate what I did on actual code.


```java
@RunWith(PowerMockRunner.class)
@PrepareForTest(value={SpringSecurityHelper.class})
public class JUnitWithPowermock {
	private static final String SPRING_APPLICATION_CONTEXT_PATH = "src/main/webapp/WEB-INF/root-context.xml";
	
	private DocumentService documentService;
	private User currentlyLoggedUser;
	
	@Before
	public void setUp() throws Exception {
		ApplicationContext appContext = new FileSystemXmlApplicationContext(SPRING_APPLICATION_CONTEXT_PATH);
		documentService = (PrincipalService) appContext.getBean("documentService");
		
		currentlyLoggedUser = mock(User.class);
		when(currentlyLoggedUser.getEmail()).thenReturn("dummy@test.com");
		when(currentlyLoggedUser.getName()).thenReturn("A Dummy User");
		when(currentlyLoggedUser.getRole()).thenReturn(Role.INFORMATION_MANAGER);
		when(currentlyLoggedUser.getUsername()).thenReturn("dummy");
		
		mockStatic(SpringSecurityHelper.class);
		when(SpringSecurityHelper.getCurrentUser()).thenReturn(currentlyLoggedUser);
	}
	
	@Test
	public void testCreateDocument() throws Exception {
		Document doc = documentService.create("Invoice 123456");
		Assert.assertEquals("Invoice 123456", doc.getTitle());
		Assert.assertEquals("Mocked user should be automatically captured", currentlyLoggedUser, doc.getAuthor());
	}
	
}
```
