# 상위 뷰에 여백 없이 subView의 제약을 추가해보자. 



![image-20211030114816963](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030114816963.png)



이와 같이 superView 안에 blueView 가 꽉 차도록 제약을 추가 하는 것이다. 

이때 코드로 제약을 추가하기 전 **Storyboard 혹은 Xib 내에서는 기본적으로 NSAutoresizingMaskLayoutConstraint 를 지정해 놓는데 이 지정된 값과 코드로 추가한 값은 항상 충돌**하게 된다. 

-> **view.translateAutoresizingMaskIntoConstraints = false** 를 해주는 이유이다. 



## 1. NSConstraintLayout 이용한 방법

![image-20211030115208261](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030115208261.png)



## 2. Visual Format Language 를 이용한 방법

![image-20211030120149623](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030120149623.png)

 여기서 주의해야 할 점은 views 문자열에 들어갈 **Dictionary key 값이 VFL 안에 들어간 값과 동일하게** 작성 되어야 한다는 점이다. 



## 3. NSAnchorLayout 사용

![image-20211030120407181](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030120407181.png)



이와 같이 isActive 를 사용하여 true 로 모두 변경이 가능하다.



### NSConstraint 사용 중 너비, 높이를 변경할때 중의점

다른 뷰와의 연관된 속성이 아니므로 

![스크린샷 2021-10-30 오후 12.07.56](/Users/choeseungmin/Documents/스크린샷 2021-10-30 오후 12.07.56.png)

toItem 은 nil, 그리고 attribute 는 notAnAttribute 로 지정해야한다. 



