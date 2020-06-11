---
layout: post
title: 우분투(리눅스) GtkWarning(작성중)
---

{% assign imgurl=site.imgbase|append: page.categories[-1] %}


### 1. GtkWarning

---

- 우분투를 다루다 보니 ```GtkWarning``` 라고 뜨며 GUI들이 안켜질 때가 있습니다.

  - Gtk는 **G**imp **T**ool**K**it 이라고 합니다. GUI를 만들기 위한 라이브러리라고 보면 됩니다.([https://www.gtk.org/](https://www.gtk.org/))
    - Gimp는 그림 편집 소프트웨어이며 [GNOME]([https://ko.wikipedia.org/wiki/%EA%B7%B8%EB%86%88](https://ko.wikipedia.org/wiki/그놈)) 프로젝트의 일환입니다.

- 해당 문제는 아래와 같은 권한 문제입니다.

  1. 우분투의 디스플레이 환경은 GNOME을 따릅니다.
  2. 근데 GNOME은 [X 서버](https://ko.wikipedia.org/wiki/X_윈도_시스템) 기반입니다.
  3. 우분투 사용자가 X 서버에 대한 권한이 없습니다.

- xhost 명령어로 X서버에 대한 권한을 주어야 합니다.

  ```
  xhost +si:localuser:root
  xhost +si:localuser:{ubuntuUserName}
  ```

  - xhost는 X서버 접근 허용 목록에 사용자를 추가하는 명령어 입니다.
  - +si는 Server Interpreted에 추가한다는 것입니다. 
  - 자세한 사항은 X서버 [명세](https://www.x.org/archive/X11R7.5/doc/man/man1/xhost.1.html)를 남깁니다.



### 2. xhost:  unable to open display "" ??? :sweat:

---

- ```xhost:  unable to open display ""``` 에러가 뜰 수 있습니다. 제가 그랬거든요..

- 이는 보통 우분투를 설치했을땐 GUI가 없는 CLI 버전으로 설치되기 때문에 GUI를 띄우기 위한 온갖 것들이 부족해서 입니다.

- 우분투 GUI를 설치해 줍시다. https://wlsvud84.tistory.com/26

  