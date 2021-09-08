---
title: Observer Pattern
author: Junyoung Kim
date: 2021-09-09
category: Android
layout: post
---


## 옵저버 패턴

> 한 객체(Subject)의 상태가 바뀌면 그 객체에 의존하는 다른 객체(Observer)들한테 연락이 가고 데이터가 업데이트되는 방식으로 일대다(one-to-many) 의존성을 정의합니다.


👀 옵저버는 감시자, 관찰자라는 뜻의 영어단어이다. 옵저버 패턴에서는 Observer가 이름 그대로 데이터, 혹은 이벤트를 발생시키는 Subject를 관찰하며, Subject는 이벤트가 발생했을때 자신을 관찰하는 Observer에게 이벤트를 통지해준다.

    1️⃣ Observer는 Subject을 구독한다.
    2️⃣ Observer에 이벤트가 발생한다.
    3️⃣ Subject을 구독한 Observer에게 이벤트가 전달된다.



📄 옵저버 패턴은 구독 패턴이라고도 하는데, 이를 신문 구독으로 비유하면 Subject는 신문사, Observer은 신문을 구독한 사람, 그리고 event는 신문이 된다. 한 번 신문사에게 신문 구독 신청을 하고 나면, 신문사에서 신문이 발행될 때마다 신문을 구독한 모든 사람에게 신문을 보내준다. 

### Push 방식과 Pull 방식

Pull 방식은 Observer가 원할때 Subject로부터 값을 가져오는 방식이고 Push 방식은 이벤트가 발생하면 Subject가 Observer에게 값을 푸시해주는 방식이다.

---



## 옵저버 패턴 사용 이유

