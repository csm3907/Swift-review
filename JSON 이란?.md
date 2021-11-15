# JSON 



### ## 1. JSON 이란

JavaScript Object Notation 을의미하고 Dictionary 와 비슷한 형태를 뜬다.

Key 와 Value로 이루어진다.



-----

### Encoding

**객체를 JSON Data 형태로 바꾸는것**을 

![image-20211110220912743](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110220912743.png)

**Encoding** 이라고 하며, JSONEncoder() 객체를 통해서 이루어진다. 

이때, 객체는 **Encodable 프로토콜을 채택하고 있어야 한다.** 



![image-20211110221225060](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110221225060.png)

이와 같이 Encoding 방식을 snake 로 지정하면 

![image-20211110221255250](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110221255250.png)

이와 같이 데이터가 생성이 된다. 



### 날짜값의 Encoding 

![image-20211110221421524](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110221421524.png)

이와 같이 **dateEncodingStrategy 로 지정**을 하면 기존의 숫자값이 date 값으로 변환하여 들어오게 된다.

![image-20211110221509007](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110221509007.png)

하지만 **DateFormatter 로 직접 지정도 역시 가능**하다. 

![image-20211110221613361](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110221613361.png)



------



## Decoding 



JSON 데이터를 객체로 변환하는것을 의미한다. 

![image-20211110221830821](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110221830821.png)



**JSONDecoder 객체를 통해서 jsonData 를 Person Type 으로 mapping 하고 있다.** 

만약 **key와 이름이 다르거나, 호환되지 않는 Type 이** 있다면 -> **decoding 되지 않는다.** 



![image-20211110222017057](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110222017057.png)

만약 서버에서 내려주는 data 가 snakeCase 라면? -> 이렇게 이름을 지어주어 변경이 가능하다. 



![image-20211110222209237](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110222209237.png)

반대로 이렇게 iso 형식으로 Date 값을 받는것도 가능하다. 



------



## JSON Encoding, Decoding 커스텀



![image-20211110222516779](/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211110222516779.png)

Enum Type 중 CodingKey 프로토콜을 채택하여 이름이 다른 변수와 JSONData를 Mapping 시켜줄 수 있다. 



**Encoding 된 데이터는 Container 안에 저장**된다. 

때문에 **container 로 각 case 에 직접 Mapping 을 해주는 것**이다.

``` swift
func encode(to encoder: Encoder) throws {
	var container = encoder.container(keyedBy: CodingKeys.self)
	try container.encode(name, forKey: .name)
	
	guard (30...60).contains(age) else { throw EncodingError.invalidRange }
  try container.encode(age, forKey: .age)
  try container.encodeIfPresent(address, forKey: .address)
}
```

Optional 이면 encodeIfPresent 로 매핑해 주어야 한다.



**Decoding 역시 Data를 가지고 있는 Container 로 시작**한다. 

``` swift
init(from decoder: Decoder) throws {
  let container = try decoder.container(keyedBy: CodingKeys.self) // CodingKeys 는 enum CodingKey를 받은 객체 Type을 의미한다.
  
  name = try container.decode(String.self, forkey: .name)
  age = try container.decode(Int.self, forkey: .age)
  guard (30...60).contains(age) else {throw DecodingError.invalidRange}
  address = container.decodeIfPresent(String.self, forkey: .address)
}
```

































