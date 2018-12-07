# CHAPTER 17 정규 표현식
- 정규표현식은 정교한 문자열 매칭 기능을 제공합니다.
- 정규표현식은 문자열 교체 기능도 들어있습니다.
- 정규표현식은 간단히 정규식으로 부르기도 합니다. (이하 정규식으로 표기)

## 17.1 부분 문자열 검색과 대체
- 정규식으로 하는 일은 문자열 속에서 부분 문자열을 찾고 교체하는 일을 합니다.
- 정규식을 쓰지 않고 String.prototype 메서드로도 문자열 검색과 교체 기능의 한계가 있지만, 한계내에서는 가능합니다.
- 자바스크립트의 문자열은 항상 불변입니다.
 
### String.prototype 메서드로 문자열 검색
```
    const input = "As I was going to Saint Ives";

    input.startsWith("As") // true
    input.endsWith("Ives") // true
    input.startsWith("going", 9) // true --> 인덱스 9에서 시작
    input.endsWith("going", 14) // true --> 인덱스 14를 끝으로 간주합니다.
    input.includes("going") // true
    input.includes("going", 10) // false --> 인덱스 10에서 시작하면 "going"이 없습니다.
    input.indexOf("going") // 9
    input.indexOf("going", 10) // -1
    input.indexOf("nope") // -1
    input.startsWith("as") //false --> 대소문자 구분을 합니다.
    input.toLowerCase().startsWith("as") //true
```
### String.prototype 메서드로 문자열 교체
```
    const input = "As I was going to Saint Ives";
    const output = input.replace("going", "walking");
``` 
## 17.2 정규식 만들기
- 정규식은 두가지 방법으로 만들 수 있습니다.
- 리터럴 방식과 생성자 함수를 사용해서 동적으로 만드는 방법입니다.
- 정규식 패턴이 변경되는 특별한 케이스를 제외하면 리터럴 방법을 사용하는 것이 좋습니다.

### 정규식 패턴이 계속 지속되는 경우
```
    const re1 = /going/; // 리터럴 방식
```
### 정규식 패턴이 변경 되는 경우
```
    const re2 = new RegExp("going"); // 생성자 함수
```

## 17.3 정규식 검색
정규식이 만들어지면 다양한 옵션으로 문자열을 검색할 수 있습니다. 예제로 사용할 정규식은 /\w{3,}/ig 입니다. 세글자 이상인 단어에 모두 일치하고 대소문자는 가리지 않습니다. 당장 이해되지 않아도 괜찮습니다. 먼저 검색하는 방법을 알아봅시다.
```
    const input = "As I was going to Saint Ives";
    const re = /\w{3,}/ig;
```

### 문자열(input) 메서드를 사용할 때
```
    input.match(re); // ["was", "going", "Saint", "Ives"]
    input.search(re); // 5 (세글자 이상으로 이루어진 첫 단어의 인덱스는 5)
```

### 정규식(re)의 메서드를 사용할 때
```
    re.test(input); // true (input에는 세글자 이상으로 이루어진 단어가 1개 이상 있습니다.)
    re.exec(input); // ["was"] (처음 일치하는 것)
    re.exec(input); // ["going"] (exec는 마지막 위치를 기억)
    re.exec(input); // ["Saint"]
    re.exec(input); // ["Ives"]
    re.exec(input); // null -- no more matches
```

위 예제 모두 정규식 리터럴을 그대로 사용해도 됩니다.
```
    input.match(/\w{3,}/ig);
    input.search(/\w{3,}/ig);
    /\w{3,}/ig.test(input);
    /\w{3,}/ig.exec(input);
```
- RegExp.prototype.exec 가장 많은 정보를 제공하지만 자주 쓰이진 않습니다.
- 저자는 String.prototype.match 와 RegExp.prototype.test를 자주 사용합니다.

## 17.4 정규식을 사용한 문자열 교체
- String.prototype.replace 메서드에서도 정규식을 사용하여 더 많은 일을 할 수 있습니다.
- 네 글자 이상으로 이루어진 단어를 모두 교체 하겠습니다.
    ```
        const input = "As I was going to Saint Ives";
        const output = input.replace(/\w{4,}/ig, '****'); //"As I was **** to **** ****"
    ```

## 17.5 입력 소비
정규식을 '큰 문자열에서 부분 문자열을 찾는 방법' 이라고만 생각해서는 안됩니다. 좀 더 나은 개념으로 **정규식이 입력 문자열을 소비하는 패턴**이라고 생각하는 것입니다. 찾아낸 문자열은 그렇게 소비한 결과 만들어진 부산물입니다.
```
    X J A N L I O N A T U R E J X E E L N P
```
위에 나열된 정규식에서 'LION', 'ION', 'NATURE', 'EEL'을 찾는 겁니다.

