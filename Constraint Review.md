# 코드를 통한 제약의 추가 



### 제약의 class NSLayoutConstriant 

- 코드로 작성시 **동적으로 update 되는 코드를 자유롭게 구현**이 가능하다. 

![image-20211030110409838](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030110409838.png)

1.  **Visual Format Language 를 통해 생성하는 NSLayout class** 이다. 

생성자를 생성하는 코드가 상당히 길어진다, 디버깅이 상당히 길어진다. 



2. **다른 Layout 과의 비교를 통해** 생성하는것 

![image-20211030111113764](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030111113764.png)



3. NSLayoutClass 의 **속성**들

![image-20211030111201710](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030111201710.png)

하나의 NSLayoutConstraint 속성을 활성화 하려면 .isActive = true 로 지정을 해주면 된다. 

여러개의 제약을 추가하고 싶다면 class func 인 activate, deactivate 를 사용하여 배열 형식으로 활성화를 시켜준다. 



간단한 Example 

![image-20211030111712890](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030111712890.png)



### iOS 9 이후 NSLayoutAnchor를 통해 제약 설정이 가능하다.

생성자가 **훨씬 짧아지고, 가독성이 커지는 장점**이 있다. 

![image-20211030111732642](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030111732642.png)

View 와 Margin 이 제공하는 Anchor 의 사용이 가능하다. 



![image-20211030111934661](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030111934661.png)



- NSLayoutXAxisAnchor : 수평방향
- NSLayoutYAxisAnchor : 수직방향
- NSLayoutDimension :  size 와 관련된 사항 



Debuging 관련 사항 

![image-20211030112237958](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030112237958.png)



- constraintsAffectingLayout : Anchor 에 영향을 주는 NSLayoutConstriants 들을 배열로 받는다. 
- hasAmbiguosLayout : 잘못된 제약이 존재하는지 
- Name, item -> Anchor 를 식별하도록 할 수 있다. 



정리! 



코드를 통한 제약의 생성 방법은 총 3가지 이다. 



![image-20211030112424743](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030112424743.png)



장단점으로 정리 

1. NSLayoutConstraints : 모든 제약의 설정이 가능하지만, 코드가 길어지고 가독성이 떨어지는 단점이 존재한다. 디버깅이 어렵다
2. Visual Format Language : 간단하지만 별도의 문법을 공부, 모든 제약의 추가가 가능한것은 아니다. 디버깅이 어렵다
3. NSLayoutAnchor : 간단하고, 디버깅으로 쉽게 찾는것이 가능하다. 또한 제약을 모두 추가 해줄수도 있다. (코드도 짧다)



----



## 간단한 Visual Format Language 



![image-20211030113231134](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030113231134.png)



1. H : -> 수평방향 (horizontal)

2. V: -> 수직방향(vertical)

3. "|" -> superView 를 의미한다. 

4. "[viewName]" -> 뷰의 이름은 [] 안에 넣어준다. 

Ex) 뷰가 여백 없이 상위뷰에 채워지는것을 의미하는 Visual Format Language 

![image-20211030113511660](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030113511660.png)



뷰의 이름 뒤에는 () 괄호 후에 predicate 들어온다. 

![image-20211030113613760](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030113613760.png)



위의 예시는 뷰의 너비를 100 포인트로 고정하는것이다. 만약 위의 예시에서 **높이로 바꾸고 싶다면? V:[viewName(==100)]** 으로 설정하면 된다. 

만약 **100이라는 값을 상수로 고정하여 사용하고 싶다면?**

![image-20211030113753914](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030113753914.png)



이와 같이 사용을 하면 된다. (Dictionary 안에 정의 후 사용하면 된다.)



EX) 두 뷰 사이의 여백을 표기하고 싶다면?

**"[v1]-20]-[v2]"** 이와 같이 여백을 20으로 하여 표기가 가능하다.



마지막으로 아래의 예시를 해석해보자!

![스크린샷 2021-10-30 오전 11.39.53](/Users/choeseungmin/Documents/스크린샷 2021-10-30 오전 11.39.53.png)

superView 에서 기본크기의 여백으로 titleLabel 이 존재하고 너비는 100포인트 이상, 750 만큼의 priority를 가집니다. 이후 10포인트의 간격과 inputField 가 나타나게 되며 마지막으로 10포인트 여백 후 actionButton이 titleLabel 과의 동일 너비만큼 나오게 됩니다. 



