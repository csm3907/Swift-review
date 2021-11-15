# Custom Notification Sound and Attachments



**Sound 의 설정과 Attachments 를 추가**한다. 

```swift
content.sound = UNNotificationSound.init(named: "bell.aif")
```



이와 같이 프로젝트 내의 **Sound 파일을 이름 + 확장자로** 간단하게 지정이 가능하다. 

<img src="/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115231831891.png" alt="image-20211115231831891" style="zoom:25%;" />



그리고 **Sound 가 지원하는 파일은 위의 4가지, 시간은 30초**이다. 

네트워크를 통해 다운받을 경우 /Library/sound/ 하위에 다운 받아야 정상적으로 실행된다. 



-----



### Attachments -> 오디오, 비디오



1. **반드시 Local에 저장되어 있어야 한다**. 

```swift
guard let url = Bundle.main.url(forResource: "hello", withExtension: "png") else { return }
guard let imageAttatchment = try? UNNotificationAttachment(identifier: "hello-image", url: url, options: nil) else { return }
        
content.attachments = [imageAttatchment]
```



![image-20211115232451856](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115232451856.png)

이렇게 오른쪽으로 보면 썸네일이 첨부되어 있다. 

2. 총 10MB, png, jpg 등이 가능하다. 



3. AttachMent 는 순서를 따른다. 

-> 가장 먼저 들어온 Attachment 만 배너 안에 들어오게 된다.

```swift
content.attachments = [imageAttatchment, image2Attachment]
```

이렇게 되어 있으면 image1 만 배너에 표시되는 것이다. (풀 다운 하면 image2까지 나오게 option 설정도 가능하다.)



4. 썸네일과 풀다운 이미지가 다르게 설정하려면?

```swift
var options = [UNNotificationAttachmentOptionsThumbnailHiddenKey: true]
guard let url = Bundle.main.url(forResource: "hello", withExtension: "png") else { return }
guard let imageAttatchment = try? UNNotificationAttachment(identifier: "hello-image", url: url, options: options) else { return }
        
options = [UNNotificationAttachmentOptionsThumbnailHiddenKey: false]
        
guard let thumburl = Bundle.main.url(forResource: "logo", withExtension: "png") else { return }
guard let thumbAttachment = try? UNNotificationAttachment(identifier: "thumbnail-image", url: thumburl, options: options) else { return }
        
content.attachments = [imageAttatchment, thumbAttachment]
```

Attachment 들마다 option 들을 바꿔주어 hello 는 썸네일에 안나오게, logo 가 나오도록 할 수 있다. 



5. Audio Attachment

```swift
guard let url = Bundle.main.url(forResource: "bell", withExtension: "aif") else { return }
guard let audioAttachment = try? UNNotificationAttachment(identifier: "audio", url: url, options: nil) else { return }
content.attachments = [audioAttachment]
```

구현 방법은 파일 Path를 통해 구현하는 방식으로 똑같다. 

하지만 Pull down 을 하면

![image-20211115233422741](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115233422741.png)

이와 같이 Audio 재생 목록이 나오게 된다. 



6. 동영상 Attachment 의 경우에도 

```swift
        
guard let url = Bundle.main.url(forResource: "video", withExtension: "mp4") else { return }
guard let videoAttachment = UNNotificationAttachment(identifier: "video", url: url, options: nil) else { return }
content.attachments = [videoAttachment]
        
        
```



위와 같은 방식으로 설정하며

![image-20211115233642850](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211115233642850.png)

이와 같은 화면이 Pull down시에 나오게 된다.