그렇다면 옵저버 패턴은 왜 사용하는 걸까? [귀여운 그림과 함께 옵저버 패턴의 사용 이유를 잘 설명하고 있는 포스팅](https://velog.io/@hanna2100/%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4-2.-%EC%98%B5%EC%A0%80%EB%B2%84-%ED%8C%A8%ED%84%B4-%EA%B0%9C%EB%85%90%EA%B3%BC-%EC%98%88%EC%A0%9C-observer-pattern)이 있어서 그 예시를 가지고 와봤다!


🌞우리가 만들고 싶은건 현재 날씨와 날씨 예보, 기상 통계 화면을 가진 날씨 앱이다. 이 앱은 기상청에서 제공되는 날씨 데이터 `WeatherData` 객체를 받아와 사용하며, 각 화면들은 데이터가 업데이트 되면 실시간으로 갱신된다.

먼저 `WeatherData` 는 이렇게 생겼다.

```kotlin
class WeatherData {
	getTemperature()
	getHumidity()
	getPressure()
	measurementsChanged()
}
```

`getTemperature` , `getHumidity`, `getPressure` 은 각각 온도, 습도, 기압을 가지고 오는 메서드이고, `measurementsChanged()` 메서드는 기상 관측값이 갱신되면 호출되는 메서드이다. 

이제 이 객체를 활용해 UI를 갱신해야 한다! 그런데...

### 🙄 뭔가 잘못됐다...

```kotlin
class WeatherData {
	...
	measrementsChanged(){
		val temp = getTemperature()
		val humidity = getHumidity()
		val pressure = getPressure()
		
		currentConditionDisplay.update(temp, humidity, pressure)
		statisticDisplay.update(temp, humidity, pressure)
		forecastDisplay.update(temp, humidity, pressure)
	}
}
```


1. `WeatherData` 를 가져다 쓰는 UI가 추가되면 `WeatherData`의 코드를 수정해야 한다.
2. 위와 같은 이유로 런타임 중 `WeatherData` 를 가져다 쓰는 UI를 추가할 수 없다.
3. 데이터가 바뀌는 부분이 캡슐화되지 않았다.


> 📌 캡슐화는 외부에서 특정 속성이나 메서드를 사용할 수 없도록 숨겨놓은 것이다! 외부 객체는 캡슐화된 객체 내부의 구조를 얻지 못하며 객체가 노출해서 제공하는 필드와 메서드만 이용할 수 있다.

위의 문제를 해결하기 위해서 **옵저버 패턴은 Subject와 Observer 사이에 인터페이스를 하나 끼워넣어 둘을 분리한다**. Observer는 인터페이스를 상속해 이벤트가 발생될 때마다 실행되게 할 콜백 메서드를 구현한다. 그리고 Subject는 이벤트가 발생할 때마다 인터페이스 메서드를 구현하게 된다.

### 😌 옵저버 패턴 적용

Subject Pseudo code
```kotlin
interface Subject {
	registerObserver()
	unregisterObserver()
	notifyObservers()
}

class WeatherData : Subject {
	registerObserver() {...}
	unregisterObserver() {...}
	notifyObservers() {
		for (observer in observers) {
			observer.update(t, h, p)
			}
	}
	measurementsChanged(){
		notifyObservers()
	}
}
```
Observer Pseudo code
```kotlin
interface Observer {
	update()
}

class currentConditionDisplay: Observer {
	update(temperature, humidity, pressure){
		updateUI()
	}
	updateUI()
}
class statisticDisplay: Observer {...}
class forecastDisplay: Observer {...}
```

---

## 옵저버 패턴의 구조
![Observer pattern](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8d/Observer.svg/854px-Observer.svg.png)

위의 예제에서 잠깐 나온 옵저버 패턴의 구조이다. Subject는 자신을 구독한 Observer의 List와 `registerObserver()`, `unregisterObserver()` 등 옵저버 객체들을 추가하거나 삭제하는 메서드를 갖고 있다. 이때 Subject에서 이벤트가 발생하면 `notifyObservers()` 메서드가 호출되며 목록 내의 옵저버 객체들에게 이벤트 발생을 알린다.
Observer는 `update` 메서드를 갖고 있는 인터페이스이다. 실제 Observer들은 다이어그램 내의 `ConcreteObserver` 이며, Observer 인터페이스를 구현한다.


---

## 느슨한 결합 (Loose Coupling)

옵저버 패턴에서의 포인트는 **Subject와 Observer를 연결하는 인터페이스이다.** 인터페이스는 Subject 객체와 Observer 객체가 느슨하게 결합되어 있도록 해준다.

두 객체가 느슨하게 결합되어 있다는 뜻은 두 객체가 상호작용을 하긴 하지만 서로에 대해 잘 모른다는 뜻이다.  Subject와 Observer은 옵저버 패턴 내에서 서로 얼마나 모르며, 이로 인해 얻는 이점은 무엇일까? 🤔

1. Subject는 Observer의 인터페이스 밖에 모른다.
2. Observer는 언제든지 추가, 삭제할 수 있다.
3. 새로운 형식의 Observer의 옵저버를 추가해도 Subject에 영향을 주지 않는다.
4. Subject와 Observer는 서로 독립적으로 사용이 가능하다.
5. Subject 혹은 Observer가 바뀌어도 서로 영향을 미치지 않는다.


두 객체 사이의 상호 의존성을 최소화하므로써 변경 사항에 유연하게 대처할 수 있게 된다. 💨


 
## TL;DR
 ```
 옵저버 패턴을 통해 Subject와 Observer의 의존성을 줄일 수 있다.
```
 


## REF
[[디자인패턴] 2. 옵저버 패턴 개념과 예제 (observer pattern)](https://velog.io/@hanna2100/%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4-2.-%EC%98%B5%EC%A0%80%EB%B2%84-%ED%8C%A8%ED%84%B4-%EA%B0%9C%EB%85%90%EA%B3%BC-%EC%98%88%EC%A0%9C-observer-pattern)

[Head First : Design Patterns - 제2강 : Observer 패턴 ](https://secretroute.tistory.com/entry/Head-First-Design-Patterns-%EC%A0%9C2%EA%B0%95-Observer-%ED%8C%A8%ED%84%B4)

[# 옵저버 패턴 개념 떠먹여드립니다](https://velog.io/@haero_kim/%EC%98%B5%EC%A0%80%EB%B2%84-%ED%8C%A8%ED%84%B4-%EA%B0%9C%EB%85%90-%EB%96%A0%EB%A8%B9%EC%97%AC%EB%93%9C%EB%A6%BD%EB%8B%88%EB%8B%A4)

[옵저버 패턴(observer pattern) 정리](https://2dongdong.tistory.com/47)