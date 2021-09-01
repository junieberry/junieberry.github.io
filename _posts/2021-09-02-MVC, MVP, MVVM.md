---
title: MVC, MVP, MVVM
author: Junyoung Kim
date: 2021-09-02
category: Android
layout: post
---



### 디자인 패턴

디자인 패턴이란 소프트웨어를 설계할 때 특정 맥락에서 자주 발생하는 문제를 해결하기 위한 재사용하기 좋은 해결책이다. **효율적인 코드를 만들기 위한 방법론**이라고 할 수 있다.

디자인 패턴 중 `MVC`, `MVP`, `MVVM` 패턴을 다뤄보려고 한다.

```
📌 MVC = Model + View + Controller
📌 MVP = Model + View + Presenter
📌 MVVM = Model + View + ViewModel
```

세 디자인 패턴 모두 공통적으로 `Model` 과 `View` 가 들어가는 것을 볼 수 있다. `Model`은 비즈니스 로직을 담당하고, `View`는 프레젠테이션 로직을 담당한다. 앱이 커질수록 `Model` 과 `View` 사이의 의존성은 커지게 되고, 의존성이 커질 수록 유지 보수 및 테스트는 점점 어려워진다.

-   의존성이 커지면 좋지 않은 이유
    
    A와 B 사이의 의존성이 커지면 A가 수정됐을때 B 또한 크게 영향 받게 된다. 즉 A 코드를 수정하면 B 코드 또한 수정해야 한다는 뜻이다!
    

따라서 화면에 보여주는 로직과 실제 데이터가 처리되는 로직을 분리해서 코드의 재활용성을 높이고 불필요한 중복을 막기 위해 `MVC`, `MVP` , `MVVM` 같은 디자인 패턴들이 등장하게 되었다.

----------

## MVC

![Model-View-Controller | Wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a0/MVC-Process.svg/200px-MVC-Process.svg.png)

**👊구조**

**📌 Model**
프로그램에서 사용되는 실제 데이터 및 데이터 조작 로직을 처리하는 부분이다. Model이 변하면 View를 업데이트한다.

**📌 View**
데이터를 표현하는 부분이다. 같은 데이터가 여러 뷰를 가질 수 있다.

**📌 Controller**
사용자의 입력을 받고 Model 혹은 View에게 명령을 전달한다. Controller는 View와 1:n 관계를 갖는다.

### ✍동작
1. 사용자의 Action이 Controller에 들어온다.
2. Controller는 Action을 기반으로 Model을 업데이트한다.
3. Controller는 화면에 보여질 View를 선택한다.
4. View는 업데이트된 Model을 기반으로 UI를 갱신한다.

안드로이드는 Activity나 Fragment 안에 View와 Controller가 모두 위치하고 있어서 MVC 패턴을 이해할때 어려움이 있다. 사실 웹의 MVC와 안드로이드의 MVC는 완전 다르다! 일반적인 MVC는 웹의 MVC인 것 같다. 이 [포스팅](https://awesometic.tistory.com/18)에서 웹과 안드로이드에서의 MVC 패턴을 같이 보면 이해가 더 잘 될 것 같다.

### 👍장점
- 단순하다!

### 👎단점
- View와 Model이 서로 의존적이다.
- 안드로이드는 하나의 클래스 안에 View와 Controller가 모두 포함되어 코드가 길어진다.

---


## MVP
MVC의 View와 Model 사이의 의존성 문제를 해결하기 위해 파생된 디자인 패턴이다. 안드로이드 개발에서 가장 많이 사용된다고 한다.

![Model-View-Presenter | Wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/d/dc/Model_View_Presenter_GUI_Design_Pattern.png/220px-Model_View_Presenter_GUI_Design_Pattern.png)

### 👊구조

**📌 Model**
MVC와 동일하다.

**📌 View**
MVC와 동일하다.

**📌 Presenter**
MVC의 Controller와 비슷하지만 단순한 인터페이스이다. Model과 View는 각각 Presenter과 상호작용하며, 둘 사이에 Presenter이 있기 때문에 서로에 대해서 알 필요가 없다. View와 1:1 관계를 갖는다.

### ✍동작
1. 사용자의 Action이 View로 전달된다.
2. Presenter이 View로부터 Action을 전달 받는다.
3. Presenter이 Action을 기반으로 Model에게 데이터를 요청하거나 Model을 업데이트한다.
4. Model은 Presenter에게 필요한 데이터를 응답하거나 업데이트한다.
5. Presenter은 View에게 데이터를 응답한다.
6. View는 UI를 갱신한다.



### 👍장점
- M-V 사이에 Presenter가 존재해 MVC패턴과 다르게 Model과 View 사이의 의존성이 없다.

### 👎단점
- View와 Presenter의 의존성이 커진다.
- 필요한 클래스 개수가 많다.


## MVVM
MVC의 Model과 View 사이의 의존성과 Controller과 View 사이의 의존성 문제를 해결하기 위해 파생된 디자인 패턴이다. 

![Model-View-Viewmodel | Wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/8/87/MVVMPattern.png/500px-MVVMPattern.png)

### 👊구조

**📌 Model**
MVC, MVP와 동일하다.

**📌 View**
View model과 바인딩되어 View model을 화면에 보여주고 사용자의 Action을 View model에 전달한다. 

**📌 View model**
View에 필요한 model이다. 안드로이드 aac의 ViewModel과는 다른 개념이다.

**MVVM 사이의 관계**
- View model은 model을 알지만 View를 알지 못한다.
- View는 Model을 알지 못하지만 View model은 알고 있다.
- View는 View Model을 관찰하고 있다 변화가 감지되면 화면을 갱신한다. 이를 가능케 해주는 것이 DataBinding이다.

### ✍동작
1. 사용자의 Action이 View로 전달된다.
2. Command 패턴을 통해 View model에게 입력을 전달한다.
3. View model은 Model에게 필요한 데이터를 요청한다.
4. Model은 View model에게 데이터를 응답한다.
5. View는 View model과의 Data Binding으로 자동으로 갱신된다.



### 👍장점
- Model과 View 사이의 의존성이 없다.
- View model과 View 사이의 의존성이 없다.


## TL;DR
```
MVC는 Model-View-Controller로 이루어진 패턴으로, Controller에서 입력을 받아 Model과 View를 컨트롤한다. MVP는 Model-View-Presenter로 이루어진 패턴으로, View와 Model 사이에 Presenter이 위치해 의존도를 낮췄다. 마지막으로 MVVM은 Model-View-View model로 이루어진 패턴으로, DataBinding 등을 사용해 각 요소의 의존성을 낮췄다.
```

각 패턴의 구성 요소들이 어떤 역할을 하고 각 계층이 어떻게 분리되는지 이해하는 게 중요한 것 같다. 어렵다.. 공부를 더 하고 내용을 더 추가해봐야할것 같다.

### Ref
[MVC, MVP, MVVM 바로알기](https://62che.com/blog/architecture/mvc-mvp-mvvm-%EB%B0%94%EB%A1%9C%EC%95%8C%EA%B8%B0.html#mvvm)

[MVC, MVP, MVVM, MVI](https://brunch.co.kr/@oemilk/113)

[MVC, MVP, MVVM 비교](https://magi82.github.io/android-mvc-mvp-mvvm/)

[안드로이드 - MVC 패턴 개념, 안드로이드에서 MVC 패턴의 미묘함](https://awesometic.tistory.com/18)