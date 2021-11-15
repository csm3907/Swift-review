# Then 이란 무엇인가!?



### 1. 먼저 Closure를 통한 Initialization 에 대해서 알아보자. 

https://docs.swift.org/swift-book/LanguageGuide/Initialization.html

위의 Apple 공식 문서를 통해 접근하였을시 Setting a Default Property Value with a Closure or Function 섹션을 통해 **closure 를 통한 초기화 문법을 확인이 가능**합니다. 

```swift
class SomeClass {
    let someProperty: SomeType = {
        // create a default value for someProperty inside this closure
        // someValue must be of the same type as SomeType
        return someValue
    }()
}
```

실제로 많이 사용하는 문법을 살펴보면 

```swift
let label: UILabel = {
  let label = UILabel()
  label.textAlignment = .center
  label.textColor = .black
  label.text = "Hello, World!"
  return label
}()
```



이와 같이 label 을 attribute 지정하며 생성이 가능합니다. 



---



### 2. Then 의 사용법



가장 먼저 문법을 살펴보면 앞서 설명한 Closure 생성자와 비슷한 문법임을 확인이 가능합니다. 

```swift
let label = UILabel().then {
  $0.textAlignment = .center
  $0.textColor = .black
  $0.text = "Hello, World!"
}
```



문법적으로 확인을 해보면 



**let 변수명 = Class instance . Then {  } 의 문법**으로 사용이 되고 있으며, 해당 instance 가 parameter 로 제공이 되어, attribute 를 초기화 하여 설정이 가능합니다. 



UIView 와 관련된 내용이 아닐시에도 

```swift
let queue = OperationQueue().then {
  $0.maxConcurrentOperationCount = 1
}
```

위와 같이 OperationQueue의 초기값으로 Readable, writeable 한 Count 를 1로 설정하여 Queue를 생성도 가능하며 혹은

```swift
extension MyType: Then {}

let instance = MyType().then {
  $0.really = "awesome!"
}
```

이와 같이 Custom Class 내에서도 초기화를 진행하며 사용해주는것이 가능합니다. **(NSObject의 모든 subclasses 들이 사용 가능)**



---



### 3. Then 의 사용 이유

그렇다면 Then 을 사용하는 이유는 무엇인가? -> 한 객체의 **속성을 변경하는 소스들을 모아주기 때문에 코드의 관리가 쉬워**진다는 장점이 존재한다! 또한 1번의 Closure를 통한 객체를 생성하는 소스 보다도 더 적은 소스로 Initialize가 가능합니다. 



----



### 4.  With() 명령어

```swift
let newFrame = oldFrame.with {
  $0.size.width = 200
  $0.size.height = 100
}
newFrame.width // 200
newFrame.height // 100
```



with는 값을 복사하고 싶을 때 사용합니다.  위의 소스의 예를 보면, Frame 은 **CGRect**(x: 50, y: 50, width: 50, height: 50) 이와 같이   x, y, width, height 값으로 이루어져 있으며 x와 y 값을 그대로 사용하며, size 값은 변경하여 복사 하고 싶을때 사용



-----



### 5. do() 명령어

```swift
UserDefaults.standard.do {
  $0.set("devxoul", forKey: "username")
  $0.set("devxoul@gmail.com", forKey: "email")
  $0.synchronize()
}
```



이 부분의 경우 제가 이해하기로는 Singleton 객체의 경우 같은 Singleton.shared.XXX 와같은 문법을 많이 사용하게 되는데, 해당 객체의 여러 함수들을 한번에 코드를 모아 사용할 수 있도록 도와주는 함수로 파악 하였습니다.





https://github.com/devxoul/Then

이곳이 Then을 만든 전수열님? 의 github 위치 입니다. 



