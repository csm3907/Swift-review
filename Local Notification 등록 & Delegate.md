# Local Notification 



![image-20211115222344686](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115222344686.png)



대표적으로 **User Notification Center 를 통해 알림을 등록하는것이 Local Push** 라 볼 수 있다. (내 로컬 서버 없이 구현이 가능한 Push)



![image-20211115222502387](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115222502387.png)

간단하게 순서를 살펴보면 **App 이 User Notification Center 에 Noti 를 등록하고, 이를 앱 실행 상태에 따라 Running 이면 바로 App 으로 그외에는 Badge 형태로** 나오게 된다. 



----





## Local Notification 순서

1. Permission ( Local Notification 을 사용할지 여부에 대한 권한 확인이 필요하다. )
2. Notification 객체를 생성하고 Center 에 요청
3. Notification 을 받을때의 코드를 구현한다. (Delegate가 실행된다.)



UNUserNotificationCenter 를 통해 구현을 하게 된다. -> 이 instance 는 **current 를 통해 Singleton 으로 구현된 객체를 사용**한다. 



-----



### 1. 권한 요청



```swift
import UserNotifications

UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in
            if granted {
                UNUserNotificationCenter.current().delegate = self
                print("granted")
            }
        }
```



**UNUserNotificationCenter 를 통해 바로 권한을 요청**하고 있다. 



```swift
let content = UNMutableNotificationContent()
content.title = "Hello"
content.body = inputField.text ?? "Empty Body"
        
content.badge = 123 // badge 갯수를 123개로 설정
content.sound = .default()
```

**Notification Center 에 등록할 Event 를 생성한다. title 은 제목, body는 내용 badge 는 앱 위에 뜰 숫자, sound 는 푸시 알림 소리를 나타내게 된다.** 



----



### Trigger 를 통해 등록

1. UNCalendarNotificationTrigger : 특정 날짜에 등록해야할때 (매일 아침 지정된 시점)
2. UNTimerIntervalNotificationTrigger : 특정시간 후 등록해야 할때
3. UNLocationNotificationTrigger : 특정 위치에 진입했을때 등록되어 방출된다. 



```swift
let trigger = UNTimeIntervalNotificationTrigger(timeInterval: interval, repeats: false)
        
let request = UNNotificationRequest(identifier: "test", content: content, trigger: trigger)

UNUserNotificationCenter.current().add(request) { (error) in
    if error = error {
        print(error?.localizedDescription)
    }
            
}
```

위의 **Trigger 와 content 를 통해 Request 객체를 설정하고, UNUserNotificationCenter 에 add 를 통해 등록**한다. 



----



### Foreground 일때 Notification 처리



먼저 Appdelegate 에서 

```swift
extension AppDelegate: UNUserNotificationCenterDelegate {
    func userNotificationCenter(_ center: UNUserNotificationCenter, willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        let content = notification.request.content
        let trigger = notification.request.trigger
        
        completionHandler([UNNotificationPresentationOptions.alert])
    }
}

```



이와 같이 **userNotificationCenter 의 willPresent 가 forground 일때 수행이** 되고(**Forground 일때 어떤 NotificationOption 으로 보여줄지를 결정한다**.) , completionHandler 의 Option 들이 나타나게 된다. **(Alert 만 있으면 소리, badge 없이 Push 위에서 만 나온다)** 



```swift
func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -> Void) {
        let content = response.notification.request.content
        let trigger = response.notification.request.trigger
        
        completionHandler()
    }
```

그리고 해당 Push 를 선택하면 위의 코드가 실행이 된다. 



마지막으로 Badge 의 숫자를 초기화 하려면

```swift
override func viewDidLoad() {
        super.viewDidLoad()
        
        UIApplication.shared.applicationIconBadgeNumber = 0
    }
```

이와 같이 Badge 의 숫자를 조절 할 수 있다. 

