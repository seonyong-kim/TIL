# Modal
Modal은 모달 창을 구현하기 위해 사용되는 컴포넌트이다. 모달 창이 뭔지 알아보면 <br>

## 정의
그래픽 사용자 인터페이스의 요소 중 하나. <br>
주 창에 종속된 그래픽 인터페이스를 의미한다. 프로그램 자체에서 새 창을 띄우는 팝업 창과 달리 같은 창 내부에서 상위 레이어를 띄우는 방식을 사용하는 창을 의미한다. <br>
모달 창 특성상 모달 창이 띄워질 경우 이 창을 종료시키지 않는 이상 원래 창에서 진행 중이던 작업에는 손을 댈 수 없다. <br>
따라서 사용자의 이목을 집중시키기 때문에 신중히 사용될 필요가 있다. 팝업 창을 대신하는 모달 창이 무분별하게 사용될 경우 창의 내용을 보지도 않고 '닫기'나 'OK' 버튼을 누르는 습관을 만들 수도 있다. <br>

## Modal의 속성
|속성|Type|설명|기본값|
|---|---|---|---|
|animationType|string(가능한 값: ‘slide’, ‘fade’, ‘none’)|모달의 열리고 닫힐 때 적용되는 애니메이션 유형을 설정|none|
|backdropColor|Color|모달의 배경색 설정|White|
|hardwareAccelerated|bool|이 소품은 기본 창에 대한 하드웨어 가속을 강제할지 여부를 제어|false|
|navigationBarTranslucent|bool|모달을 시스템 막대 아래에 배치할지 여부를 결정|false|
|onRequestClose|function|모달이 닫힐 때 호출되는 콜백 함수를 설정(단, 뒤로가기 버튼을 눌렀을 경우에도 호출)||
|onShow|function|모달이 나타났을 때 호출되는 콜백 함수를 설정||
|statusBarTranslucent|bool|속성은 모달이 시스템 상태 표시줄 아래에 있어야 하는지 여부를 결정|false|
|transparent|boolean|모달이 투명하게 표시되는지 여부를 설정|false|
|visible|bool|소품은 모달이 표시되는지 여부를 결정|true|
