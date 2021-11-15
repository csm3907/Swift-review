# Notification Center 와 Notification



1. Notification을 사용한 Event, 객체 전달

- Notification 은 Foundation 프레임워크 안에 구현되어있다. 
- Notification 안에 Name과 userInfo 객체를 담아 전달 할 수 있다. 



![image-20211115221329078](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115221329078.png)



Post 한 Event가 Notification Center 에 등록된 모든 Observer 에게 Broad Cast 된다. 



-----



### 사용 방법



```swift
@IBAction func postValue(_ sender: Any) {
        if let text = inputField.text {
            DispatchQueue.global().async {
                NotificationCenter.default.post(name: Notification.Name("postCompose"), object: nil, userInfo: ["NewValue": text])
            }
        }
        dismiss(animated: true, completion: nil)
    }
```



이와 같이 **NotificationCenter 에 post 를 통해 어떤 이름의 Event 가 발생**하였으며, **userInfo 의 dictionary 를 전달하는 방식**으로 등록하게 된다. 



 ```swift
 var token: NSObjectProtocol?
 
 token = NotificationCenter.default.addObserver(forName: Notification.Name("postCompose"), object: nil, queue: OperationQueue.main) { [weak self] noti in
             print(Thread.isMainThread ? "Main" : "Background")
             guard let value = noti.userInfo?["NewValue"] as? String else{ return }
             
             DispatchQueue.main.async {
                 self?.valueLabel.text = value
             }
             
             print("#1", #function)
         }
 ```



이와 같이 **NotificationCenter.default 안에 addObserver 를 통해 #selector 혹은 Closure 형식으로 등록**하게 된다. 

```swift
NotificationCenter.default.addObserver(self, selector: #selector(process), name: Notification.Name("postCompose"), object: nil)

@objc func process(notification: Notification) {
        print(Thread.isMainThread ? "Main" : "Background")
        guard let value = notification.userInfo?["NewValue"] as? String else{ return }
        
        DispatchQueue.main.async {
            self.valueLabel.text = value
        }
        
        print("#1", #function)
    }

```

이와 같이 Notification 을 통하여 userInfo 값에 접근하여 값을 뽑아내고 있다. 



```swift
deinit {
        NotificationCenter.default.removeObserver(self) // instance 와 연결된 모든 Observer 를 해지한다.
        if let token = token {
            NotificationCenter.default.removeObserver(token)
        }
        
        print(#function)
    }
```

**마지막으로 addObserver 를 통해 등록한 Observer 들을 제거 해주어야 한다.  iOS9 이전의 기기에서는 removeObserver를 수행하지 않으면 Crash가 발생 할 수 있다.** 













