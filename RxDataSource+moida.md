# Moida 소스 분석 ( BaseViewCell, BaseViewCellModel )



## 기본 목표



현재 우리가 하려는 목표는 한 화면의 **TableView 에서 Section 마다, 그리고 같은 Section 일지라도 각 Cell 마다 다른 View를 보여주고 싶은 것이다.** 



### 1. RxDataSources Pod

```swift
let data = Observable<[String]>.just(["first element", "second element", "third element"])

data.bind(to: tableView.rx.items(cellIdentifier: "Cell", cellType: MyCell.self)) { index, element, cell in
  cell.textLabel?.text = model
}
.disposed(by: disposeBag)
```

**RXSwift 에서 사용하는 TableVIew, CollectionView 의 기본 형식**은 

위와 같이 cellIdentifier 를 통해 storyboard 혹은 xib 파일 내에서 찾을 인식값과, class 명을 적어주어 index, element, cell 을 return 받는 형식을 이용하게 된다.  하지만 여러개의 섹션, 복잡한 데이터의 Set, 더하기, 수정, 삭제와 같은 animations 를 각 케이스별로 **복잡한 상황을 처리해야 하는 등과 같은 상황이 온다면? -> 분기를 치는 것이 필요 할 것이다.** 



```swift
let dataSource = RxTableViewSectionedReloadDataSource<SectionModel<String, Int>>(configureCell: configureCell)

Observable.just([SectionModel(model: "title", items: [1, 2, 3])])
    .bind(to: tableView.rx.items(dataSource: dataSource))
    .disposed(by: disposeBag)
```

 

그래서 items 에 bind 할시에 **items(dataSource: dataSource))** 이와 같이 **dataSource 처리하는 변수를 따로 분기하여 사용** 가능하게 해준다.  이제 구현부를 살펴보자



dataSource object 를 넘겨줄때 Type 을 지정해준다. 예를들어 SectionOfCustomData 라는 Type 을 넘겨주도록 하자

```swift
struct SectionOfCustomData {
  var header: String    
  var items: [Item]
}
extension SectionOfCustomData: SectionModelType {
  typealias Item = CustomData

   init(original: SectionOfCustomData, items: [Item]) {
    self = original
    self.items = items
  }
}

let dataSource = RxTableViewSectionedReloadDataSource<SectionOfCustomData>(
  configureCell: { dataSource, tableView, indexPath, item in
    let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath)
    cell.textLabel?.text = "Item \(item.anInt): \(item.aString) - \(item.aCGPoint.x):\(item.aCGPoint.y)"
    return cell
})
```



가장 쉽게 이해하면 configureCell 이라는 Closure 안에서는 기존 Swift 에서 진행하였던 cellForRowAt 함수와 같은 cell 을 데이터와 Mapping 시키는 업무를 진행한다고 볼 수 있다.



----------



### 2. Moida 의 BaseCell && BaseCellModel

가장 먼저 모이다 내의 LabelCell ( 1개의 Label 만 존재하는 Custom TableView Cell ) 을 살펴보도록 하자. 



```swift
class LabelCell: BaseCell {
    let label: UILabel = UILabel()
    let button: UIButton = UIButton()

    private(set) weak var cellModel: LabelCellModel?
```

LabelCell 의 윗 부분부터 살펴보면 내용을 적을 label 과, label 을 클릭했을시 실행할 업무를 방출하는 ( button.rx.tap ) button 만 존재하는 간단한 Cell 이다. 

그리고 cell 의 값의 지정을 위해 **ViewModel ( 상위 ViewController의 viewModel ) -> Cell Model -> Cell 로의 data 의 이동 && 디자인 적용**이 이루어지도록 LabelCellModel 이라는 값을 지정하였다. 하여 이를 코드로 살펴보면 



<ViewModel> -> cellModel Make 를 호출

```swift
assetSectionItems.append(LabelCellModel(title: "MyData_ManageAsset_Detail_AccountList_Title".localized,
                                                    height: 24, textColor: .black, font: .font_apple_b(20),
                                                    edgeInsets: UIEdgeInsets(top: 0.0, left: 25.0, bottom: 0.0, right: 25.0),
                                                    backgroundColor: .clear))
```



<Initializer>

```swift
class LabelCellModel: BaseCellModel {

    var title: String
    var titleIndent: CGFloat = 0
    var edgeInsets: UIEdgeInsets
    var textColor: UIColor
    var font: UIFont
  	......
    var differentFontText = UIConstant.empty
    var differentFont: UIFont?
    var differentColor = UIColor.black

    override var cellIdentifier: UITableViewCell.Type {
        LabelCell.self
    }

    init(title: String,
         width: CGFloat? = nil,
         height: CGFloat? = nil,
         textColor: UIColor,
         font: UIFont,
         textAlignment: NSTextAlignment = .left,
         lineBreakMode: NSLineBreakMode = .byWordWrapping) {
        self.title = title
        self.width = width
        self.height = height
      	.....
        self.highLightColor = .gray900
        self.backgroundColor = .white
        edgeInsets = UIEdgeInsets(top: 0, left: 0, bottom: 0, right: 0)
        self.isUnderlinedTitle = false
    }
  ...
```



