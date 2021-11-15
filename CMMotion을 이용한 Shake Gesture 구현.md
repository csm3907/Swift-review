# Accelermeter Events



iOS 에서는 3개의 좌표가 존재한다. 

[<img src="https://docs-assets.developer.apple.com/published/96e9d46b41/c9b606b2-9a52-487e-8385-e710ffa1ce5f.png">](http://google.com.au/)



X, Y, Z 이렇게 3개의 좌표를 가지고 있으며 이 움직임을 **Detect 하는 객체가 CMMotionManager**이다



``` swift
var coreMotionManager: CMMotionManager!
override func viewDidLoad() {
    super.viewDidLoad()
    coreMotionManager = CMMotionManager()
    self.getDeviceMotion(coreMotionManager : coreMotionManager)
 }
private func getDeviceMotion(coreMotionManager : CMMotionManager) {
    if coreMotionManager.isAccelerometerAvailable {
        coreMotionManager.accelerometerUpdateInterval = 1
        coreMotionManager.startAccelerometerUpdates(to: .main) {
            (data, error) in
            guard let data = data, error == nil else {
                return
            }
            print(data)
        }
    }
}
```



1. 먼저 viewDidLoad 에서 CMMotionManager 객체를 초기화해준다.
2. 이후 getDeviceMotion 함수를 통하여 Accelermeter의 사용이 가능한지 여부를 확인후 
3. accelerMeterUpdateInterval = 1 로 설정, 1초마다 AccelerMeter Update 를 감지하여 Closure를 실행하겠다는것을 의미한다.
4. 마지막으로 startAccelermeterUpdates 를 통해 data 혹은 error 를 감지하게 된다. 



위의 내용을 이용하여 Shake 를 탐지하여 ShakeCount 의 값을 올리는 작업을 수행하려 한다. 

```swift
override func motionBegan(_ motion: UIEvent.EventSubtype, with event: UIEvent?) {
        if motion == .motionShake {
            log.d("motionBegan 시작")
            coreMotionManager.startDeviceMotionUpdates(to: OperationQueue.main) { [weak self] motionData, error in
                guard let self = self else { return }
                if let error = error {
                    log.d("Motion 캡쳐 오류 발생 \(error.localizedDescription)")
                    return
                }
                guard let data = motionData else { return }
                if data.userAcceleration.x > 1.0 || data.userAcceleration.x < -1.0 {
                    if data.userAcceleration.x > 1.0 {
                        self.xInPositiveDirection = data.userAcceleration.x
                    }
                    
                    if data.userAcceleration.x < -1.0 {
                        self.xInNegativeDirection = data.userAcceleration.x
                    }
                    
                    if self.xInPositiveDirection != 0.0 && self.xInNegativeDirection != 0.0 {
                        self.motionShakeCount = self.motionShakeCount + 1
                        log.d("Shake Motion Count : \(self.motionShakeCount)")
                        self.xInPositiveDirection = 0.0
                        self.xInNegativeDirection = 0.0
                    }
                    
                    if self.motionShakeCount > 2 {
                        self.motionShakeEnded()
                        self.motionShakeCount = 0
                    }
                }
            }
        }
    }
```



가속을 측정하여, 양의 x 좌표, 음의 x 좌표값을 받아 둘다 0이 아니라면? -> 한번 위 아래로 흔들렸다는 말이 되므로 shakeCount를 1 더해준다.



마지막으로 motion Shake Count 값이 2번 이상일경우 motionShakeEnded() 라는 custom Event Function을 처리한 후 다시 motion Shake Count 값을 0 으로 초기화 해준다.

















