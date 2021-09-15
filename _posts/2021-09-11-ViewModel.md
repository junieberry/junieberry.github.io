---
title: ViewModel
author: Junyoung Kim
date: 2021-09-11
category: Android
layout: post
---


LiveData와 찰떡궁합, MVVM의 기본 ViewModel에 대해 알아보자ㄱㅣ! 

## ViewModel

> The [`ViewModel`](https://developer.android.com/reference/androidx/lifecycle/ViewModel) class is designed to store and manage UI-related data in a lifecycle conscious way.

## ViewModel 등장 배경
ViewModel도 LiveData와 마찬가지로 Android Jetpack 구성 요소 중 하나이다. ViewModel은 UI 관련 데이터를 저장하고 관리하도록 설계되었다. 기존 안드로이드 설계 구조에서는 액티비티 내에서 이러한 동작을 함께 했다. 그럼 문제가 생기는데...😟

1️⃣ **액티비티가 다시 생성될 시 내부의 데이터가 손실된다.**
액티비티의 재생성은 생각보다 흔하다. 안드로이드의 os 설정 (`화면 전환`, `언어 변경`, `멀티 윈도우 환경으로 변경`, `키보드 변경` 등)이 바뀌면 activity를 다시 만들게 되는데, 만약 액티비티 내에 서버에서 사용자 목록을 가져와 나타내는 UI가 있다면, 액티비티가 다시 생성되는 과정에서 사용자의 목록들 다시 가져와야 한다. 간단하고 작아 쉽게 직렬화할 수 있는 데이터는 `onSaveInstanceState()`를 사용해서 저장할 수 있지만, 이렇게 사용자의 목록이나 bitmap 같은 큰 데이터를 저장하기에는 적합하지 않다.

2️⃣ **UI 컨트롤러가 무거운 비동기 호출을 수행해야 한다.**
UI 컨트롤러의 목적은 UI 데이터를 표시하거나, 사용자 작업에 반응하는 것이다. 하지만 UI 컨트롤러 내에서 비동기 호출을 수행하게 된다면  UI 컨트롤러가 비동기 호출을 관리해야하고, 액티비티가 생성될 때마다 비동기 호출을 호출해야 한다. 또한 클래스가 과도한 책임을 지게 된다.

**따라서 UI 컨트롤러에서 View 데이터 소유권을 분리할 수 있는 ViewModel이 탄생하게 되었다. 👼**


### ViewModel? AAC ViewModel? MVVM?
이전 [MVC, MVP, MVVM 포스팅](https://junieberry.github.io/android/2021-09-02-MVC,MVP,MVVM.html)의 MVVM에서도 똑같은 이름을 가진 ViewModel이 등장한다. 이름이 똑같아서 둘을 구분하기 위해 AAC의 뷰모델은 대부분 AAC ViewModel이라고 한다.

🤯 이름이 똑같아서 AAC의 ViewModel이 MVVM의 ViewModel이라고 생각하기 쉽다. 심지어 대부분의 MVVM 예시에서 AAV의 ViewModel을 써서 더 헷갈린다. 하지만 둘은 전혀 다르다. 

먼저 정의를 생각해보자.

**📌AAC ViewModel**

> ViewModel은 환경 변화에서 뷰에 사용되는 데이터를 유지시키기 위한, 라이프 사이클을 알고 있는 클래스이다.

**📌MVVM의 ViewModel**
> 뷰와 모델 사이에서 데이터를 관리하고 바인딩 해주는 역할을 한다.

정의로 보니까 둘의 차이가 확연하게 느껴진다. AAC의 ViewModel을 사용하지 않고 MVVM을 구현할 수 있다. AAC의 ViewModel을 사용한다면 AAC ViewModel의 장점을 갖고 MVVM을 구현할 수 있다.  오히려 좋아.. 하지만 두 MVVM에는 차이가 존재한다. AAC ViewModel의 경우 ViewModelPrividers를 사용하기 때문에 싱글톤으로 구현된다. 또한 기존 MVVM에서는 View와 Model의 관계가 1:n인 반면, 구글은 하나의 View에 하나의 Model을 사용하는 것을 권장한다고 한다.


## ViewModel 생명주기

ViewModel은 생명주기를 고려하는 클래스이다.
![ViewModel의 생명주기](https://developer.android.com/images/topic/libraries/architecture/viewmodel-lifecycle.png)

**ViewModel의 생명주기는 액티비티의 생명주기보다 더 길다**

그림에서 잘 보이듯이, 액티비티가 종료될 때까지, 혹은 프래그먼트가 분리될 때까지가 ViewModel의 Scope이다.
`Activity roated` 같은 configuration 변경 시 액티비티는 다시 시작되지만 ViewModel은 여전히 메모리 상에 남아있다. 따라서 아까 언급한 액티비티의 재생성으로 인한 데이터 손실 문제나 반복적인 비동기 호출의 문제를 해결할 수 있다.

그럼 이제 ViewModel을 한번 만나보러 가보자! 😙

## ViewModel 구현
ViewModel이 보유한 데이터는 액티비티나 프래그먼트에서 즉시 사용할 수 있다. 아까 언급한 사용자 목록을 불러오는 예제를 활용해보자.

```kotlin
class MyViewModel : ViewModel()
	{ private val users: MutableLiveData<List<User>> by lazy {
	  MutableLiveData<List<User>>().also {
	   loadUsers()
	   }
	 }
	 fun getUsers(): LiveData<List<User>> {
	 return users
	 }
	 private fun loadUsers() {
	   // 비동기 호출 }  
}
```
ViewModel에서 사용자 목록을 불러오는 비동기 호출 작업을 수행하고, 사용자 목록을 관리하는 것을 볼 수 있다.
이때 ViewModel은 View와 LifecycleOwners보다 생명주기가 길게 설계되어있다. 따라서 View와 LifecycleOwners에 대해 알지 못해도 ViewModel을 다루는 테스트를 작성할 수 있지만, ViewModel에서 LiveData와 같이 수명 주기를 인식하는 객체를 Observe해서는 안된다.
Application Context가 필요하면 `ViewModel` 대신 `AndroidViewModel` 클래스를 상속받도록 하자!

```kotlin
class MyActivity : AppCompatActivity() {
	override fun onCreate(savedInstanceState:  Bundle?) {
		val model: MyViewModel by viewModels()
		model.getUsers().observe(this, Observer<List<User>>{ users -> // update UI
		})
	}  
}

```

### 프래그먼트 간 데이터 공유

액티비티 내부의 두 프래그먼트는 ViewModel을 사용했을 때 서로 커뮤티케이션하지 않고도 쉽게 해결할 수 있다.
```kotlin
class SharedViewModel : ViewModel() {
  val selected = MutableLiveData<Item>()
  fun select(item:  Item)  {
  selected.value = item
  }  
}  
  
class ListFragment : Fragment() {
	private lateinit var itemSelector: Selector
	private  val model:  SharedViewModel by activityViewModels()
	override  fun onViewCreated(view:  View, savedInstanceState:  Bundle?)  {
		super.onViewCreated(view, savedInstanceState)
		itemSelector.setOnClickListener { item ->  // Update the UI  }
		}  
}  
  
class  DetailFragment : Fragment() {
	private val model:  SharedViewModel by activityViewModels()
	override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
		super.onViewCreated(view, savedInstanceState)
		model.selected.observe(viewLifecycleOwner, Observer<Item> { item ->  // Update the UI  })
		}  
}
```

Activity 내에서 ViewModel을 공유해 프래그먼트가 데이터를 공유하는 경우

- 액티비티는 프래그먼트 커뮤니케이션에 개입하지 않아도 된다.
- 프래그먼트는 ViewModel 외의 다른 부분에 의존하지 않아 다른 프래그먼트가 없어져도 올바르게 동작한다.
- 각 프래그먼트는 자체 생명주기를 갖고 있고 다른 프래그먼트의 생명주기에 영향 받지 않는다.

**이러한 방법을 통해 액티비티와 프래그먼트 간의 결합을 느슨하게 만들어준다고 할 수 있다.**

### Ref
- [안드로이드 공식 문서](https://developer.android.com/topic/libraries/architecture/viewmodel)
- [쾌락 코딩 블로그](https://wooooooak.github.io/android/2019/05/07/aac_viewmodel/)
- [Charlezz의  ViewModel이란 무엇인가? ViewModel 초보를 위한 가이드](https://charlezz.medium.com/viewmodel%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-viewmodel-%EC%B4%88%EB%B3%B4%EB%A5%BC-%EC%9C%84%ED%95%9C-%EA%B0%80%EC%9D%B4%EB%93%9C-e1be5dc1ac18)
- [Android Architecture Components ViewModel을 간단하게 초기화 하려면?](https://thdev.tech/androiddev/2018/08/05/Android-Architecture-Components-ViewModel-Inject/)