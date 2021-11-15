# URL Session Configuration



- URL Session 은 하나의 통신 선이다. => 이 **통신선을 설정 하는것이 URL Session Configuration 이다.**

EX) 셀룰러 사용 금지, Cache 의 저장 위치 설정



URLSession 객체가 생성되고 나면, 해당 Session에서 Configuration의 변경은 불가능하다. 



### 1. \- Shared Session Configuration

가장 기본 시스템 설정 Configuration 이다.  URLSession.shared 에서 바꿀 수가 없다. 

```swift
endReqeust(using: URLSession.shared)
```



### 2. - Default Session Configuration 

Delegate 를 통해 받을 수 있으며, response 를 Cache 에 저장하고, 쿠키도 저장한다.  인증 정보는 key chain 에 저장한다. 

```swift
let configuration = URLSessionConfiguration.default
let session = URLSession(configuration: configuration)
sendReqeust(using: session)
```



### 3. - Ephemeral 

임시로 저장하고, 디스크에는 저장하지 않는다. 

```swift
let configuration = URLSessionConfiguration.ephemeral
let session = URLSession(configuration: configuration)
sendReqeust(using: session)
```





### 4. - Background Session Configuration

백그라운드에서도 세션이 동작하도록 한다. 

```swift
let configuration = URLSessionConfiguration.background(withIdentifier: "DownTask")
let session = URLSession(configuration: configuration)
sendReqeust(using: session)
```

이때 **Background Session 은 delegate 로만 받을 수 있다 -> Completion Handler 는 Crash가 발생**한다.





### 5. - Custom Session Configuration

```swift
let configuration = URLSessionConfiguration.default
configuration.timeoutIntervalForRequest = 30
configuration.httpAdditionalHeaders =  ["Apple" : "isGood"]// 모든 요청에 공통적인 헤더 설정
configuration.networkServiceType = .responsiveData

let session = URLSession(configuration: configuration)
sendRequest(using: session)
```







-----

































