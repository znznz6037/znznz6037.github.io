---
layout: post
title: "@IBOutlet, @IBAction 의 역할"
date: "2023-07-23 15:30:00"
categories: [Swift]
tags: [iOS, Swift]
---

### IBOutlet
---
스토리보드에 선언한 View 객체를 `인터페이스 빌더(IB)가 인식`할 수 있도록 하여 연결해주는 역할


### IBAction
---
스토리보드에 선언한 View 객체에서 이벤트(액션) 발생 시 수행할 메서드와 연결해주는 역할  
즉, 인터페이스 빌더에서 생성한 객체가 전달하는 이벤트를 코드의 액션과 연결

### 예제
```Swift
import UIKit

class ViewController: UIViewController {
    
    @IBOutlet var playPauseButton: UIButton!
    @IBOutlet var timeLabel: UILabel!
    @IBOutlet var progressSlider: UISlider!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
    }

    //버튼 클릭 시 수행 할 액션 정의
    @IBAction func touchUpPlayPauseButton(_ sender: UIButton) {
        print("Button tapped")
    }
    
    //슬라이더 조작 시 수행 할 액션 정의
    @IBAction func sliderValueChanged(_ sender: UILabel) {
        print("Slider value changed")
    }
}
```

![img](https://i.imgur.com/ZTTOjOw.png)

  
### IBOutlet 객체 선언 시 왜 암시적 옵셔널(강제 언래핑)을 사용하는걸까?
스위프트를 처음 공부하면서 암시적 옵셔널은 사용하지 않는 것이 일반적이다라고 배웠는데,
암시적 옵셔널을 사용하고 있어 조금 의아했다.

IBOutlet 객체가 nil 인 경우, 애초에 잘못된 상황이므로 런타임에러를 통해서라도 다시 시작해야한다는 의미인가?
구글링해봐도 명확한 답이 나오지 않아서... 다음에 수준이 높아졌을 때 다시 한번 고민해보자

### 컨트롤 이벤트의 종류

|이벤트|행위|
|---|---|
|touchDown|컨트롤을 터치했을 때|
|touchDownRepeat|컨트롤을 연속 터치할 때|
|touchDragInside|컨트롤 범위 내부에서 터치한 영역을 드래그할 때|
|touchDragOutside|컨트롤 범위 외부에서 터치한 영역을 드래그할 때|
|touchDragEnter|터치 영역이 컨트롤 범위 외부로 나갔다가 다시 내부로 진입할 때|
|touchDragExit|터치 영역이 컨트롤 범위 외부로 나갔을 때|
|touchUpInside|컨트롤 영역 내부에서 터치 후 뗐을 때|
|touchUpOutside|컨트롤 영역 내부에서 터치 후 외부에서 뗐을 떄|
|touchCancel|터치를 취소하는 이벤트|
|valueChanged|터치를 드래그 및 다른 방법으로 조작하여 값이 변경됐을 때|
|primaryActionTriggered|버튼이 눌릴 때(tvOS에서 주로 사용)|
|editingDidBegin|UITextField에서 편집이 시작될 때|
|editingChanged|UITextField에서 값이 바뀔 때마다 발생|
|editingDidEnd|UITextField에서 외부 객체와의 상호작용으로 인해 편집이 종료됐을 때|
|editingDidEndOnExit|UITextField 편집상태에서 키보드의 Return 키를 터치했을 때|
|allTouchEvents|모든 터치 이벤트|
|allEditingEvents|UITextField에서 모든 편집작업 이벤트|
|applicationReserved|각각의 애플리케이션에서 프로그래머가 임의로 지정할 수 있는 이벤트 값의 범위|
|systemReserved|프레임워크 내에서 사용하는 예약된 이벤트 값의 범위|
|allEvents|시스템 이벤트를 포함한 모든 이벤트|

[출처: 야곰의 iOS 앱 프로그래밍](https://www.boostcourse.org/mo326)