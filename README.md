# Sunny Day

> 실시간 날씨, 대기오염 정보 그리고 날씨 예보를 제공하며 자주 확인하는 정보만으로 깔끔하게 구현한 애플리케이션

&nbsp;
## 앱 설명

&nbsp;
### 실행 화면
![](https://github.com/0jun0815/YJWeather/blob/master/Images/Sunny%20Day.png)

&nbsp;
### 앱스토어
[Sunny Day](https://itunes.apple.com/kr/app/sunny-day/id1385458263?mt=8)

&nbsp;
### 기능
* 리스트 형식으로 지역별 날씨 정보를 제공, 상단부터 현재 위치 정보 그리고 추가된 지역순으로 정렬
* 현재 하늘 상태에 바뀌는 색상(맑음: 초록색, 구름조금: 노랑색, 구름많음: 주황색, 비: 파랑색, 눈: 하늘색)
* 사용자의 터치에 따라 요약 정보, 상세 정보를 제공

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![](https://github.com/0jun0815/YJWeather/blob/master/Images/ExtensibelCell.gif)

* 요약 정보에서는 실시간 하늘 상태, 현재 온도, 최고/최저 온도, 습도, 풍향/풍속, 강수확률, 미세먼지 등급을 제공
* 상세 정보에서는 24시간 동안의 시간대별 날씨 예보와 통합대기 환경, 미세먼지, 초미세먼지, 일산화탄소, 이산화탄소, 오존, 아황산가스 등급과 농도를 확인 가능
* 하단의 버튼으로 지역 추가, 제거

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![](https://github.com/0jun0815/YJWeather/blob/master/Images/AddRemove.gif)

&nbsp;
### 사용된 기술
- 개발언어는 `Swift4.0`을 사용
- `Alamofire`, `DispatchGroup`을 활용한 `Request 클래스`를 구현하여 HTTP/HTTPS 통신
- 각각 다른 기관에서 제공하는 API의 JSON 데이터를 추출, 변환하는 `데이터 처리 클래스`를 만들어 서버의 부재를 해결
- `CoreData`, `UserDefaults`, `DAO 패턴 클래스`로 사용자 데이터를 저장, 삭제 및 관리
- `CoreLocation`을 사용하여 위치 기반 데이터를 제공
- 경위도, TM 좌표, 격자 좌표간의 `좌표 변환 클래스`를 구현
- `DispatchQueue`를 활용한 비동기 프로그래밍
- 터치에 따라 확장, 축소되는 `커스텀 UITableViewCell`, 좌우로 스크롤되는 `커스텀 UICollectionViewCell`을 구현
- 사용자 검색에 즉시 반응하는 `UISearchBar`를 구현

&nbsp;
### 문제점 및 해결 과정
* [x] 각각의 공공데이터 API를 호출하는 과정에서 동기적으로 해결해야 하는 상황이 발생(대기오염 정보의 경우 근접측정소 목록 API 호출 -> 측정소별 측정정보 API 호출), 그리고 모든 호출이 종료된 후 데이터를 통합할 적절한 순간이 필요
    * 완료 클로저만을 사용하여 해결할 경우 가독성이 저하되고 기능별  분리가 의미 없어짐
    * 따라서 완료 클로저와 DispatchGroup의 enter(), leave(), notify() 메서드를 사용하여 해결 
* [x] 데이터 출처 기관마다 매개변수로 요구하는 위치 정보 좌표의 기준이 다름
    * 국토지리정보원의 자료를 참고하여 경위도, TM 좌표, 격자 좌표간의 좌표 변환 클래스를 구현하여 해결
* [x] 현재 위치 정보를 가져오는 과정에서 디바이스 언어에 따라 지역명 표기 언어가 달라지는 현상
    *  CLGeocoder의 reverseGeocodeLocation(_:preferredLocale:completionHandler:) 메서드를 사용하여 해결
* [x] 앱 실행에 앞서 Cellular 또는 WIFI 연결 상태를 확인하는 작업이 필요
    * SystemConfiguration, Reachability 관련 오픈 소스를 참고하여 해결하였으나 충분한 이해와 공부가 필요함


&nbsp;
### 데이터 출처
* 기상청(동네예보 정보)
* 한국환경공단(대기오염 정보, 측정소 정보)

&nbsp;
## 리팩토링
### 리팩토링을 계획한 이유
그동안 프로젝트에 참여하면서 가장 우선시했던 것은 완성이었다. 하지만 네이버 테크 밋업, 부스트코스 등을 참여하며 만났던 선배 개발자분들의 조언은 단순한 복사 붙여 넣기 보단 원리를 알고 공부를 하는 것, 그리고 왜 그렇게 코드를 만들었고 더 나은 방법은 없는지에 대해 많은 생각을 해보는 것. 즉, '어떻게'보다는 '왜' 그 코드를 사용했는지가 중요하다는 것이었다.


그리고 네이버 커넥트재단의 부스트코스 에이스 과정에 참여를 하며 새로운 기술과 세련된 코드를 배웠다. 따라서 배운 것들을 적용하여 해당 프로젝트를 좀 더 나은 코드로 개선하고 구현한 기능들에 대한 명확한 이해를 해보자는 목표로 리팩토링을 계획하였다.

&nbsp;
### 리팩토링 목표
리팩토링 진행에 앞서 다음과 같은 목표를 세웠다. 공부한 내용들은 [블로그](https://0jun0815.github.io)에 정리해둘 것이다. 

* [x] 코드 간결화 및 기능의 세분화
* [x] 프로토콜 지향 프로그래밍
* [x] CoreData에 충분한 이해 ([https://0jun0815.github.io/core-data](https://0jun0815.github.io/core-data))
* [ ] Alamofire의 장단점 및 코드 분석
* [x] Instrument 사용해보기
* [ ] UnitTest 사용해보기
