# Debuggin Auto Layout 방식 



1. 필요한 제약을 추가하지 않았거나
2. 제약이 서로 겹치는 경우 

위의 2가지가 오류를 발생하게 만드는 이유 이다.



## Run Time 에서 오류를 찾아보자!

Debug view Hierachy 를 들어간다. 

오류가 있으면 뷰 계층구조에서 오른쪽에 보라색으로 뷰가 나오게 된다. 

![스크린샷 2021-10-30 오후 12.26.34](/Users/choeseungmin/Documents/스크린샷 2021-10-30 오후 12.26.34.png)

이후 size inspector 를 보면 



![image-20211030122733524](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030122733524.png)

 x 를 제외한 모든 값이 0 임을 알수 있고 -> 해당 이슈로 오류가 발생하구나를 알 수가 있다.



-----



## console 을 이용하여 원인 파악

- 보통 Conflicting Layout 의 경우 콘솔에서 로그를 통해 파악 후 callBack 으로 하나를 제거 하여 보여준다. 

![image-20211030123431226](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030123431226.png)



![image-20211030123451444](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030123451444.png)



첫번째 console 에서는 문제가 되는 constraint 를 나타내준다. -> 메모리로 보여주기 때문에 식별은 어려울 수가 있다. 

두번쨰의 경우 callback system 에서 어떤 system 을 제거했는지를 알려준다. 

![image-20211030123632497](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030123632497.png)



현재 Constraint 를 보면 회색으로 callback system 이 강제로 없앤 뷰가 보이게 된다. But 보라색으로 뷰 하이에라키에서는 보이지 않는다. 



2번째 방법! View의 Pointer 로 검색을 하여 어떤 뷰에서 오류가 발생하였는지를 확인이 가능하다!

![image-20211030123955174](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030123955174.png)

먼저 view의 포인터값을 찾고, 

![image-20211030124107554](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030124107554.png)

하단 왼쪽의 Navigator 아래에 붙여넣어 검색을 하면 해당 View 의 계층 구조만 찾을수가 있고 가장 하단의 뷰를 선택하면 찾을 수가 있다. 

![image-20211030124154077](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030124154077.png)



symbolic breakpoint 의 이용방법 -> 자주 활용되지는 않는다. (있다는것 정도만.. ㅎㅎ)



---



**Identifier 를 이용하여 어떤 Constraint 의 오류인지 정확하게 파악을 해보자**

![image-20211030124639876](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030124639876.png)

위와 같이 View에서 Accessibility 안에 Identifier 의 지정이 가능하다. 그리고 그 이름을 적어주고

그리고 Constraint 에서도 



![image-20211030124738913](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030124738913.png)



이와 같이 Identifier 의 지정이 가능하다.

또한 Code 로 지정도 가능하다. 

![image-20211030124905864](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030124905864.png)

App을 다시 실행하고 결과를 확인해보면 

![image-20211030124956306](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211030124956306.png)

**뷰를 쉽게 파악하는것이 가능해진다!!!**

-> View를 포인터로 검색하지 않아도 알아보는것이 가능해지는 것이다. 



****