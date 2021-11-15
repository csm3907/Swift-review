# Post Request



iOS 의 통신 중 **http 관련된 설정은 urlRequest 객체가 담당**을 하고 있다. => 여기에 설정



```swift
var request = URLRequest(url: url)
request.addValue("application/json", forHTTPHeaderField: "Content-Type")
request.httpMethod = "POST"
request.httpBody = encodedData()
        
let task = URLSession.shared.dataTask(with: request) { (data, response, error) in
      if let error = error {
         return
      }
            
      guard let httpResponse = response as? HTTPURLResponse else { return }
       	if (200...299).contains(httpResponse.statusCode) {
          	guard let data = data, let str = String(data: data, encoding: .utf8) else {
             	return
        }   
       } else { return }
}
```



이와 같이 기본 코드를 작성한다.



