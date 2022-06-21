# WWDC22 - UIKit

# 주제

**Productivity improvements**

**Control enhancements**

**API refinements**

**UIKit and SwiftUI**

## 1. Productivity

- Improved navigation bars
- Title menu
- Find and replace
- Editing interactions

### Improved navigation bars

**Navigation styles**

![Untitled](WWDC22%20-%20UIKit%20908dfd004e9b4777a050a19472cb1d12/Untitled.png)

iOS16에서는 두 가지 다른 네비게이션 스타일 **Browser, Editor 스타일**이 등장했다.

이들로 하여금 브라우저 베이스 앱들이 Editor style 인터페이스와 함께 유저들에게 친숙한 인터페이스를 제공할 수 있고, Browser style로 document 베이스 앱을 도울 것이다.

또한 타이틀 뷰 대신에 **센터 아이템들로 하여금 유저들에게 더 많은 선택지**를 준다. 어플리케이션 윈도우들이 옆에 있을 때, 자동적으로 아이템들이 스크린에 맞추어 오버플로우 메뉴가 된다. 추가적으로 Mac Catalyst는 필요로 하는 코드 없이 네비게이션 바를 개선하는 이점을 가진다.

### Find and replace

![Untitled](WWDC22%20-%20UIKit%20908dfd004e9b4777a050a19472cb1d12/Untitled%201.png)

UIKit view들(UITextView, WKWebView, PDFView)을 위한 하나의 flag와 함께 활성화될 수 있다.

### Edit menu

![Untitled](WWDC22%20-%20UIKit%20908dfd004e9b4777a050a19472cb1d12/Untitled%202.png)

iOS16에서 edit menu는 큰 업데이트를 가진다. 터치 상호작용에서 더 인터랙지브하게 재디자인된 메뉴를 가질 수 있다. 또한 포인터들을 위해서 더 특징화된 컨텍스트 메뉴를 제공한다. 이 새로운 특징들을 제공하기 위해서 UIMenuController(Deprecated)의 대체재인 **UIEditMenuInteraction API**를 사용할 수 있다.

## 2. Control enhancements

### UICalendarView

이제 프로젝트에 캘린더를 사용하고자 할 때, UICalendarView와 함께 사용할 수 있다. 

- 단일, 다중 선택 같은 선택 행동들의 타입 제공
- 선택 가능, 선택 불 가능 날짜를 설정
- 다양한 기본 데코레이션 같은 커스텀 뷰 제공
- NSDate 대신 NSDateComponents를 표현하며, 더 옭은 date value 제공

다중 선택을 위해서는 **UICalendarSelectionMultiDateDelegate**, 단일 선택을 위해서는 **UICalendarSelectionSingleDateDelegate을 채택하면 된다.**

```swift
private lazy var calendarView: UICalendarView = {
        let calendarView = UICalendarView()
        calendarView.calendar = Calendar(identifier: .gregorian)
        calendarView.timeZone = .autoupdatingCurrent
        calendarView.locale = .current
        calendarView.fontDesign = .default
        calendarView.delegate = self
        calendarView.visibleDateComponents = DateComponents(calendar: Calendar(identifier: .gregorian), year: 2022, month: 6, day: 1)

        // Set singleDateSelection if you want to set for Single Selection
        calendarView.selectionBehavior = multiDateSelection
        calendarView.selectionBehavior = singleDateSelection
        return calendarView
    }()

    private lazy var multiDateSelection: UICalendarSelectionMultiDate = {
        let multiDateSelection = UICalendarSelectionMultiDate(delegate: self)
        return multiDateSelection
    }()

    private lazy var singleDateSelection: UICalendarSelectionSingleDate = {
        let singleDateSelection = UICalendarSelectionSingleDate(delegate: self)
        return singleDateSelection
    }()
```

**UICalendarViewDelegate 프로토콜**을 채택함으로써 컨트롤러로 하여금 뷰를 꾸밀 수 있다.

```swift
extension UICalendarViewExample: UICalendarViewDelegate {
    func calendarView(_ calendarView: UICalendarView, decorationFor dateComponents: DateComponents) -> UICalendarView.Decoration? {
        guard let event = eventDays.filter({ $0.dateComponents.isSameDate(with: dateComponents) }).first else {
            return nil
        }

        switch event.decorationType {
        case .none:
            return nil
        case .defaultDecorator:
            return .default(color: .purple, size: .large)
        case .image:
            return .image(.init(systemName: "applelogo"), color: .red)
        case .customView:
            return .customView {
                let label = UILabel()
                label.text = "WWDC"
                label.textColor = event.titleColor
                label.font = .boldSystemFont(ofSize: 10)
                return label
            }
        }
    }
}
```

![Untitled](WWDC22%20-%20UIKit%20908dfd004e9b4777a050a19472cb1d12/Untitled%203.png)

### PageControl

![Untitled](WWDC22%20-%20UIKit%20908dfd004e9b4777a050a19472cb1d12/Untitled%204.png)

UIPageControl을 더욱 즐기고 작업을 쉽게 하기 위해 **control layout의 수직, 수평같은 방향을 설정**할 수 있다. 게다가, **커스텀 이미지** 특징 또한 상태에 맞추어 추가되었다.

### UIPasteControl

![Untitled](WWDC22%20-%20UIKit%20908dfd004e9b4777a050a19472cb1d12/Untitled%205.png)

iOS16 전에는 copy-paste 기능은 유저에게 배너 형식으로 보여졌다. 지금은 **alert형식으로 대체**되었다. 이 permission alert는 자동적으로 시스템에 의해 작동되며, 답변에 따라 컨텐츠 접근을 할 수 있다.

