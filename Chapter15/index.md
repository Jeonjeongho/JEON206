# chapter15 : 날짜와 시간
* 자바스크립트의 Date 객체는 잘 설계된 편이 아님
* 객체의 기능이 불충분 
* Moment.js => 날짜와 시간에 관련된 널리 쓰이는 기능을 대부분 제공

## 15.1 날짜, 타임존, 타임스탬프, 유닉스 시간
모두 유닉스 시간 원점으로부터 몇 밀리초가 지났는지 나타내는 숫자 

* 자바스크립트는 타임존이 들어간 [그레고리력](https://goo.gl/yvkdko) 날짜로 출력함
* 숫자형 표현이 필요한 경우 valueOf() 메서드 사용


## 15.2 Date 객체 만들기
네 가지 방법으로 객체를 생성할 수 있음 
```
new Date(); // 현재 날짜
​
// 자바스크립트의 월은 0으로 시작 (0 - 1월, 1 - 2월)
new Date(2018, 0); // 2018년 1월 1일 0시
new Date(2018, 1); // 2018년 2월 1일 0시
new Date(2018, 1, 14); // 2018년 2월 14일 0시
new Date(2018, 1, 14, 13); // 2018년 2월 14일 오후 1시
new Date(2018, 1, 14, 13, 30); // 2018년 2월 14일 오후 1시 30분
new Date(2018, 1, 14, 13, 30, 5); // 2018년 2월 14일 오후 1시 30분 5초
new Date(2018, 1, 14, 13, 30, 5, 500); // 2018년 2월 14일 오후 1시 30분 5.5초 
​
// 유닉스 타임스탬프
new Date(0); // 12:00 A.M., Jan 1, 1970 UTC
​
// 유닉스 시간 원점 이전의 날짜를 구할 때
new Date(-365*24*60*60*1000); // 12:00 A.M., Jan 1, 1969 UTC
​
// 날짜 문자열 해석 (표준시를 기준으로 함)
new Date('June 14, 1903'); // 12:00 A.M., Jun 14, 1903 지역 표준시
new Date('June 14, 1903 GMT-0000'); // 12:00 A.M., Jun 14, 1903 UTC
```

## 15.3 Moment.js

타임존을 지원하는 버전, 지원하지 않는 버전 두 가지 지원

## 15.4 현실적인 자바스크립트 날짜 접근법
Date 객체의 메서드에 관한 레퍼런스가 필요하다면 MDN 문서(https://goo.gl/DaGfQ)에서 상세한 설명을 읽을 수 있음

## 15.5 날짜 데이터 만들기
보통은 자바스크립트의 Date 객체로 충분하지만 타임존을 명시해서 생성할 때는 고려해야할 것이 많음

## 15.5.1 서버에서 날짜 생성하기
서버에 날짜를 생성하는 경우 => 항상 UTC 사용 또는 타임존 명시
```
const d = new Date(Date, UTC(2018, 4, 27)); // May 27, 2018 UTC
```
특정 타임존에 있는 서버에서 날짜를 생성하는 경우 => moment.tz
```
// Moment.js에 넘기는 배열은 자바스크립트의 Date 생성자에 넘기는 매개변수와 똑같고, 월은 0으로 시작한다.
// toDate() 메서드는 Moment.js 객체를 자바스크립트 Date 객체로 변환한다.
const d = moment.tz([2018, 3, 27, 9, 19], 'America/Los_Angeles').toDate();
const s = moment.tz([2018, 3, 27, 9, 19], 'Asia/Seoul').toDate();
```

## 15.5.2 브라우저에서 날짜 생성하기
* 브라우저는 운영체제를 통해 타임존 정보를 알 수 있음
* 앱에서 다른 타임존의 날짜를 처리해야하는 경우 Moment.js 이용

## 15.6 날짜 데이터 전송하기
* 자바스크립트의 Date 인스턴스는 날짜를 저장할 때 UTC 기준으로 유닉스 타임스탬프를 저장
* 자바스크립트에서 날짜를 전송하는 가장 확실한 방법 => JSON
```
const before = { d: new Date() };
before.d instanceof Date // true
const json = JSON.stringify(before);
const after = JSON.parse(json);
after.d instanceof Date // false
typeof after.d // "string"
​
after.d = new Date(after.d); // 사용자의 타임존 기준으로 표시
after.d instanceof Data // true
```

## 15.7날짜 형식
* 자바스크립트 Date 객체는 제공하는 날짜 형식이 다양하지 않음 
* Moment.js는 원하는 형식을 쉽게 만들 수 있음
* Moment.js의 format 메서드는 메타 문자가 포함된 문자열을 받고, 메타 문자는 그에 해당하는 날짜 구성 요소를 반환 함


## 15.8 날짜 구성 요소
Date 인스턴스의 각 구성 요소에 접근할 때는 다음 메서드를 사용한다.
```
const d = new Date(Date.UTC(1815, 9, 10));
​
// 로스엔젤리스 기준
d.getFullYear(); // 1815
d.getMonth(); // 9 - 10월
d.getDate(); // 9
d.getDay(); // 1 - 월요일
d.getHours(); // 17
d.getMinutes(); // 0
d.getSeconds(); // 0
d.getMilliseconds(); // 0
​
..etc
```
Moment.js를 사용한다면 날짜의 각 부분에 대해 신경 쓸 필요가 별로 없긴 하지만, 알아둬서 나쁠 것은 없다.

## 15.9 날짜 비교
비교 연산자를 이용해 어떤 날짜가 더 앞인지 단순 비교 가능
숫자에 쓸 수 있는 비교 연산자를 그대로 쓰면 됨
```
const d1 = new Date(1996, 2, 1);
const d2 = new Date(2009, 4, 27);
​
d1 > d2 // false
d1 < d2 // true
```

## 15.10 날짜 연산
날짜는 숫자이므로 날짜에서 날짜를 뺴면 몇 밀리초가 지났는지 알 수 있다.

빼기
```
const msDiff = d2 - d1;
const daysDiff = msDiff/1000/60/60/24
```
Array.prototype.sort를 써서 날짜를 정렬할 수 있다.

정렬
```
const dates = [];
​
// 랜덤으로 날짜 생성
const min = new Date(2017, 0, 1).valueOf();
const delta = new Date(2020, 0, 1).valueOf() - min;
​
for (let i=0; i<10; i++)
    dates.push(new Date(min + delta*Math.random()));

// 다음과 같이 역순으로 정렬 할 수 있음
dates.sort((a, b) => b - a);
// 날짜순으로 정렬할 수도 있음
dates.sort((a, b) => a - b);
```

## 15.11 사용자가 알기 쉬운 상대적 날짜
‘3일 전’처럼 날짜를 상대적으로 표시하면 알기 쉽다. Moment.js에서 쉽게 할 수 있다.
```
moment().subtract(10, 'seconds').fromNow();       // a few seconds ago
moment().subtract(44, 'seconds').fromNow();       // a few seconds ago
moment().subtract(45, 'seconds').fromNow();       // a minute ago
moment().subtract(5, 'minutes').fromNow();       // 5 minutes ago
moment().subtract(44, 'minutes').fromNow();       // 44 minutes ago
moment().subtract(45, 'minutes').fromNow();       // an hour ago
moment().subtract(5, 'hours').fromNow(); // 4 hours ago
moment().subtract(21, 'hours').fromNow();         // 21 hours ago
moment().subtract(22, 'hours').fromNow();         // a day ago
moment().subtract(300, 'days').fromNow();         // 10 months ago
moment().subtract(345, 'days').fromNow();         // a year ago
```
Moment.js 에서는 적당한 시간 단위가 지나면 다른 단위를 써서 나타낸다.

## 15.12 요약
 * 자바스크립트의 날짜는 1970년 1월 1일 UTC로부터 몇 밀리초가 지났는지 나타내는 숫자
 * 날짜를 생성할 때는 타임존에 유의
 * 날짜 형식을 자유롭게 바꿀 수 있어야 한다면 Moment.js를 사용