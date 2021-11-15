# Data Task 



```swift
// Code Input Point #1
        guard let urlRequest = URL(string: booksUrlStr) else { return }
        let task = URLSession.shared.dataTask(with: urlRequest) {
            (data, response, error) in
            guard let error = error else { return }
            
            guard let httpStatus = response as? HTTPURLResponse else { return }
            guard (200...299).contains(httpStatus.statusCode ?? 0) else { return }
            guard let data = data else { return }
            
            do {
                let decoder = JSONDecoder()
                let bookList = try decoder.decode(BookList.self, from: data)
                decoder.dateDecodingStrategy = .custom({ (decoder) -> Date in
                    let container = try decoder.singleValueContainer()
                    let dateStr = try container.decode(String.self)
                    let formatter = ISO8601DateFormatter()
                    formatter.formatOptions = [.withFullDate, .withTime, .withDashSeparatorInDate, .withColonSeparatorInTime, .withColonSeparatorInTime]
                    return formatter.date(from: dateStr)!
                })
                
                if bookList.code == 200 {
                    self.list = bookList.list
                    DispatchQueue.main.async {
                        self.tableView.reloadData()
                    }
                } else { return }
            } catch {
                print(error)
            }
        } // with: URLRequest는 Network 별로 Cache 정책등이 바꿔야 할때 사용
        task.resume()
```



위의 코드를 순서대로 살펴보면 

1. URLRequest 를 생성하여 URL 요청사항을 담은 객체를 생성한다. 
2. 데이터를 처리할 하나의 task 를 생성한다. 이때 API 나 간단한 통신이므로 dataTask 를 이용한다. 
3. completionHandler 를 이용한 방식으로 data, response, error 를 받는다. 
4. Decoder 를 사용한 Mapping 을 수행한다. 



이와 같은 순서로 이루어지며 Mapping된 결과인 객체를 이용하여 View 처리를 이어간다. 





---------



## URL Session Delegate 



먼저 계층을 살펴보자

![image-20211112225009328](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211112225009328.png)

이와 같이 URLSessionDelegate 를 상속받는 TaskDelegate, 그리고 이를 상속받는 Data, DownloadDelegate 가 존재한다. 



이제 구현부를 살펴보자 먼저 URLSession 객체 생성시 



```swift
let configuration = URLSessionConfiguration.default
session = URLSession(configuration: configuration, delegate: self, delegateQueue: OperationQueue.main) // 모든 Delegate Method 가 Main Thread 에서 실행된다.
```



그리고 delegate 를 살펴보면

```swift
extension SessionDelegateTableViewController: URLSessionDataDelegate {
    
    
    // 처음에 데이터를 받았을경우
    func urlSession(_ session: URLSession, dataTask: URLSessionDataTask, didReceive response: URLResponse, completionHandler: @escaping (URLSession.ResponseDisposition) -> Void) {
        guard let response = response as? HTTPURLResponse, (200...299).contains(response.statusCode) else {
            completionHandler(.cancel)
            return
        }
        
        completionHandler(.allow) // data 전송 시작
    }
    
    // data 가 받아질 때마다 호출된다. (API 호출 후 받을때마다 호출된다.)
    func urlSession(_ session: URLSession, dataTask: URLSessionDataTask, didReceive data: Data) {
        buffer?.append(data)
    }
    
    // data 전송이 완료된 경우 (error의 nil check로 오류 판단)
    func urlSession(_ session: URLSession, task: URLSessionTask, didCompleteWithError error: Error?) {
        if let error = error {
            showErrorAlert(with: error.localizedDescription)
        } else {
            parse() // decoding 실시
        }
        
    }
}
```



그리고 마지막 Decoding 함수를 살펴보면

```swift
func parse() {
        guard let data = buffer else { return }
        
        let decoder = JSONDecoder()
        
        decoder.dateDecodingStrategy = .custom({ (decoder) -> Date in
            let container = try decoder.singleValueContainer()
            let dateStr = try container.decode(String.self)
            
            let formatter = ISO8601DateFormatter()
            formatter.formatOptions = [.withFullDate, .withTime, .withDashSeparatorInDate, .withColonSeparatorInTime]
            return formatter.date(from: dateStr)!
        })
        
        do {
            let detail = try decoder.decode(BookDetail.self, from: data)
            
            if detail.code == 200 {
                titleLabel.text = detail.book.title
            }
        } catch {
            print(error.localizedDescription)
        }
        
    }
```



JSONDecoder 를 설정하여 객체를 생성하는 작업, 객체를 활용한 View 작업 등이 이루어 지는것을 확인 할 수 있다.



--------



**여기서 잠깐!**



URLSession 이란 하나의 통신 선을 의미한다. -> HTTP 연결을 시켜놓고 

URLRequest-> url 과 post, get 방식인지, cache 설정 등을 세팅하는 객체

Task -> 연결된 통신선을 통한 하나의 작업 개념이다. -> suspended 상태이기 때문에 .resume() 함수를 꼭 사용해주어야 하는 것이다. 