## 3. API Refinements

### Customizing Sheets

![Untitled](WWDC22%20-%20UIKit%20908dfd004e9b4777a050a19472cb1d12/Untitled%206.png)

Sheets는 iOS15에 등장하여, iOS16에서는 그들의 **사이즈를 커스텀**할 수 있다. 계산된 detents는 safe area를 계산하면 안 된다.

```swift
extension UISheetPresentationController.Detent.Identifier {
    static let developerDefiened = UISheetPresentationController.Detent.Identifier("developerDefiened")
}

final class UISheetPresentationControllerExample: UIViewController {

    private func presentBottomSheet() {
        let vc = SFSymbolsExample()
        vc.view.backgroundColor = .white
        guard let sheet = vc.sheetPresentationController else {
            return
        }

       //  sheet.largestUndimmedDetentIdentifier = .developerDefiened - You can add if clear background.
        sheet.detents = [
            .custom(identifier: .developerDefiened) { context in
                context.maximumDetentValue * self.multiplier
            }
        ]
        self.present(vc, animated: true)
    }
}
```

### Symbol images

![Untitled](WWDC22%20-%20UIKit%20908dfd004e9b4777a050a19472cb1d12/Untitled%207.png)

iOS15이전에는 monochrome 렌더링이 디폴트로 사용되었다. iOS16에서는 계층적 렌더링이 사용된다. 만약 애플리케이션에서 monochrome을 사용하기 원한다면, **UIImage.SymbolConfiguration.preferringMonochrome()**을 호출함으로써 monochrome을 사용하는 것을 지속할 수 있다.

또한 iOS16에서 **다양한 심볼들이 등장**하였다. 팔레트 설정에 의해 렌더링 모드를 사용할 수 있다.

 ****

```swift
@objc private func sliderDidValueChange(_ sender: UISlider) {
    imageView.image = UIImage(
        systemName: "wifi",
        variableValue: Double(sender.value),
        configuration: UIImage.SymbolConfiguration(paletteColors: [.orange])
    )
}
```

### Self-resizing cells

![Untitled](WWDC22%20-%20UIKit%20908dfd004e9b4777a050a19472cb1d12/Untitled%208.png)

iOS16에서 UICollectionView와 UITableView는 self resizing cell을 가지게 된다. “selfSizingInvalidation” 파라미터가 기본으로 접근가능하다. 

UICollectionView에서는 **UICollectionLayoutListConfiguration와UICollectionViewCompositionalLayout**를 사용하는 것이 필요하다. 또한 셀 내에서 컨텐츠뷰 내에 하위뷰들을 더해야 한다. resize 작동을 수동으로 하기위해서는 셀 내부에서 **self.invalidateIntrinsicContentSize()**를 호출할 수 있다.

```swift
private lazy var collectionView: UICollectionView = { [weak self] in
    var config = UICollectionLayoutListConfiguration(appearance: .insetGrouped)
    let layout = UICollectionViewCompositionalLayout.list(using: config)
    let collectionView = UICollectionView(
        frame: .zero,
        collectionViewLayout: layout
    )
    collectionView.translatesAutoresizingMaskIntoConstraints = false
    collectionView.dataSource = self
    collectionView.register(
        SelfResizingCollectionViewCell.self,
        forCellWithReuseIdentifier: SelfResizingCollectionViewCell.description()
    )

    //Enable by default
    collectionView.selfSizingInvalidation = .enabledIncludingConstraints
    return collectionView
}()
```

## 4. UIKit and SwiftUI

두 프레임워크를 하나의 앱에서 함께 쓰기 위해 개발되었다.

### UIHostingConfiguration

**UIHostingConfiguration**을 활용하여 **SwiftUI 뷰를 UITableView나 UITableView 안에서 사용**할 수 있다. 가장 좋은 점은 UIKit과 SwiftUI **함께 셀을 만들어 사용**할 수 있다는 점이다.

```swift
extension UIHostingConfigurationExample: UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return contentDatas.count
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let contentData = contentDatas[indexPath.row]
        let cell = indexPath.row == 0 ? createSwiftUICell(with: contentData) : createUIKitCell(with: contentData)
        return cell
    }
}

private extension UIHostingConfigurationExample {
    func createSwiftUICell(with contentData: ContentData) -> UITableViewCell {
        let cell = UITableViewCell()
        cell.contentConfiguration = UIHostingConfiguration {
            CustomSwiftUICell(contentData: contentData)
        }
        return cell
    }

    func createUIKitCell(with contentData: ContentData) -> UITableViewCell {
        let cell = CustomUIKitCell()
        cell.configure(with: contentData)
        return cell
    }
}
```

![스크린샷 2022-06-21 오후 3.54.51.png](WWDC22%20-%20UIKit%20908dfd004e9b4777a050a19472cb1d12/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-06-21_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_3.54.51.png)

### UIDevice Deprecations

```swift
// Synonym for model. Prior to iOS 16, user-assigned device name (e.g. @"My iPhone").
 // In iOS 16 => iPhone 13 Pro Max
 // Now it reports device name
 UIDevice().name

 //Now UIDevice().orientation now supported 
 //use preferredInterfaceOrientationForPresentation
 UIDevice().orientation
 UIViewController().preferredInterfaceOrientationForPresentation
```

### 참고

- [https://developer.apple.com/videos/play/wwdc2022/10068/](https://developer.apple.com/videos/play/wwdc2022/10068/)
- [https://appcircle.io/blog/whats-new-in-uikit-at-ios-16-wwdc22/#Self-resizing_Cells](https://appcircle.io/blog/whats-new-in-uikit-at-ios-16-wwdc22/#Self-resizing_Cells)