### 어떻게 찾는지 알아볼까요?
- 정규식은 첫번째 글자 X에서 시작합니다. 찾으려는 단어중에서 X로 시작하는 것이 없으니 일치하는 것이 없다고 판단합니다.
- 정규식은 거기서 멈추지 않고 글자 J로 이동합니다. J로 시작하는 단어도 없으니 A로 이동합니다. 이렇게 이동하는 과정에서 정규식 엔진이 지나간 글자를 소비했다고 표현합니다.
- 이제 L에 도달했습니다. 정규식은 ‘L’? LION이 될수 있겠군! 하고 생각합니다. 그래서 **L은 일치할 가능성이 높으므로 정규식은 L을 소비하지 않습니다.** 계속해서 ‘I,O,N’을 만납니다. 일치하는 단어를 찾았으니 ‘LION’은 소비됩니다. 그런데 LION과 NATURE가 겹치는 군요. **정규식은 한 번 소비한 것은 절대 다시 보지 않습니다.**
- N을 이미 소비했으므로 정규식은 NATURE를 찾지 못합니다.
- 하지만 EEL은 찾아냅니다. 

### 이번엔 예제의 O를 X로 바꾸어 보겠습니다.
```
    X J A N L I X N A T U R E J X E E L N P
```
- 정규식이 L에 도달하여 LION에 일치할 가능성이 있다고 생각하고 L을 소비하지 않습니다. 그리고 I로 넘어가죠. 이 다음에 X를 만납니다. 
- 이 시점에서 일치하는 단어를 찾을 수 없다고 생각하고 **L로 돌아가서 L을 소비한 후 계속 진행합니다.**
- 이제는 NATURE을 찾습니다. 

### 정규식 메타 언어에 대해 살펴보기 전에 아래의 내용을 간단히 살펴봅시다.
- 문자열 왼쪽에서 오른쪽으로 진행합니다.
- 일단 소비한 글자에 다시 돌아오는 일은 없습니다.
- 한 번에 한 글자씩 움직이며 일치하는 것이 있는지 확인합니다.
- 일치하는 것을 찾으면 해당하는 글자를 한꺼번에 소비할 후 다음 글자로 진행합니다.

## 17.6 대체
HTML페이지를 문자열에 담았다고 합시다. `<a>, <area>, <link>, <script>, <source>, <meta>`외부 자원 태그를 모두 찾고 싶은 경우 대소문자가 통일 되지 않아서 <Area>, <LINK> 같은 태그도 포함되어 있을 수 있습니다. 정규식을 대체를 통해 이 문제를 해결합니다.
```
    const html = 'HTML with <a href="/one">one link</a>, and some JavaScript.' +
    '<script src="stuff.js"></script>';
    const matches = html.match(/area|a|link|script|source/ig); // area보다 a를 나중에 쓴 이유
```
- 파이프(|) : 대체를 뜻하는 메타문자
- ig : (i)대소문자를 가리지 않고, (g)전체를 검색
- 정규식은 왼쪽에서 오른쪽으로 평가되기 때문에 **반드시 작은 것보다 큰 것**을 먼저 써줘야 합니다.

## 17.7 HTML 찾기
- 정규식으로 HTML을 완벽하게 분석하는 것은 불가능 합니다.
- 무언가를 분석하려면 각 부분을 구성 요소로 완전히 분해 할 수 있어야 합니다. 
- 정규식의 한계를 인정하고 상황에 더 알맞은 방법을 찾아야 합니다.
- 정규식은 아주 간단한 언어만 분석할 수 있습니다.

```
    const html = '<br> [!CDATA[[<br>]]';
    const matches = html.match(/<br>/ig);
```
- 이 정규식은 두번 일치하지만 진짜 <br>태그는 하나 뿐입니다. 다른 하나는 글자 데이터(CDATA)입니다. 
- 정규식은 <p>태그 안에 <a>태그가 존재하는 것 같은 계층적 구조에 매우 취약합니다.

## 17.8 문자셋
문자셋은 글자 하나를 다른 것으로 대체하는 방법을 간단하게 줄인 겁니다. 예를 들어 문자열에 있는 술자를 모두 찾고 싶다고 합시다. 대체를 사용한다면 다음과 같이 할 수 있습니다.
```
    const beer99 = "99 bottles of beer on the wall " +
     "take 1 down and pass it around -- " +
     "98 bottles of beer on the wall.";
    const matches = beer99.match(/0|1|2|3|4|5|6|7|8|9/g);
```
그닥 좋은 방법은 아닙니다. 숫자가 아니라 글자를 찾아야 한다면 다시 만들어야 합니다. 숫자와 글자를 모두 찾을 때도 마찬가집니다. 숫자가 아닌 것을 모두 찾아야 할 때도요. 이런 경우에 문자셋을 사용하면 편리하게 쓸 수 있습니다.

