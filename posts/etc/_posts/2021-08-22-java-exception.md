---
layout: post
title: Java Exception
---

{% assign imgurl=site.baseurl |append: site.data.common.path.image|append: '/'|append: page.categories[1] %}

# 개요

개인적으로 Java 개발을 처음 할 때 가장 오래 걸렸던 것 중 하나가 예외처리이다. 주된 고민은 아래와 같은 것들이었다.

1. CheckedException vs UncheckedException 무엇을 사용할 지
2. CheckedException을 어떻게 처리할 것인가
3. UncheckedException은 어떻게 처리할 것인가



# Throwable

![java_exception01.png]({{ imgurl }}/java_exception01.png)<small>출처 : https://www.onlinetutorialspoint.com/java/checked-and-unchecked-exceptions-in-java.html</small>

우선 자바의 모든 `Exception` 클래스가 상속하는  `Throwable` 부터 알아보자.



## Error vs Exception

`Throwable` 은 위 그림처럼 `Error` 와 `Exception` 으로 나뉜다. 

둘의 가장 큰 차이점은 **'어플리케이션 수준에서 대응할 수 있는 문제인가'** 이다. 

```java
try {
  something()
} catch(Exception e) {
  logging()
  rollback()
}
```

예외 및 오류 상황 발생시 어플리케이션에서 catch 하여 무언가 대응할 수 있을만한 상황이면 `Exception` 을 사용한다.

```
public class ExceptionStudy {
    public static void main(String[] args) {
        try {
            recursiveCall(0);
        } catch (Error e) {
            System.out.println("Error occurs.");
        }
    }

    private static void recursiveCall(int index) {
        System.out.println(index);
        recursiveCall(index+1);
    }
}
```

