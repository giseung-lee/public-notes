---
layout: post
title: Javascript 실행 과정 및 비동기
---

{% assign imgurl=site.data.common.path.image|append: '/'|append: page.categories[1] %}

- 본 포스팅은 아래의 글을 참고했습니다.
  - [https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf)
  - [https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e](https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e)
  - [https://blog.sessionstack.com/how-javascript-works-event-loop-and-the-rise-of-async-programming-5-ways-to-better-coding-with-2f077c4438b5](https://blog.sessionstack.com/how-javascript-works-event-loop-and-the-rise-of-async-programming-5-ways-to-better-coding-with-2f077c4438b5)



## Javascript 엔진

- 자바스크립트 엔진이란 자바스크립트 코드를 해석하고 실행하는 프로그램 또는 인터프리터입니다. (다른 언어에선 컴파일러, 인터프리터라고 부르는데 왜 자바스크립트에선 엔진이라 부르는지는 모르겠습니다..)

- 자바스크립트 엔진은 대부분 웹 브라우저, Node JS에서 실행됩니다. (물론, 자바스크립트 엔진을 사용하는 다른 프로그램들도 있습니다.)

- 자바스크립트 엔진에 따라 구현하는 ECMAScript의 버전이 다릅니다. (흔히 말하는 ES6, ES7, ES8...)

- 유명한 자바스크립트 엔진엔 다음과 같은 것들이 있습니다.

  - V8 : 구글에서 개발, 크롬, NodeJS에서 사용중.
  - Rhino : 모질라재단에서 개발
  - SpiderMonkey : 최초의 자바스크립트 엔진, 파이어폭스에서 사용중
  - Chakra(JScript9) : 인터넷익스플로러에서 사용
  - Chakra(Javascript) : 마이크로소프트 엣지에서 사용
  - Jerryscript : IoT 에서 많이 사용

  

## V8

- 대부분 엔진은 비슷하게 동작하지만, 엔진마다 다소간의 차이가 있으므로 현재 가장 유명한 엔진인 V8엔진을 알아보겠습니다.

### 구성요소

{: .p_img}

![v8-engine.png]({{ imgurl }}/v8-engine.png)<small>출처 : https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf</small>

- V8 엔진은 위와 같이 두 부분으로 이루어집니다.
  - 메모리 힙(Memory Heap) : 다른 언어들의 힙 메모리 처럼 메모리 할당이 이루어집니다.
  - 콜 스택(Call Stack) : 다른 언어들의 스택 메모리 처럼 함수의 스택 프레임이 쌓입니다.
- 그런데, 우리가 실제로 자바스크립트를 이용할 땐 V8 엔진만 가지고 동작하는 것은 아닙니다.

{: .p_img}

![v8-engine-web-api.png]({{ imgurl }}/v8-engine-web-api.png)<small>출처 : https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf</small>

- DOM, AJAX(XMLHttpRequest), Timeout과 같이 자바스크립트를 사용하면 반드시 마주치게 되는 것들이 브라우저의 Web API에서 지원되는 것들입니다. Web API에 대한 자세한 사항은 [크롬의 Web API 문서](https://developer.chrome.com/apps/api_other)를 참조해주세요.
- Event Loop와 Callback Queue는 뒤에서 살펴볼 자바스크립트의 비동기에 있어 필수적인 것입니다.

### Call Stack

- 자바스크립트는 싱글 스레드 언어라는 말을 들어보셨을 겁니다. 대부분 언어에서 한 스레드당 하나의 스택 메모리를 할당 받는데, 자바스크립트는 오직 하나의 콜 스택을 갖습니다. 때문에, 자바스크립트를 싱글 스레드 언어라고 부릅니다.
- 콜 스택의 작동방식은 다른 언어의 스택 메모리와 크게 다르지 않습니다.



## Javascript 비동기

- 비동기 방식은 한 개의 작업을 기다리는 동안 다른 작업을 수행할 수 있는것입니다. 그런데 스택 메모리가 1개인데 어떻게 한개를 기다리며 다른 작업을 할 수 있을까요?
- Web API와 Callback Queue, Event Loop를 통해 가능합니다.
  - Web API는 앞서 말했듯이 브라우저 차원에서 지원해주는 SetTimeOut, DOM 같은 API입니다.
  - Callback Queue는 onClick, onLoad, onDone과 같은 이벤트와 콜백 함수들이 저장되는 곳입니다.
  - Event Loop는 JS의 Call Stack과 브라우저의 Callback Queue를 감시하고 있다가 Call Stack이 비었을 때 Callback Queue에 있는 작업을 Call Stack에 옮깁니다.

{: .p_img}

![v8-engine-web-api.png]({{ imgurl }}/v8-engine-web-api.png)<small>출처 : https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf</small>

- 자바스크립트가 싱글스레드라는 것은 위 그림에서 JS의 Call Stack이 하나라는 것입니다. Web API는 별도의 스레드를 가지고 있습니다.
- 비동기 작업(SetTimeOut이나 외부 API 호출 등..)은 Web API의 스레드로 옮겨집니다. 
- 작업이 끝나고 이후의 작업을 할 준비가 됐다면 Callback Queue로 옮겨집니다.
- Event Loop가 Call Stack을 보고있다가 Callback Queue의 콜백 함수를 Call Stack에 옮겨줍니다.
- [latentflip](http://latentflip.com/loupe/?code=JC5vbignYnV0dG9uJywgJ2NsaWNrJywgZnVuY3Rpb24gb25DbGljaygpIHsKICAgIHNldFRpbWVvdXQoZnVuY3Rpb24gdGltZXIoKSB7CiAgICAgICAgY29uc29sZS5sb2coJ1lvdSBjbGlja2VkIHRoZSBidXR0b24hJyk7ICAgIAogICAgfSwgMjAwMCk7Cn0pOwoKY29uc29sZS5sb2coIkhpISIpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lb3V0KCkgewogICAgY29uc29sZS5sb2coIkNsaWNrIHRoZSBidXR0b24hIik7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coIldlbGNvbWUgdG8gbG91cGUuIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)에서 코드의 진행에 따라 각 메모리가 어떻게 변하는 지 확인할 수 있습니다.