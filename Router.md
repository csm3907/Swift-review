# Openbanking Router 화면전환 패턴 분석 



## 1. OpenBankingIntroViewController & ViewModel

<img src="/Users/choeseungmin/Library/Application Support/typora-user-images/image-20211025222453093.png" alt="image-20211025222453093" style="zoom:50%;" />



위의 화면이 가장 먼저 불러지게 되고, 해당 화면에서 viewDidLoad 함수를 통해 viewModel.input의 값들을 fetch 해주는 연산을 수행합니다. 이후 bindViewModel 을 통해 **ViewModel 의 데이터와 View를 bind** 시킵니다.

```swift
override func bindViewModel() {
        registerAccountButton.rx.tap.throttle()
            .subscribe(
                onNext: { [weak self] in
                    guard let self = self else { return }
                    self.viewModel.inputs.nextScene.accept(.terms)
                }
            ).disposed(by: disposeBag)
}
```





OpenBankingIntroViewModelInput 의 경우 **nextScene 이라는 PublishRelay 를 가지고 있으며**, 해당 Relay 의 경우 OpenBankingIntroViewModel의 **NextScene enum 타입**을 갖게 됩니다. 

```swift

protocol OpenBankingIntroViewModelInputs {
    typealias Routes = OpenBankingTermsRoute
    func setRouter(router: Routes)
    var nextScene: PublishRelay<OpenBankingIntroViewModel.NextScene> { get }
    func setData(data: OpenBankingDataType?)
    func fetchOpenBankingIntroData()
}

```



NextScene 이 변경이 될시 **OpenBankingIntroViewModel 에서 구독하는 코드가 실행**이 되고 

```swift
nextScene.subscribe(onNext: { [weak self] scene in
            guard let self = self else { return }
            switch(scene) {
            case .terms:
                self.router?.gotoOpenBankingTerms(with: self.data)
            }
        })
        .disposed(by: disposeBag)
```



위의 gotoOpenBankingTerms 함수는 

```swift
func gotoOpenBankingTerms(with data: OpenBankingDataType?, and unwindTransition: TransitionType) {
        let router = BaseRouter(unwindTransition: ShowTransition())
        let vc = OpenBankingTermsViewController.configureWith(data, and: router)
        router.rvc = vc
        route(to: vc, with: unwindTransition)
}
```



**BaseRouter 객체 생성 및 route 함수를 호출**하게 됩니다. 



--------



## 2. BaseRouter Class 



BaseRouter Class 의 경우 총 3개의 route 함수가 구현되어 있으며 각 route 함수는 Parameter 의 갯수로 구분이 되어 실행이 됩니다. 

```swift
extension BaseRouter: Routable {
    func route(to vc: UIViewController, with transition: TransitionType, completion: (() -> Void)?) {
        guard let rvc = rvc else { return }
        transition.open(vc, from: rvc, completion: completion)
    }

    func route(to vc: UIViewController, with transition: TransitionType) {
        route(to: vc, with: transition, completion: nil)
    }

    func route(with id: String) {
        rvc?.performSegue(withIdentifier: id, sender: rvc)
    }
}
```



OpenBankingIntroViewController 의 경우 **completionHandelr 가 nil 인 trainsition.open 함수가 호출**이 되고,  이동할 viewController의 경우 Parameter로 넘어온 OpenBankingTermsViewController, TransitionType은 ShowTransition 객체가 됩니다. 



하여 **ShowTransition의 open 함수가 호출**이 되고, 

```swift
func open(_ viewController: UIViewController, from: UIViewController, completion: (() -> Void)?) {
        self.from = from
        openCompletion = completion
        nav?.delegate = self
        nav?.pushViewController(viewController, animated: animated)
}
```



from 으로 받은 OpenBankingTermsViewController, completion nil, 을 이용하여 **UINavigationController 의  pushViewController 가 수행, 화면 이동**이 이루어집니다. 



마지막 주의 사항으로 다음 ViewController 생성시 configureWith 함수를 통해 OpenBankingTermsViewModel 의 router 를 설정하고 해당 Router가 다시 다음 화면을 call 하게 됩니다. 

```swift
let vc = OpenBankingAccountTermsViewController.configureWith(data, and: router)
```



----



## 3. 정리 



### Router

Router는 **실제로** ViewController를 present하고, dismiss 해주는 역할을 수행한다.

ViewController를 present하는 **방법**(navigation에 push한건지, modal로 띄운건지 등)은 알고 있지만, 다음에 어떤 ViewController가 present 될지는 알지 못한다.

```swift
public protocol Router: class {
    func present(_ viewController: UIViewController, animated: Bool)
    func present(_ viewController: UIViewController, animated: Bool, onDismissed: (() -> Void)?)
    func dismiss(animated: Bool)
}
```

위 코드는 Router protocol이고, Concrete Router는 다음과 같은 것들이 될 수 있다.

- NavigationController를 이용해 화면을 조작하고 싶다면 NavigationRouter를 만들어 사용
- 화면을 BottomSheet 형태로 띄우고 싶다면 BottomSheetRouter를 만들어 사용







https://velog.io/@ssionii/Coordinator-Pattern%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%84%EB%9D%BC%EB%B3%B4%EC%9E%90

위의 링크를 참조해주시면 감사하겠습니다. 



