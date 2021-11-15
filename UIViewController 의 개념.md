# View 가 하는것들 



### 1. ViewController 는 View의 계층을 관리한다.

### 2. 화면 전환

### 3. Touch 를 하위 View에 전달한다. 



**ViewController**  는 UIViewController 를 상속하여 사용하게 된다.

그리고 UIViewController 내에서 ViewDidLoad, ViewWillAppear 와 같은 함수들이 호출이 되는 것이다. 



새로운 View를 부를 때 

![image-20211107210620553](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107210620553.png)

이와 같이 Modal 로 부를수가 있고 아래에서 위로 올라오는 Animation을 가지게 된다. 



ViewController는 화면에 제공할 Property들을 관리한다. 그리고 Property들은 View 가 사라질때 같이 사라지게 된다.

![image-20211107210732572](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107210732572.png)



이와 같이 Memory 가 없어질 경우 DidReceiveMemory 함수를 통해 특수 활동을 지정할 수가 있다.



--------



## View 계층관리 



RootView 는 UIViewController 의 view 를통해 접근이 가능하며 보통 다른 View들을 채우주는 Container 가 된다. 

![image-20211107211944233](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107211944233.png)



Parent View와 여러 SubView들의 계층이 생성되게 된다. 



-----------------



## Orientation and Rotation 



1. Portrait 

   가장 기본적인 형태 

2. Up-side Down 

   위, 아래가 바뀐상태 

3. Landscape -Left 

   음향 조절이 왼쪽에 오는 상태를 의미

![image-20211107212431774](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107212431774.png)

4. Landscape-Right

   음항 조절 스피커가 오른쪽으로 오는 상태를 의미한다.

![image-20211107212444164](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107212444164.png)



추가로 iOS 기기가 **화면이 위로 있으면 Face-up** 상태, **뒷면이 위로 가있다면 Face-down 상태**가 된다. 



UIViewController 의 **supportedInterfaceOrientations 속성을 통해서 Orientation의 지정**이 가능하다 

![image-20211107212939485](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107212939485.png)



또한 **shouldAutorotate true 일 경우 지원하는 Orientation 으로 자동으로 회전을 시킬지 여부**를 결정할 수 있다.

![image-20211107213028892](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107213028892.png)



마지막으로 **PreferedOrientationForPresentation 속성**을 통해 

![image-20211107213415977](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107213415977.png)

처음 지정한 Orientation을 지정해줄수 있다 (**나머지 Orientation으로 변경하는것도 역시 가능**하다)



마지막으로 Trainsition을 받는 Delegate 역시 사용이 가능하다 하지만 해당 Delegate 내에서 정확히 어떤 모드인지는 확인이 불가능한데 그 이유는 iOS 8 부터 Adaptive Layout 이 적용 되었기 때문이다. 



![image-20211107213920581](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107213920581.png)

하지만 **아이패드의 경우 Trainsition이 모두 Regular Regular 이므로 호출이 되지 않는다.**



때문에 RootView 의 크기와 차이를 통해 구하는것이 가장 정확하게 구할 수 있다.

![image-20211107214215713](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107214215713.png)



이와 같이 구현하면 **Height 와 width 의 비교를 통해 가로, 세로 모드인지 구분하여 구하는것이 가능**해진다. (아이패드도 가능해진다.)

마지막으로 coordinator 의 경우 Animation을 지정하는데 사용한다. 

![image-20211107214520897](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107214520897.png)



-----



## Container View Controller



<img src="/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107214651856.png" alt="image-20211107214651856" style="zoom:50%;" />



하나 이상의 Child View Controller들을 관리하면서 Layout 과 Transition을 담당한다. UI는 ChildViewController 가 담당한다 

가장 큰 예시는 NavigationController 이다.



예시로 ContainerView 를 스토리보드에서 가져와 쓰면

![image-20211107215041727](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107215041727.png)



이런식으로 ChildView 가 이어진 화면을 볼 수 있으며 **ChildView는 Container의 크기를 그대로 따르게** 된다. 

  

만약 **CustomView를 childViewController로 설정하고 싶다면**

![image-20211107215811541](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107215811541.png)

이와 같이 **Embed 로 설정**해주어야 한다. 그 외에는 **Crash가 발생할 수 있다.**



코드를 통해 ChildViewController를 설정해보자.

![image-20211107220350874](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107220350874.png)

여기서 **addChildViewController 는 ChildViewController 를 뷰 계층에 넣는것을 의미**하고, 

**bottomContainerView.addSubview(childVc.view) 를 통해서 화면에 띄워주어야** 한다. 



반대로 Remove 를 해줄 경우는 view를 removeFromParent로 제거하고, containerView를 제거하여야 한다.

![image-20211107220856287](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107220856287.png)

이것을 살펴보면 **view.removeFromSuperview() 를 통해서 View를 먼저 제거**하였고, **removeFromParentViewController 를 통해서 ContainerView 에서 삭제**해주었다. 

Child 를 제거하는것은 보통 child 에서 해주나, Container 에서도 가능하다.

![image-20211107221043319](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107221043319.png)



이와 같이 **ChildViewController 를 찾아서 제거**해주는것이다

![image-20211107221140210](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107221140210.png)

이 함수의 경우 **childViewController가 추가되거나, 삭제될때 불리게 된다. 그리고 parent 의 경우 추가될때는 ContainerView가, 삭제될때는 nil 이 전달**된다. 



마찬가지로 **DidMove 역시 존재**한다. 

![image-20211107221332973](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107221332973.png)



그런데 **코드를 통해 Add 한 경우에는 Didmove 는 호출되지 않는다. -> 내가 명시적으로 호출**을 해주어야 한다는 것이다.

![image-20211107221717779](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107221717779.png)

즉 이와같이 **didMove 를 직접 호출**해주어야 한다. 









