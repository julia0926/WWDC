# Key Features

## Xcode previews
UIkit에서도 Preview를 볼 수 있다.
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-13-01-09.png)
앱이 생성되면 프리뷰를 통해 다양한 구성 및 설정에서 테스트를 할 수 있다.
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-13-07-14.png)

## View controller lifecycle update
`viewWillAppear` -> (new) `ViewIsAppearing` -> `ViewDidAppear`
- 뷰가 나타날 때 마다 작업을 수행하기 가장 좋음
- 호출되면 뷰 컨트롤러와 뷰 모두 최신 특성 컬렉션을 갖는다.
- view의 계층 구조에 추가되었으며, 슈퍼뷰에 의해 배치되었다.
- Back-deploys to iOS 13


![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-13-31-59.png)
1. 뷰가 계층 구조에 추가되기 전과 레이아웃 시작 전 viewWillAppear가 어떻게 호출되는지 확인
2. 애니메이션 발생 후 CATranscation에서 `viewDidAppear`가 어떻게 호출되는지 확인함.
3. `viewIsAppearing`은 `viewWillAppear`과 동일한 트랜잭션 내에서 호출

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-13-34-55.png)
- viewIsAppearing과 viewWillLayoutSubviews 같은 레이아웃 콜백 사이에는 중요한 차이점이 있다.
- `layoutSubviews`를 실행할 때마다 만들어지며, 이는 전환 중에 여러 번 또는 나중에 뷰가 표시되는 동안 언제든지 발생할 수 있습니다.
- `viewIsAppearing`은 모양이 전환되는 동안 한번 호출! 보기에 레이아웃이 안바껴도 여전히 호출

### Trait system enhancements
iOS 17에선 앱의 계층 구조를 통해 자동으로 데이터를 전파합니다.
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-13-40-02.png)
- UItraitCollections에 고유한 데이터 추가를 위해 사용자 지정으로 특성 정의
- 새로운 특성 재정의 API 추가
- `traitCollectionDidChange`를 재정의 할 필요 없이 trait 값이 변경될 때마다 콜백으로 수신하도록 유연한 API 채택 가능.
- UIKit과 SwiftUI 구성 요소 간 데이터를 원활히 전달

## Animated symbol images
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-13-45-09.png)
- 애니메이션을 적용하려면 `addSymbleEffect()` 사용

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-13-45-32.png)
- 다양한 색상 효과를 추가
- `removeSymbolEffect()`를 통해 효과를 종료

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-13-47-59.png)
- `setSymbolImage()` 심볼 간 전환 효과 수행 

## Empty states
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-14-05-49.png)
- 표시할 컨텐츠가 없는 앱의 순간
    - 데이터 없을 때
    - 앱 처음 시작
- `.empty()` : 비어있을 때
- `.loading()` : 로딩중

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-14-07-26.png)
- UIHostingConfiguration API를 활용해 SwiftUI view를 앱 빈 상태로 나타냄

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-14-08-31.png)
- 결과를 반환하지 않는(결과가 없는) 쿼리용으로 설계된 `.search` 사용
- 구성은 보간된(Interpolated) 쿼리와 함께 localized primary 및 secondary 텍스트를 제공한다.
- `setNeedsUpdateContentUnavailable Configuration()` : viewcontroller의 콘텐츠가 바뀔 때마다 호출해서 업데이트 요청

## Internationliaztion
### Dynamic line-height adjustments
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-14-23-37.png)
- 기준선 : 문자나 단어가 놓이는 가상의선
- line-height : 기준선 사이의 공간 
- x-height : 소문자 위에 있는 선

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-14-24-44.png)
- 일부 글꼴엔 이 선을 넘어 확장되는 ascenders(위) and desender(아래)가 있다.

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-14-30-28.png)
- ascender와 descender가 겹치는 것을 방지학 위해 동적 height 높이 조정 기능을 도입
- UILabels와 같은 텍스트 요소가 최적의 가독성을 위해 행 높이와 수직 치수를 자동으로 조정합니다.

### Wrapping and hyphenation
- 중국어, 독일어, 일본어 및 한국어와 같은 언어의 줄 바꿈 동작을 크게 개선
- 개선 사항은 사용 중인 텍스트 스타일의 종류에 따라 다른 규칙을 적용

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-14-32-49.png)
- `typesettingLanguage` traits

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-14-45-17.png)
- iOS 17에선 앱은 이미지 구성에 locale을 제공해 특정 변형을 요청할 수 있다.

