# URL Loading System 



**url Loading System 은 url을 통해서 서버와 통신하는것**을 의미한다. 



이와같은 system 을 지원하기 위해 **URLSession을 지원하게 된다. -> 요청과 응답을 처리**한다

그리고 URLSession은 3개의 Type이 존재한다. 

![image-20211110223935287](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110223935287.png)



## 1. Shared Session

단순한 Network 요청을 이용하는데 사용한다. 

완료 Handler 로 return을 받는다. 구현은 단순하지만 background 전송은 지원하지 않는다. 



## 2. Default Session

 Session을 직접 구성할때 사용, Delegate 로 세부적 동작 가능하다. 

![image-20211110224133243](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110224133243.png)

응답이 Disk와 메모리에 저장된다. 



## 3. Ephemeral Session

Private 한 통신을 할때 사용, Memory 에만 저장된다. (쿠키, 디스크에 저장 안된다.)



## 4. Background Session

앱 상태에 관계 없이 background 에서 다운로드 가능하도록 할때 사용한다. 



-----



# Task 



**session 생성 후 Task 를 생성한다. -> 각 업무라고 생각하자** 



1. **Data Task**

API 통신때 사용

![image-20211110224403937](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110224403937.png)



2. **Download, Upload Task**

![image-20211110224436101](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110224436101.png)



Background session을 지원한다. 



3. **Stream Task** 

TCP 프로그램을 만들때 (채팅앱과 같이)



**Task 는 suspended 상태로 생성된다. -> resume()을 써야 하는 이유이다.** 



Task 를 처리하는 방법 2가지 => completion Handler, session Delegate 

![image-20211110224631920](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110224631920.png)



1. **완료 Handler** 

-> **1번만 호출이 된다, 서버의 완료 데이터가 한번에 전송**이 된다. 



2. Delegate 

->. **세부적인 처리**에 써야 한다. 



위의 두가지는 **둘다 사용할 수는 없다**. 한가지만 사용할 수가 있다. 