`Error` 는 어플리케이션 수준에서 대응할 수 없는 예외 상황들에 사용한다. 위처럼 catch 해서 뭔가 대응할 수는 있다. 근데 그렇게 사용하는건 `Error` 를 잘못 사용 하고 있는 것이다. [java 공식 문서](https://docs.oracle.com/javase/8/docs/api/java/lang/Error.html)에서도 "합리적인 어플리케이션이라면 Error는 catch 하지 마세요" 라고 안내하고 있다. 

어플리케이션이 catch 해서 대응을 하는 것보다 죽는게 더 합리적인 상황일 정도의 상황에 `Error` 를 사용한다.



이 글의 주제는 예외처리이기 때문에 `Error` 들 중 그나마 좀 쉽게 볼 수 있는 것들을 나열하고 넘어간다.

- NoSuchMethodError : 라이브러리를 덕지덕지 가져다 쓰다보면 컴파일시 종종 마주치는 에러다.
- OutOfMemoryError : 메모리 부족. 마주치고 싶지 않은 에러다.
- StackOverflowError : 재귀함수를 배우다보면 한 번 쯤은 만나봤을 에러. 실제 어플리케이션에선 보기 힘들다.



## Checked Exception vs Unchecked Exception

다시 맨 위 그림으로 오면 `Exception` 이 `RuntimeException` 과 그 외의 `Exception` 으로 나뉘는 것을 볼 수 있다. 

`RuntimeExcpetion` 을 상속 받은 예외들을 Uncheked Exception 이라 부르고, 그외 나머지 `Exception` 들을 Checked Exception 이라고 부른다.



Checked, Unchecked의 뜻은 컴파일러가 체크 하는 지, 안 하는지이다.

```
    private static void catchCheckedException() {
        try {
            throw new Exception();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static void throwCheckedException() throws Exception {
        throw new Exception();
    }
```

Checked Exception의 경우에 컴파일러가 체크하기 때문에 try~catch 로 잡거나 throw로 던지길 요구한다. 

이 부분이 Checked Exception의 탄생 이유이기도 하고, Checked Exception vs Unchecked Exception 논쟁의 시작점이기도 하다.



### Checked Exception 탄생

이런 Checked Exception의 특징은 Java만의 특징이기도 하다.(내가 아는 범위에선 Checked Exception을 사용하는 다른 언어는 없다.) 

[checked exception에 관한 한 포스팅](https://www.infoworld.com/article/3142626/are-checked-exceptions-good-or-bad.html)에 따르면 Java를 만든 James Gosling이 Checked Exception을 만든 이유는, 개발자들이 예외처리를 잘 하지 못해 버그를 일으키는 코드를 많이 봤기 때문에 checked exception을 만들었다고 한다. 예외 처리를 컴파일 과정에서 강제하여 좀 더 견고한 소프트웨어를 만들도록 강제하겠는 의미인것 같다. 

그렇기 때문에 Checked Exception은 예측 가능하나 막을 수 없고, catch 절에서 무언가 할 수 있는 상황(복구 할 수 있는 상황)에 발생 시켜야 한다.(구글링 하며 찾은 Checked Exception을 설명하는 문장 중 가장 적합한 문장이라 생각 - [stackoverflow](https://stackoverflow.com/questions/27578/when-to-choose-checked-and-unchecked-exceptions))



### Checked Exception 반대파 형성

고슬링의 의도는 사람들이 Checked Exception을 잘 사용하여 적절한 곳에서 던지고, 그걸 catch 해서 잘 처리하는 것이었다. (하지만, C에서 버그를 만들던 사람들은 Java에서도 Exception을 남발하기 시작했다.) 

Checked Exception의 반대파들의 주된 의견들 중 몇 개를 가져와봤다. ([checked exception에 관한 한 포스팅](https://www.infoworld.com/article/3142626/are-checked-exceptions-good-or-bad.html))

1. Checked Excpetion을 무시하기 쉬움

   ```java
   try {
     // do stuff
   } catch (AnnoyingcheckedException e) {
     throw new RuntimeException(e);
   }
   
   
   ```

   위처럼 Checked Exception을 잡아서 Unchecked Exception을 다시 던질 수도 있고

   ```java
   try {
     // do stuff
   } catch (AnnoyingCheckedException e) {
     // do nothing
   }
   ```

   잡아서 아무 일도 안할 수도 있음

2. 코드의 복잡도 증가.

   ```java
       public void process1() throws ExceptionOne, ExceptionTwo, ExceptionThree, ExceptionFour {
           subProcess1();
           subProcess2();
           subProcess3();
           subProcess4();
       }
       
       public void process2() {
           try {
               subProcess1();
               subProcess2();
               subProcess3();
               subProcess4();
           } catch (Exception exceptionThree) {
               // ...something
           }
       }
   
       private void subProcess1() throws ExceptionOne {
           // ... something
           throw new ExceptionOne();
           // ... something
       }
       
       private void subProcess2() throws ExceptionTwo {
           // ... something
           throw new ExceptionTwo();
           // ... something
       }
   
       private void subProcess3() throws ExceptionThree {
           // ... something
           throw new ExceptionThree();
           // ... something
       }
   
       private void subProcess4() throws ExceptionFour {
           // ... something
           throw new ExceptionFour();
           // ... something
       }
   ```

   Checked Exception의 남발은 결국 사용하는 쪽의 코드 복잡도를 올릴 수 밖에 없음. 사용하는 쪽도 위처럼 다시 떠 넘기던가 모든 Checked Exception을 하나로 묶어 퉁치게 됨. 

3. 코드의 복잡성 증가 + 개방 폐쇄 원칙(OCP, open close principal)을 무시하게 됨

   Checked Exception은 사용하는 쪽의 코드 작성을 강제하기 때문에 OCP 원칙이 무시됨. Checked Exception을 만드는 쪽의 코드 수정이 여러 군데의 코드 수정을 유도하게 됨. (그런데 이건 Unchecked Exception을 사용해도 마찬가지 아닌가?)



# Checked Exception vs Unchecked Exception 결론

아직도 Checked Exception의 팬들이 있긴 하지만, 그래도 승리는 Unchecked Exception 인것 같다. 

일단, Checked Exception이 제대로 작동하려면 모든 개발자들이 Checked Exception을 잘 이해하고 잘 대처해야 한다는 가정이 불가능한 것 같다. 

그리고 Java 이후에 나온 언어들에서도 Checked Exception을 배제한 것을 보면 Java를 하면서도 Checked Exception은 멀리하는게 좋지 않을까 생각한다.

Unchecked Exception을 주로 쓰되, 예외 설명하는 주석을 잘 만들어 놓는 것으로!



# Checked Exception 어디서 잡을 것인가

결론과는 별개로, 처음에 했던 고민들에 대해 어느정도 답을 좀 내려놓아야 할 것 같다. 

우선, 내가 Checked Exception을 발생시키는 메서드를 호출하는 쪽일때 했던 고민이다. CheckedException을 던지는 라이브러리를 쉽게 볼 수 있다. 이런 라이브러리를 사용할 때면 이걸 나도 던져야 할지, 내가 잡아야 할 지 고민하게 된다. 

![java_exception02.png]({{ imgurl }}/java_exception02.png)<small>나에게 또 선택을 강요한다..</small>

Java를 처음 시작했을땐 고민을 많이 했지만, 지금 내린 결론은 가능하면 최대한 호출한 곳에서 처리하는 것이다.

다른 Checked Exception으로 만들어서 던지는 한이 있더라도, 호출한 곳에서 받은 Checked Exception을 다시 또 넘기는건 Checked Exception의 부적절한 사용이기도 하고, 내가 작성하는 코드의 책임 회피인 것 같다.





# Unchecked Exception 처리

또 다른 주요 고민거리는 Unchecked Exception을 어떻게 처리할 것인가였다. Unchecked Exception이 발생할거라고 이미 알고 있는 메서드들이 있다. 

null을 던졌을때 주로 나오는 `IllegalArgumentException` 나, Spring의 `RestTemplate` 을 이용할 때 나오는 `RestClientException` 들...

```java
    public void useRestTemplate() {
        RestTemplate restTemplate = new RestTemplate();
        try {
            restTemplate.getForEntity("http://somewhere", String.class);
        } catch (RestClientException e) {
            // ... logging
            throw new CustomRuntimeExcpetion(e.getMessage());
        }
    }
```

Unchecked Exception에 대한 결론도 호출한 쪽에서 처리하는 것이다. 해당 어플리케이션 안에서 약속된 Unchecked Exception (Exception Handler에서 잡아서 공통으로 처리하기로 약속한 Exception 등..)을 제외한 Java, 프레임워크, 라이브러리 등에서 만든 Unchecked Exception 들은 일단 다 잡아서 어떻게든 처리하는게 맞는 것 같다.

가장 좋은 처리 방법은 어플리케이션 안에서 다루는 RuntimeException으로 바꾸고 해당 RuntimeException을 처리하는 공통 로직을 만드는것 같다.





# 스프링 예외처리

```java
  protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
		HttpServletRequest processedRequest = request;
		HandlerExecutionChain mappedHandler = null;
		boolean multipartRequestParsed = false;

		WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

		try {
			ModelAndView mv = null;
			Exception dispatchException = null;

			try {
				processedRequest = checkMultipart(request);
				multipartRequestParsed = (processedRequest != request);

				// Determine handler for the current request.
				mappedHandler = getHandler(processedRequest);
				if (mappedHandler == null) {
					noHandlerFound(processedRequest, response);
					return;
				}

				// Determine handler adapter for the current request.
				HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

				// Process last-modified header, if supported by the handler.
				String method = request.getMethod();
				boolean isGet = "GET".equals(method);
				if (isGet || "HEAD".equals(method)) {
					long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
					if (new ServletWebRequest(request, response).checkNotModified(lastModified) && isGet) {
						return;
					}
				}

				if (!mappedHandler.applyPreHandle(processedRequest, response)) {
					return;
				}

				// Actually invoke the handler.
				mv = ha.handle(processedRequest, response, mappedHandler.getHandler());

				if (asyncManager.isConcurrentHandlingStarted()) {
					return;
				}

				applyDefaultViewName(processedRequest, mv);
				mappedHandler.applyPostHandle(processedRequest, response, mv);
			}
			catch (Exception ex) {
				dispatchException = ex;
			}
			catch (Throwable err) {
				// As of 4.3, we're processing Errors thrown from handler methods as well,
				// making them available for @ExceptionHandler methods and other scenarios.
				dispatchException = new NestedServletException("Handler dispatch failed", err);
			}
			processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
		}
		catch (Exception ex) {
			triggerAfterCompletion(processedRequest, response, mappedHandler, ex);
		}
		catch (Throwable err) {
			triggerAfterCompletion(processedRequest, response, mappedHandler,
					new NestedServletException("Handler processing failed", err));
		}
		finally {
			if (asyncManager.isConcurrentHandlingStarted()) {
				// Instead of postHandle and afterCompletion
				if (mappedHandler != null) {
					mappedHandler.applyAfterConcurrentHandlingStarted(processedRequest, response);
				}
			}
			else {
				// Clean up any resources used by a multipart request.
				if (multipartRequestParsed) {
					cleanupMultipart(processedRequest);
				}
			}
		}
	}
```

스프링의 요청 처리흐름은 `org.springframework.web.servlet.DispatcherServlet.doDispatch()` 에서 확인할 수 있다. 

(현재 블로그에선 코드 라인 번호가 없지만..) `mv = ha.handle(processedRequest, response, mappedHandler.getHandler());` 이 부분이 실제 요청을 처리하는 부분이다. 여기부터 우리가 작성한 컨트롤러와 서비스들이 일을 하게 된다.

`doDispatch()` 시작부분에서 `Exception dispatchException = null;` 발생한 Exception을 null로 두고 시작한다.

그 후, `ha.handle(..)` 중 발생한 예외는

```java
			catch (Exception ex) {
				dispatchException = ex;
			}
			catch (Throwable err) {
				// As of 4.3, we're processing Errors thrown from handler methods as well,
				// making them available for @ExceptionHandler methods and other scenarios.
				dispatchException = new NestedServletException("Handler dispatch failed", err);
			}
			processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
```

여기서 잡히는 걸 확인할 수 있다. 그리고 `doDispatch` 의 결과를 처리하는 `processDispatchResult` 메서드안을 들여다보면 스프링에서 어떻게 예외처리를 공통 로직으로 처리하는지를 확인할 수 있다.

```java
	private void processDispatchResult(HttpServletRequest request, HttpServletResponse response,
			@Nullable HandlerExecutionChain mappedHandler, @Nullable ModelAndView mv,
			@Nullable Exception exception) throws Exception {

		boolean errorView = false;

		if (exception != null) {
			if (exception instanceof ModelAndViewDefiningException) {
				logger.debug("ModelAndViewDefiningException encountered", exception);
				mv = ((ModelAndViewDefiningException) exception).getModelAndView();
			}
			else {
				Object handler = (mappedHandler != null ? mappedHandler.getHandler() : null);
				mv = processHandlerException(request, response, handler, exception);
				errorView = (mv != null);
			}
		}

		// Did the handler return a view to render?
		if (mv != null && !mv.wasCleared()) {
			render(mv, request, response);
			if (errorView) {
				WebUtils.clearErrorRequestAttributes(request);
			}
		}
		else {
			if (logger.isTraceEnabled()) {
				logger.trace("No view rendering, null ModelAndView returned.");
			}
		}

		if (WebAsyncUtils.getAsyncManager(request).isConcurrentHandlingStarted()) {
			// Concurrent handling started during a forward
			return;
		}

		if (mappedHandler != null) {
			// Exception (if any) is already handled..
			mappedHandler.triggerAfterCompletion(request, response, null);
		}
	}

	@Nullable
	protected ModelAndView processHandlerException(HttpServletRequest request, HttpServletResponse response,
			@Nullable Object handler, Exception ex) throws Exception {

		// Success and error responses may use different content types
		request.removeAttribute(HandlerMapping.PRODUCIBLE_MEDIA_TYPES_ATTRIBUTE);

		// Check registered HandlerExceptionResolvers...
		ModelAndView exMv = null;
		if (this.handlerExceptionResolvers != null) {
			for (HandlerExceptionResolver resolver : this.handlerExceptionResolvers) {
				exMv = resolver.resolveException(request, response, handler, ex);
				if (exMv != null) {
					break;
				}
			}
		}
		if (exMv != null) {
			if (exMv.isEmpty()) {
				request.setAttribute(EXCEPTION_ATTRIBUTE, ex);
				return null;
			}
			// We might still need view name translation for a plain error model...
			if (!exMv.hasView()) {
				String defaultViewName = getDefaultViewName(request);
				if (defaultViewName != null) {
					exMv.setViewName(defaultViewName);
				}
			}
			if (logger.isTraceEnabled()) {
				logger.trace("Using resolved error view: " + exMv, ex);
			}
			else if (logger.isDebugEnabled()) {
				logger.debug("Using resolved error view: " + exMv);
			}
			WebUtils.exposeErrorRequestAttributes(request, ex, getServletName());
			return exMv;
		}

		throw ex;
	}
```

`doDispatch` 결과를 `processDispatchResult` 에서 처리하는데, 이때 넘겨받은 Exception이 있다면 `processHandlerException` 를 호출하는걸 확인할 수 있다. (즉 RuntimeException를 던져도 스프링 어플리케이션 프로세스 자체가 죽는 일은 없다.)

`processHandlerException` 에선 에러를 핸들링할 적합한 핸들러를 찾고, 에러를 처리한다.

스프링 에러 핸들러를 등록하는 것들은 따로 첨부하지 않는다.  `@ControllerAdvice` , `@ExceptionHandler` 키워드로 검색하면 많이 나온다. 





# TODO

다음엔 다른 언어들의 Exception 처리를 좀 더 알아보고 비교해보고 싶다.
<br>


### 참고 링크

- [Are checked exceptions good or bad?](https://www.infoworld.com/article/3142626/are-checked-exceptions-good-or-bad.html)
- [Failure and Exceptions A Conversation with James Gosling, Part II](https://www.artima.com/articles/failure-and-exceptions)
- [Java에서 Checked Exception은 언제 써야 하는가?](https://blog.benelog.net/1901121)
- [When to choose checked and unchecked exceptions](https://stackoverflow.com/questions/27578/when-to-choose-checked-and-unchecked-exceptions)

