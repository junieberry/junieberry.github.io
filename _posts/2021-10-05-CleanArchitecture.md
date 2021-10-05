---
title: CleanArchitecture
author: Junyoung Kim
date: 2021-10-06
category: Android
layout: post
---


🤔**어떻게 하면 유지 보수가 쉽고 테스트가 용이한 코드를 짤 수 있을까?**

## 의존성 규칙

----------


![https://techblog.woowahan.com/wp-content/uploads/img/2019-10-02/the-clean-architecture.png](https://techblog.woowahan.com/wp-content/uploads/img/2019-10-02/the-clean-architecture.png)

클린 아키텍쳐의 목표는 계층을 분리하여 관심사를 분리해야 한다는 것이다. 이러한 아키텍쳐를 설계하기 위해 반드시 지켜야 하는 규칙이 **의존성 규칙**이다.

이때 의존성 규칙은, 항상 **외부 레이어에서 내부 레이어로, 저수준 정책에서 고수준 정책으로 향해야 한다는 것**이다. 이때 "수준"은 입력과 출력까지의 거리이다. 입력과 출력으로부터 멀어질 수록 정책의 수준이 높아진다. 고수준 정책은 입력과 출력으로부터의 거리가 멀다. 따라서 상위 수준의 개념, 혹은 추상화된 개념을 의미하다. 반대로 저수준 정책은 추상화된 개념을 실제 어떻게 구현할지에 대한 세부적인 개념을 의미한다.

## 클린 아키텍쳐 계층
---
클린 아키텍쳐는 총 4가지의 계층으로 이루어져 있다.

### 1. Entities
엔티티는 비즈니스 로직을 캡츌화한다. 가장 일반적이면서 고수준의 규칙을 캡슐화하며, 따라서 가장 안쪽 레이어에 위치해 바깥 레이어에서 변경사항이 생겨도 바뀌지 않는다.

### 2. Use cases
유스케이스는 시스템의 동작을 사용자의 입장에서 표현한 시나리오다. 해당 레이어는 엔티티와의 데이터 흐름을 조정하고, 엔티티가 유스케이스의 목표를 달성하도록 지시한다.
유스케이스는 엔티티에게 영향을 받지 않으며, 데이터베이스, UI, 프레임워크의 변경으로부터 영향을 받지 않는다. 

### 3. Interface Adapters (Presenters)
인터페이스 어댑터는 바깥 또는 안쪽으로 전달되는 모든 데이터를 데이터를 전달 받는 레이어에 맞게 변환시켜주는 역할을 한다.  유스케이스와 엔티티로부터, 데이터베이스나 웹 등 외부 기능에 용이한 형식으로 데이터를 변환한다. 프리젠터, 뷰, 컨트롤러는 모두 인터페이스 어댑터에 속한다.

### 4. Frameworks & Drivers (Web, DB)
가장 바깥쪽의 레이어로, 프레임워크나 도구로 구성된다.
- 네트워크
- UI
- 데이터베이스
- 라이브러리와 프레임워크
- I/O 장치

이 레이어는 빈번하게 변경된다.


## 안드로이드의 클린 아키텍쳐
----------
![enter image description here](https://miro.medium.com/max/2000/1*Y3c0eHFi4SHUJbhdOVdt7g.jpeg)

안드로이드의 계층은 일반적으로 **Presentation, Domain, Data** 총 3개의 계층으로 나눠지게 된다. 이때 Presentation ➡ Domain ➡ Data 방향으로 의존성을 갖고 있다.

### 1. Presentation
화면, 사용자와의 상호작용 등 UI와 관련된 것들을 포함한다. Activity, Fragment, View, Presenter 및 ViewModel 등이 있다.
	
1. View
	UI 화면과 사용자 입력 등을 담당한다.
	자신이 화면에 그리는 것의 의미는 알지 못하고 presenter의 명령을 받아 행동한다.
2. Presenter
	View의 사용자 입력 처리 등을 하며, 화면에 그리는 것이 어떤 의미를 갖는지 알고 있다. 플랫폼에 직접적으로 의존하지 않어 단위 테스트가 쉬우며, MVVM 구조의 ViewModel이 대표적인 예시이다.

### 2. Domain
비즈니스 로직에 필요한 UseCase와 Model을 포함하고 있다. 이때 안드로이드의 의존성 없이 java 혹은 kotlin으로만 구성되어 있어 다른 애플리케이션에서도 사용할 수 있다.
1. Usecase
	비즈니스 로직이 구현되는 부분이다.	
2. Translater
	Entity - Model을 변환해준다. 	
3. Model
	앱의 실질적인 데이터가 구현되는 부분이다.
	
### 3. Data
데이터베이스, 서버와의 통신 등을 맡고 있는 부분이다.
1. Repository
	유스케이스가 필요로 하는 데이터 저장/수정 등을 담당한다. 데이터 소스는 인터페이스 형태로, 이 클래스에서 데이터 소스 객체를 갈아 끼우며 외부 API 호출, 로컬 DB 접근, mock object 출력을 전환할 수 있다.
2. DataStore
	실제 데이터의 입출력
3. Entity
	데이터 소스에서 사용되는 데이터로, 클린 아키텍쳐의 네 가지 계층의 Entity와는 다른 개념이다. REST API의 요청, 응답을 위한 JSON이나 로컬 DB에 저장하기 위한 테이블 등이 있다.

## 클린 아키텍쳐 사용 이유
---
1. 유지보수가 쉽다.
	복잡한 수정 사항이 생겨도 어떤 부분을 수정해야 하는지 쉽게 파악할 수 있다.
2. 패키지/폴더 구성이 자연스럽게 트리 구조를 이룬다.
3. 특정 계층에 대한 수정이 다른 계층에 영향을 거의 주지 않는다.	


### Ref

[주니어 개발자의 클린 아키텍처 맛보기](https://techblog.woowahan.com/2647/)

[Clean Architecture는 모바일 개발을 어떻게 도와주는가? - (1) 경계선: 계층 나누기](https://medium.com/@justfaceit/clean-architecture%EB%8A%94-%EB%AA%A8%EB%B0%94%EC%9D%BC-%EA%B0%9C%EB%B0%9C%EC%9D%84-%EC%96%B4%EB%96%BB%EA%B2%8C-%EB%8F%84%EC%99%80%EC%A3%BC%EB%8A%94%EA%B0%80-1-%EA%B2%BD%EA%B3%84%EC%84%A0-%EA%B3%84%EC%B8%B5%EC%9D%84-%EC%A0%95%EC%9D%98%ED%95%B4%EC%A4%80%EB%8B%A4-b77496744616)

[클린 아키텍처에 대해 아라보자](https://velog.io/@ssionii/%ED%81%B4%EB%A6%B0-%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%84%EB%9D%BC%EB%B3%B4%EC%9E%90)