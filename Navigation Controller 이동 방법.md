# Navigation Controller 



![image-20211107222945762](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107222945762.png)



Navigation Controller 는 Stack 으로 관리된다.



그리고 위는 NavigationBar, ToolBar 가 존재하고 가운데는 TopViewController 의 rootView 가 나오게 된다. 

<img src="/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107223141159.png" alt="image-20211107223141159" style="zoom:50%;" />



Navigation Bar 에 Item 을 추가할 경우 

![image-20211107223217883](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107223217883.png)



이와 같이 **UIBarButtonItem 으로 무조건 추가**가 되어야 한다. 

![image-20211107223304264](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107223304264.png)

Navigation은 위의 3가지 속성들이 대표적이다. 



--------



## UIStoryboard 를 통한 RootViewController 지정



![image-20211107224045150](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107224045150.png)

위와 같이 control 키와 함께 사용할시 Relationship segue 가 존재한다. 

또한 다음 화면을 지정할 시에는 



![image-20211107224515717](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107224515717.png)



이와 같이 Show 를 선택하여 지정한다.



----



### Code 를 통한 NavigationController 부르기



![image-20211107225006086](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107225006086.png)

UINavigationController 를 생성하고, rootViewController 를 지정한다. 그리고 해당 NavigationController 를 present 하여 화면에 띄워주게 된다.

나머지 View 에서는 push, pop 을 통해 화면 이동을 진행한다. self.navigationController 는 UIViewController 내부에 정의되어 있다.

![image-20211107225256173](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107225256173.png)



만약 Pop to Root VC 의 경우 unwind Segue 를 지정해야 한다.

첫번째 화면에서 

![image-20211107225506394](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107225506394.png)

이와 같이 unwindToFirst 라는 segue 를 설정을 한 후

![image-20211107225542814](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107225542814.png)



Exit 를 연결하고 처음 unwindToFirst segue 를 지정을 해준다. 그렇게 되면 첫번째 Scene과 unwind scene 이 연결 되는 것이다.

-> 또한 처음으로 알아서 돌아가게 된다. 



또한 코드로 특정 화면으로 이동을 하려면

![image-20211107225807086](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211107225807086.png)

이와 같이 ViewController 를 찾아서 이동을 시켜주면 된다. 