## Improvements for iPad
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-14-47-07.png)
- 드래그 제스처를 시작할 수 있는 영역을 확장해 State Manager의 창 드래그 기능을 업데이트

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-14-48-13.png)
- UINavigationBar 내부의 아무데나 끌면 창 이동이 시작된다.
- UINavigationBar 안쓰면 `UIWindowSceneDragInteraction`을 채택하고 view에 추가해서 사용 가능
- 다른 펜 제스처와 관계를 설정 가능
### New Sidebar behavior in Stage Manger
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-15-08-59.png)
- 이중, 삼중 열로 생성된 `UISplitVC`에서 발생
- 자동 동작은 가능할 때마다 열을 바둑판식으로 배열하고, 너비가 줄어들면 필요에 따라 사이드바를 숨기고, 사이드바 버튼을 누를 때 타일링할 공간이 충분하지 않은 경우 보조 열을 사이드바로 오버레이하거나 대체하며, 동작을 재정의

## Document-centric apps
Document에 적합한 viewcontroller 등장
- `UINavigationItemRenameDelegate` 준수해 네비게이션 설정할 때 전체 이름 바꾸기를 제공

## Hover with Apple Pencil
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-15-13-41.png)
- pencil에서 hover를 캡쳐

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-15-14-48.png)
- 더욱 풍부한 iOS 17에선 PencilKit 사용

## General enhancements
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-15-18-36.png)
- 새로운 8가지 일반적인 개선 사항

### CollectionView performance
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-15-19-30.png)
10000개 항목에서 iOS 16보다
- 정렬 순서 바꾸는게 2배
- 절반 삭제하는 업데이트 속도 3배 빠르다
- diffable 쓰든 안쓰든 상관없이 속도 빨라진다.

### Compositional Layout
- `uniformAcrossSiblings` : 레이아웃의 자체 크기 조정 항목이 가장 큰 항목의 크기를 기준으로 일관된 크기를 받을 수 있음
- 가장 큰 항목의 크기를 결정하려면 모든 근처 항목을 만들고 크기를 조정
- 그룹의 많은 수의 항목이 있는 경우는 안사용하는게 좋음

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-20-04-58.png)
두개의 크기가 다른 셀의 항목이 있다.

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-20-05-25.png)
medication cell이 더 큰 sound levels 셀의 높이와 일치하도록 켜져 원하는 레이아웃을 얻는다!

### Spring animation parameters
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-20-07-31.png)
- duration : spring 애니메이션이 안정될 때까지

### Text cursor improvements
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-20-08-32.png)
1. 글자 아이템과 인터렉션 API
2. 메뉴 컨텐츠
3. tag custom

### Default status bar style
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-20-11-21.png)
default 값은 밝으므로 (시계 부분) 검은색으로 표시
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-20-13-41.png)
dark 모드 일때는 반대로 밝은 스타일로 자동 변환됨 
모든 경우가 어둡기만 하거나 밝기만 하지 않으므로 지정값 제거하고 default 값으로 설정해서 사용할 수 있다.

### Drag and drop enhancement
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-20-18-14.png)
- 지원되는 파일 및 콘텐츠를 홈 화면의 아이콘에 드롭하고 해당 앱에서 직접 열 수 있음
- Info.plist에 CFBundleDocumentTypes를 사용해 드롭된 파일 지원하는지 확인 

### ISO HDR image support
> ISO HDR는 이미지 센서나 디지털 카메라에서 사용되는 기술로, High Dynamic Range의 약어입니다. High Dynamic Range는 넓은 다이내믹 레인지를 의미하며, 어두운 영역과 밝은 영역의 세부 사항을 모두 잘 보존하여 사진이나 영상에서 더 풍부하고 현실적인 톤을 제공합니다.

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-20-25-53.png)
UIimageView로 ISO HDR 이미지를 표현할 수 있고, UIGraphicsImageRenderer로 조작 가능

### Page Control
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-20-26-45.png)
- 설정 기간에 따라 자동으로 페이징되는 슬라이드쇼 콘텐츠 표시하는데 사용
- 타임에 따른 진행률을 표시
- 페이지 기간을 쉽게 구성 가능.
- 내장 타이머 존재해서 해당 시간 지나면 자동으로 변경

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-20-28-37.png)
- UIPageControlProgress type을 이용해 수동으로 업데이트도 가능

### Palette menus
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-20-33-41.png)
UIkit first class control로 사용 가능
선택되면 해당 색으로 변함

![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-20-34-24.png)
선택되면 슬래시 표시도 가능

## Next steps
![](../2023/images/What's%20new%20in%20UIKit/2023-06-12-20-37-27.png)
- iOS 17 SDK로 앱 컴파일
- UIkit 기능 통합하고 preview 활용
- UI가 다양한 텍스트 측정항목을 수용하도록 유연하게 설계