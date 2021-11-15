# Adaptive Layout 이란?



모든 Device 에서 동작 할 수 있는 하나의 UI 를 개발하는 방법, 기술을 의미한다. 

![image-20211030133333867](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030133333867.png)



iOS8 이후 단일 Layout 을 제공한다. 

![image-20211030133419026](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030133419026.png)



이제는 위와 같이 Trait Collection 이 제공하는 것으로 뷰를 구분한다. 



1. AutoLayout -> 제약을 의미
2. size class -> 사이즈 계층을 의미하는 것이다. Regular class, compact class 두가지가 존재하고 큰것은 Recular, 작은것은 compact 가 된다. 



![image-20211030133647273](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030133647273.png)



이와 같이 구성이 된다. 

예시를 들면 아이폰 8Plus 모델의 경우 

![image-20211030133739835](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030133739835.png)



이와 같이 높이가 Regular, 너비는 Compact 가 되고, Landscape 모델에서는 반대가 된다. 

이와 같은 내용은 Apple 공식 문서를 보며 확인하도록 하자.



![image-20211030134009113](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030134009113.png)



Trait Collection은 size 외에 Device 의 다양한 정보가 포함되어있다. 



----



## Trait 구현 방법을 알아보자. 



내가 구현하고자 하는 목표는

![image-20211030134724475](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030134724475.png)

이와 같이 구현을 하고자 하는 것이다. (각 높이마다 Layout이 달라지게 되는 것이다. )



![image-20211030134849625](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030134849625.png)



가장 먼저 Adaptive Layout 을 하기 위해서는 Use Auto Layout, Use Trait Variations 를 꼭 사용하도록 체크해야한다.

![image-20211030135548942](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030135548942.png)

이와 같이 화면을 전환하면서 AutoLayout 의 개발이 가능하고 각 case 별로 Constraint가 어떤 경우에 적용이 되는것인지 Inspector 에서 확인이 가능하다 

![image-20211030135641401](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030135641401.png)



이와 같이 hC 일경우에만 적용되는 Constraint 임을 알 수가 있는 것이다.

아이패드의 경우 Vary for Traits 항목을 보게 되면, 

![image-20211030135859779](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030135859779.png)



이와 같이 Width, Height 의 선택이 가능해진다.  이렇게 하면 각 경웨 따라 Width, Height 지정이 가능해지는 것이다. 

**마지막으로 done Variation 으로 저장을 꼭 시켜주어야 한다.**



---



## Code 를 통해 구현을 해보자.



가장 먼저 delegate 를 살펴보면 

![image-20211030140245494](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030140245494.png)



이와 같이 **Transition이 이루어질 때마다 viewWillTransition 함수가 호출**이 되고, Frame, Size 클래스의 변경을 감지 할 수 있다. 

사용 예시 



![image-20211030140753560](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030140753560.png)

이와 같이 각 Size 별로 분기 처리하는것이 확인이 가능하다.

아이폰은 회전 할 때마다 Size Class 가 변경이 된다. -> 항상 호출이 된다. 

**아이패드의 경우는 Device 가 Size Class 가 변경 되지 않으므로 다른 delegate 가 필요하다.** 



----



Assets 에서도 이미지의 경우 



![image-20211030141103213](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030141103213.png)

이와 같이 Variation의 추가가 가능해진다.