문자셋은 이러이러한 문자들이 들어갈 수 있다는 것을 간략하게 표시할 수 있습니다. 위 예제를 아래와 같이 고쳐 씁시다.
```
    const m1 = beer99.match(/[0123456789]/g); // okay
    const m2 = beer99.match(/[0-9]/g); // better!
```
범위를 결합하는 것도 가능합니다. 아래의 정규식은 문자열에서(공백을 제외한) 모든 것들을 다 찾습니다.
```
    const match = beer99.match(/[\-0-9a-z.]/ig);
```
순서는 중요하지 않습니다. `/[.a-z0-9\-]`도 똑같이 표현됩니다. 단 하이픈은 꼭 이스케이프 해야 합니다. 이스케이프 하지 않으면 정규식은 하이픈을 범위를 표시하는 메타 문자라고 간주합니다. 닫는 대괄호 바로 앞에 쓰는 하이픈은 이스케이프 하지 않아도 됩니다. 문자셋은 특정 문자, 또는 범위를 제외하고 찾을 수도 있습니다. 문자셋을 제외할 때는 다음과 같이 캐럿(^)을 맨 앞에 쓰면 됩니다.
```
    const match = beer99.match(/[^\-0-9a-z.]/);
```
위 정규식은 원래 문자열에서 공백만 찾습니다.

## 17.9 자주 쓰는 문자셋
매우 자주 쓰이는 문자셋은 단축 표기가 따로 있습니다. 이들을 **클래스**라고 부르기도 합니다.
| 문자셋 | 동등한 표현 | 노트 |
| :--------: | :--------: | :-------- |
|\d|`[0-9]`||
|\D|`[^0-9]`||
|\s|`[ \t\v\n\r]|`|탭, 스페이스, 세로탭, 줄바꿈이 포합됩니다.|
|\S|`[^ \t\v\n\r]`||
|\w|`[a-zA-Z_] `|하이픈과 마침표는 포함되지 않으므로 이 문자셋으로 도메인 이름이나 CSS클래스 등을 찾을 수는 없습니다.|
|\W|`[^a-zA-Z_]`||

| Header 1 | Header 2 | Header 3 |
| :-------- | :--------: | --------: |
| Left | Center | Right |

위 단축 표기 중에서 가장 널리 쓰이는 것은 아마도 공백 문자셋 \s 일것입니다. 공백을 써서 줄을 맞출 때가 많습니다. 이런 파일을 프로그램으로 분석하려면 스페이스 몇 칸이 들어갔든 관계없이 필요한 내용을 찾을 수 있어야 합니다. 
```
    const stuff =
     'hight: 9\n' +
     'medium: 5\n' +
     'low: 2\n';
    const levels = stuff.match(/:\s*[0-9]/g);
```
위 정규식에서 \s 뒤에 애스터리스크(\*)는 ‘숫자는 상관없으며 없어도 된다’는 의미입니다. 그 밖에도 문자 제외 클래스 \D, \S, \W를 사용하면 원치 않는 문자들을 빠르고 효율적으로 제거 할 수 있습니다.
```
    const messyPhone = '(505) 555-1515';
    const neatPhone = messyPhone.replace(/\D/g, ''); //5055551515
```
## 17.10 반복
## 17.11 마침표와 이스케이프
## 17.11.1 진정한 와일드카드
## 17.12 그룹
## 17.13 소극적 일치, 적극적 일치
## 17.14 역참조
## 17.15 그룹 교체
## 17.16 함수를 이용한 교체
## 17.17 위치 지정
## 17.18 단어 경계 일치
## 17.19 룩어헤드
## 17.20 동적으로 정규식 만들기
```
    const users = ["mary", "nick", "arthur", "sam", "yvette"];
    const text = "User @arthur started the backup and 15:15, " + "and @nick and @yvette restored it at 18:35.";
    const userRegex = new RegExp(`@(?:${users.join('|')})\\b`, 'g');
    text.match(userRegex); // [ "@arthur", "@nick", "@yvette" ]
```
이 예제를 리터럴 방식으로 표기하면 /@(?:mary|nick|arthur|sam|yvette)\b/g,

## 17.21 요약
- 이 장에서는 정규식의 요점만 간단히 짚어봤습니다.
- 정규식을 능숙하게 사용하려면 이론 이해 20% 나머지 80%는 연습으로 채워야 합니다. 
- [정규식101](https://regex101.com/#javascript) 같은 테스터를 사용하면 큰 도움이 됩니다.
- 이 장에서 가장 핵심은 정규식이 입력을 어떻게 소비하는지 입니다.