<LabelCell>

```swift
override func configureWith(value: BaseCellModelProtocol) {
        guard let cellModel = value as? LabelCellModel else { return }

        self.cellModel = cellModel
        self.lets {
            $0.selectionStyle = .none
        }

        label.lets {
            $0.text = cellModel.title
            $0.textColor = cellModel.textColor
            $0.numberOfLines = cellModel.numberOfLines
            $0.textAlignment = cellModel.textAlignment
            $0.lineBreakMode = cellModel.lineBreakMode
            $0.font = cellModel.font
        }
        self.backgroundColor = cellModel.backgroundColor
```



이와 같이 configureWith 내에 cellModel 을 받아 해당 데이터들을 Binding 시켜주고 있다.



-----------



### 3. CellModel을 Add 할 SectionModel (RxDataSources pod)



이제 CellModel 을 적용할 Section의 Model 을 생성해보자 

먼저 Section 별 case 를 저장할 enum Type을 정의 하자.



```swift
enum SettingAssetDetailSectionModel {
    case topSection(_ cellModel: [BaseCellModelProtocol] = [])
    case infoSection(_ cellModel: [BaseCellModelProtocol] = [])
    case assetSection(_ cellModel: [BaseCellModelProtocol] = [])
    case bottomSection(_ cellModel: [BaseCellModelProtocol] = [])
}
```



그리고 이를 RXDataSources pod 파일에 적용하기 위해  

참고 사항 RxDataSource 를 구현하는 방법



```swift
/*
Start by defining your sections with a struct that conforms to the SectionModelType protocol:
define the Item typealias: equal to the type of items that the section will contain
declare an items property: of type array of Item
*/

struct SectionOfCustomData {
  var header: String    
  var items: [Item]
}
extension SectionOfCustomData: SectionModelType {
  typealias Item = CustomData

   init(original: SectionOfCustomData, items: [Item]) {
    self = original
    self.items = items
  }
}
```

이와 같이 Item의 Typealias 별칭을 지어주고, initializer 를 직접 구현을 해주어야 한다. -> 이것을 모의다에 적용한 결과를 보면 아래의

```swift
	extension SettingAssetDetailSectionModel: SectionModelType {
    typealias Item = BaseCellModelProtocol

    var items: [BaseCellModelProtocol] {
        switch self {
        case .topSection(let items):
            return items
        case .infoSection(let items):
            return items
        case .assetSection(let items):
            return items
        case .bottomSection(let items):
            return items
        }
    }

    init(original: SettingAssetDetailSectionModel, items: [BaseCellModelProtocol]) {
        self = original
    }
}
```

SettingAssetDetailSectionModel 이 SectionOfCustomData 와 같은 일을 하는 선언임을 알 수가 있다. 

그리고 SettingAssetDetailSectionModel 의 경우 item을 반환할 case 가 존재하므로 각 case 에 getter 를 정의 해준다. 



**RxDataSources pod 파일 내의 SectionModelType, 그리고 identifier 를 비교할수 있는 Identifiable 프로토콜을 둘다 만족시키는 custom Type인   SectionModelType** protocol 을 생성한다. 

```swift
protocol SectionModelType: Differentiator.SectionModelType & Identifiable {}
```



위의 SettingAssetDetailSectionModel 의 경우 RxDataSource 에 들어갈 Item 들의 Type 이 될 것이다. 



마지막으로 

정의한 SectionModel 을 RxDataSource 안에 넣어 cell 들을 type 에 따라 분기 하면 각 cell 마다의 처리가 가능해진다. 



```swift
private func dataSource() -> RxTableViewSectionedReloadDataSource<SettingAssetDetailSectionModel> {
        .init(configureCell: { [weak self] _, tableView, indexPath, element -> UITableViewCell in
            let cell = tableView.dequeueBaseCell(class: element.cellIdentifier, for: indexPath, configure: element)
            guard let self = self else { return cell }
            switch (cell, element) {
            case let (cell as SettingAssetDetailTopCell, element as SettingAssetDetailTopCellModel):
                self.setDriver(cell)
            case let (cell as SettingAssetDetailBottomCell, element as SettingAssetDetailBottomCellModel):
                self.setDriver(cell)
            default:
                break
            }
            return cell
        })
    }
```



https://github.com/RxSwiftCommunity/RxDataSources

**이것에 대한 이해가 필수이다**

