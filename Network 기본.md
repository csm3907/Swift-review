# Network 개발



 ## 1. Network 유의점

![image-20211110203955446](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110203955446.png)

위의 내용을 준수하여 개발을 해야 한다. 

Socket 통신 대신 HTTP를 사용하여 통신하자 



데이터의 통신은 URLSession을 사용한다 

![image-20211110204055701](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110204055701.png)



**웹서버가 제공하는 HTML 데이터를 화면에 출력할때는 Webkit** 을 사용한다. 

게임을 GameKit, 음악 스트리밍 Bonjour, **소켓통신은 CFNetwork**, **iOS12 에서는 Network 프레임워크로 TCP, UDP 통신**을 구현



Network 사용시 주의점 -> **비동기 && Background Thread 로 사용**하여야 한다.

그것을 지원하는것이 **URLSession.shared.dataTask 통신**

![image-20211110204535916](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110204535916.png)





--------------



## 2. Web contents 사용법



**Url 을 통해 URLRequest 객체를 생성한다. -> HTTP 통신의 주체가 되는 request 객체**

![image-20211110211758044](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110211758044.png)



뒤로가기 구현

![image-20211110211915726](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110211915726.png)



앞으로 가기 

![image-20211110211927775](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110211927775.png)



Reload -> **webView.reload() 함수 호출**



webkit View 의 경우 **default 는 http를 지원하지 않는다. -> HTTPS 를 사용**하여야 한다.

----------



## 3. Apple Transport Security 



**보안성을 위해 http 를 지원하지 않고, https 만을 사용하도록 권장**하고 있다. (웹뷰에서 이동이 되지 않은 이유)

-> ATS 의 수준을 낮추면 사용이 가능하다. 

![image-20211110212511834](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110212511834.png)

위와 같이 설정하면 **모든** **http 통신을 가능**하게 하겠다는것을 의미한다.



일부만 예외로 처리하고 싶다면 

![image-20211110212727614](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110212727614.png)

이와 같이 **domain 을 Dictionary 로 선언**하고 **NSExceptionAllowsInsecureHTTPLoads 를 True 로** 지정한다.



만약 m.shinhan.com dmf 추가하고 싶다면?

![image-20211110212833377](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110212833377.png)

이와 같이 NSIncludesSubdomains 를 설정해주면 된다.



![image-20211110212941122](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110212941122.png)



1. NSAllowsArbitraryLoads : ATS 를 풀지, 안풀지
2. NSAllowsArbitraryLoadsForMedia : 미디어 요청을 안전하지 않아도 접근 가능하도록 (비디오, 오디오) AVFoundation,사용하여야 한다.
3. NSAllowsArbitraryLoadsInWebContent : Web 에서 허용할시
4. NSAllowsLocalNetworking : 로컬에서만 허용할시 
5. Dictionary -> 조금씩 설정을 하고 싶을때 사용한다. 



----

