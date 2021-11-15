# Notification 



### Notification 이란? 

사용자에게 Event를 알려주는것을 의미한다.

![image-20211115215536153](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115215536153.png)

1. 이름이 없는 Notification 

-> 특정 Event 가 발생하면 Observer 들에 broadcasting 이 된다.

<img src="/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115215600362.png" alt="image-20211115215600362" style="zoom:50%;" />





2. Local Notification

-> 시계 알람과 같은것

<img src="/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115215713772.png" alt="image-20211115215713772" style="zoom:50%;" />



3. Remote Notification

-> 일반적으로 알고 있는 Push 서비스이다. 

-> Provider 라고 하는 Local 서버가 존재하고 해당 서버에서 APNs에 Noti를 등록하여 사용자에게 Push가 가게 된다. 

이떄 Provider 는 인증된 서버만 가능하게 되어 있다 -> Certificate 와 Push Provisioning 이 존재하는 이유이다. 

<img src="/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115215813500.png" alt="image-20211115215813500" style="zoom:50%;" />





2번과 3번은 User Notification Framework의 통합 API 를 통해 개발을 진행한다. -> 권한 요청부터 모든것을 처리한다. 



-----



### 권한



![image-20211115215917204](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115215917204.png)



Notification은 허가를 받아야 한다. (Local, Remove 인경우만)



------



### 등록 가능한 상태



![image-20211115220016928](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115220016928.png)



이름이 없는 Notification 만 Not Running 일때 불가능하다. 



----



### 각 상태에 따른 User Notification 

![image-20211115220106323](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115220106323.png)



Foreground 에서는 App이 동작하고 있으므로 당연히 Delegate( AppDelegaet ) 로 이벤트를 전달한다. 

하지만 Background 이거나 Not Running 상태라면 



![image-20211115220316689](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115220316689.png)



이와 같이 Push Notification 을 등록, 원하는 Action을 추가 할 수 있다. 



![image-20211115220347269](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115220347269.png)



확인 하지 않은 메시지는 Notification Center 안에 등록되고 사용자가 지우거나, 특정 Action을 취할 수가 있다.





