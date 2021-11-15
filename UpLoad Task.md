# Upload Task 



1. Delegate 형식

```swift
extension UploadTaskViewController: URLSessionTaskDelegate {
    func urlSession(_ session: URLSession, task: URLSessionTask, didSendBodyData bytesSent: Int64, totalBytesSent: Int64, totalBytesExpectedToSend: Int64) {
        // total 과 bytesSent 로 퍼센트를 계산한다.
    }
    
    func urlSession(_ session: URLSession, task: URLSessionTask, didCompleteWithError error: Error?) {
        // 에러 발생시
    }
}
```



위의 두가지 함수를 통해 현재 진행중인 상황과, 마지막 끝마친 상황의 Delegate 구현이 가능하다. 

그리고 Delegate 를 설정하는 부분의 경우 URLSession 의 dataTask 와 동일한 함수를 사용한다. 



```swift
lazy var session: URLSession = { [weak self] in
        let config = URLSessionConfiguration.default
        let session = URLSession(configuration: config, delegate: self, delegateQueue: OperationQueue.main)
        return session
    }()

session.upLoadTask(with: request, from: data) // 데이터와 URLRequest 객체만 보낸다.
```





## 2. Completion Handler



dataTask 와 동일하게 생성한다.

```swift
let task = URLSession.shared.uploadTask(with: dropboxUploadRequest, from: data) { (data, response, error) in
            if let error = error { return }
            print(String(data: data!, encoding: .utf8)) // 완료되면 출력
        }
```

