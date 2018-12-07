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
- 이 정규식은 두번 일치하지만 진짜 `<br>`태그는 하나 뿐입니다. 다른 하나는 글자 데이터(CDATA)입니다. 
- 정규식은 `<p>`태그 안에 `<a>`태그가 존재하는 것 같은 계층적 구조에 매우 취약합니다.

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
| \d | [0-9] | |
| \D | [^0-9] | |
| \s | [ \t\v\n\r] | 탭, 스페이스, 세로탭, 줄바꿈이 포합됩니다.|
| \S | [^ \t\v\n\r] | |
| \w | [a-zA-Z_] | 하이픈과 마침표는 포함되지 않으므로 이 문자셋으로 도메인 이름이나 CSS클래스 등을 찾을 수는 없습니다. |
| \W | [^a-zA-Z_] | |

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
비슷한 예로 required 필드 (공백이 아닌 글자가 최소한 하나는 있어야 하는 필드)에 데이터가 있는지 검사할 때 종종 \S를 쓰곤 합니다.
```
    const field = ' something ';
    const valid = /\S/.test(field);
```

## 17.10 반복
반복 메타 문자는 얼마나 많이 일치해야 하는지 지정할 때 씁니다. 숫자 여러 개를 찾아야 한다면 어떨까요? 이미 알고 있는 방법을 사용해서 다음과 같이 찾을 수 있습니다.
```
    const match = beer99.match(/[0-9][0-9][0-9]|[0-9][0-9]|[0-9]/);
```
이번에도 두자리 숫자가 숫자를 소비해서 세 자리 숫자를 찾지 못하는 일이 없도록 세 자리 숫자를 먼저 썼습니다.
이 정규식은 한 자리 ~ 세 자리 숫자에는 잘 동작하지만 네 자리 숫자가 필요하다면 정규식을 고쳐야 하겠죠. 위의 정규식을 아래와 같이 고쳐봅시다. 
```
    const match = beer99.match(/[0-9]+/);
```
문자셋 다음에 오는 +는 그 앞의 요소가 하나 이상 있어야 한다는 뜻입니다. 반복 메타 문자는 그 자체로는 아무런 의미도 가지지 못합니다.

- 반복 메타 문자에는 다섯 가지 종류가 있습니다.

| 반복 메타 문자 | 설명 | 예제 |
| :--------: | :-------- | :-------- |
| {n} | 정확히 n개 | /\d{5}/는 새 우편번호처럼 정확히 다섯 자리 숫자에만 일치합니다. |
| {n, } | 최소한 n개 |  /\d{5, }/는 다섯 자리 이상의 숫자에만 일치합니다. |
| {n,m} | n개 이상, m개 이하 |  /\d{2,5}/는 2개~5개에 일치 합니다. |
| ? | 0개 또는 1개. {0,1}과 동등 |  /[a-z]\d?/i는 글자가 있고 그 다음에 숫자가 없거나 한개 있는 경우에 일치 합니다. |
| * | 숫자는 상관없으며 없어도 됨.  (클레이니 스타, 클레이니 클로저라고 부르기도 함) | /[a-z]\d*/i는 글자가 있고 그 다음에 숫자가 없거나 있는 경우에 일치. |
| + | 하나이상 | /[a-z]/d+i는 글자가 있고 그 다음에 숫자가 한개 이상 있는 경우에 일치 |

## 17.11 마침표와 이스케이프
정규식에서 마침표는 줄바꿈 문자를 제외한 모든 문자에 일치하는 특수 문자입니다. 이 메타 문자는 **입력이 어떤 문자이든 상관하지 않고 소비하려 할 때 주로 사용**합니다. 문자열에서 우편번호만 필요하고 다른 것은 아무것도 필요하지 않다고 해봅시다.
```
    const input = "Address: 333 Main St., Anywhere, NY, 55532. Phone: 555-555-2525.";
    const match = input.match(/\d{5}.*/);
```
간혹 아스테리스크, 괄호 및 마침표처럼 정규식 메타 문자를 글자 그대로 찾아야 할 때가 있습니다. 이럴 땐 정규식 특수 문자를 이스케이프해서 일반 문자로 사용하려면 그 앞에 역슬래시를 붙이면 됩니다.
```
    const equation = "(2 + 3.5) * 7";
    const match = equation.match(/\(\d \+ \d\.\d\) \* \d/);
```
> 우리가 흔히 알고 있는 와이드카드(\*)의 의미는 정규식에서는 완전히 다른 의미를 가지고 있습니다. 또한 단독으로 쓸 수도 없습니다. 파일 이름에 쓰이는 와일드카드는 정규식의 마침표 메타 문자에 더 가깝습니다. 정규식에서는 단 한 글자에만 일치한다는 점을 제외하면 말입니다.

## 17.11.1 진정한 와일드카드
마침표가 줄바꿈을 제외한 모든 문자에 일치한다면, 줄바꿈 문자를 포함해서 모든 문자에 일치하는 것은 어떻게 써야 할까요? 
- 생각보다 더 자주 필요한 작업입니다. 방법은 다양하지만 가장 널리 쓰이는 것은 [\s\S]입니다. 
- 이것은 공백인 모든 문자에 일치하는 동시에, 공백이 아닌 모든 문자에 일치합니다.

## 17.12 그룹
- 그룹을 사용하면 하위 표현식을 만들고 단위 하나로 취급할 수 있습니다. 또한, 그 그룹에 일치하는 결과를 나중에 쓸 수 있도록 캡쳐할 수도 있습니다. 
- 결과를 캡쳐하는 것이 기본값이지만 캡쳐하지 않은 그룹도 만들 수 있습니다. 저자는 기본적으로 캡쳐하지 않는 그룹을 사용하길 권장하는데 그 이유로 캡쳐하지 않는 그룹을 사용하면 성능상 장점이 있습니다. 또한 이치하지 않는 결과를 나중에 쓸 필요가 없는데 굳이 캡쳐할 필요가 없습니다.
- 그룹은 괄호로 만듭니다. 캡쳐하지 않는 그룹은 ?:[subexpression]형태이고 여기서 [subexpression]이 일치시키려 하는 패턴입니다. 아래의 예제는 도메인 이름을 찾으려 하지만 .com, .org, .edu만 찾는다고 합니다.
```
    const text = "Visit oreilly.com today!";
    const match = text.match(/[a-z]+(?:\.com|\.org|\.edu)/i);
```
그룹에도 반복을 저용할 수 있습니다. 일반적으로 반복은 반복 메타 문자의 바로 왼쪽에 있는 문자 하나에 적용되지만, 그룹을 사용하면 그룹 전체에 반복을 적용합니다. http://, https://, //로 시작하는 URL을 찾으려 한다면 그룹과 함께 0 또는 1개에 일치하는 메타 문자 ?를 쓰면 됩니다.

```
    const html = '<link rel="stylesheet" href="http://insecure.com/stuff.css">\n' +
     '<link rel="stylesheet" href="https://secure.com/securestuff.css">\n' +
     '<link rel="stylesheet" href="//anything.com/flexible.css">';
    const matches = html.match(/(?:https?)?\/\/[a-z][a-z0-9-]+[a-z0-9]+/ig);
```
정규식의 시작에는 캡쳐하지 않은 그룹 (?:https?)?가 있습니다.
여기에는 0 또는 1 메타 문자(?)가 두개 있습니다. 그 중 처음은 ‘s는 옵션이다’라는 뜻입니다. 일반적으로 반복은 메타 문자의 바로 왼쪽에 있는 문자 하나에 적용됩니다. 두번째는 그 왼쪽에 있는 그룹 전체에 적용됩니다. 따라서 이 패턴은 빈 문자열, http, https에 일치합니다. 이 다음에는 이스케이프한 슬래시 두 개 (\/\/)가 있습니다.

도메인 이름에는 글자와 숫자, 하이픈이 들어갈 수 있지만 시작은 글자여야 하고 하이픈으로 끝날 수는 없습니다.
이 예제가 완벽하지 않다는 것을 참고 하십시오.

## 17.13 소극적 일치, 적극적 일치
정규식은 기본적으로 적극적입니다. 검색을 멈추기 전에 일치하는 것을 최대한 많이 찾으려고 한다는 뜻입니다.
예를 들어 HTML 텍스트에서 `<i>` 태그를 `<strong>` 태그로 바꿔야 한다고 합시다. 우선 이렇게 해 볼 수 있습니다.
```
    const input = "Regex pros know the difference between\n" +
     "<i>greedy</i> and <i>lazy</i> matching.";
    
    input.replace(/<i>(.*)<\/i>/ig, '<strong>$1</strong>');

    // console
    "Regex pros know the difference between
    <strong>greedy</i> and <i>lazy</strong> matching."
```
- 교체 문자열에 있는 $1은 .* 그룹에 일치하는 문자열로 바뀝니다.

위 예제의 콘솔 부분을 살펴보면 우리가 작성한 정규식은 우리가 의도하지 않은 결과를 나타냅니다. 정규식은 일치할 가능성이 있는 동안 문자를 소비하지 않고 계속 넘어갑니다. 그리고 그 과정을 적극적으로 진행합니다. `<i>`를 만나면 `</i>`를 더는 찾을 수 없을 때까지 소비하지 않고 진행합니다. 원래 문자열에는 `</i>`가 두 개 있으므로 첫번째 것을 무시하고 두 번째 것에서 일치한다고 판단합니다.

- 정규식이 우리가 의도한 대로 실행되게 하기 위해 메타 문자 \*를 소극적으로 바꾸는 방법을 쓰겠습니다.
```
    input.replace(/<i>(.*?)<\/i>/ig, '<strong>$1</strong>');

    // console
    "Regex pros know the difference between
    <strong>greedy</strong> and <strong>lazy</strong> matching."
```
반복 메타 문자 \*뒤에 ?를 넣었습니다. 이제 정규식 엔진은 `</i>`를 보는 즉시 일치하는 것을 찾았다고 판단합니다. 따라서 `</i>`를 발견할 때마다 그때까지 찾은 것을 소비하고, 일치하는 범위를 넓히려 하지 않습니다. 그렇게 우리가 원하는 것에 딱 맞게 동작합니다.

## 17.14 역참조
그룹을 사용하면 역참조라는 테크닉도 쓸 수 있습니다. XYYX 형태의 밴드 이름을 찾고 싶다고 합시다. PJJP, ANNA, GOOG 등이 해당되겠죠. 역참조를 이러한 경우에 유용하게 쓸 수 있습니다. 서브 그룹을 포함해 정규식의 각 그룹은 숫자를 할당 받습니다. 맨 왼쪽이 1번에서 시작해 오른쪽으로 갈수록 1씩 늘어납니다. 역슬래시 뒤에 숫자를 써서 이 그룹을 참조할 수 있습니다.
```
    const promo = "Opening for XAAX is the dynamic GOOG! At the box office now!";
    const bands = promo.match(/(?:[A-Z])(?:[A-Z])\2\1/g);
```
이 정규식을 왼쪽에서 오른쪽으로 읽으면 그룹이 두 개있고, 그 다음에 \2\1이 있습니다. 첫번째 그룹이 X에 일치하고 두 번째 그룹이 A에 일치한다면 \2는 A이고 \1은 X입니다.
HTML에서는 태그의 속성값에 큰 따옴표나 작은 따옴표를 써야 합니다. 역참조를 이용하면 쉽게 찾을 수 있습니다.
```
    //작은따옴표와 큰따옴표를 모두 썼으므로 백틱으로 문자열 경계를 나타냈습니다.
    const html = `<img alt='A "simple" example.'>` +
     `<img alt="Don't abuse it!">`;
    const matches = html.match(/<img alt=(?:['"]).*?\1/g);
```
밴드 이름 예제와 마찬가지로 첫 번째 그룹은 따옴표 뒤에 0개 이상의 문자를 찾습니다. 그 다음에 있는 \1은 앞에서 찾은 따옴표의 짝입니다.

## 17.15 그룹 교체
그룹을 사용하면 문자열 교체도 더 다양한 방법으로 할 수 있습니다. 이번에도 HTML을 예로 들겠습니다. `<a>`태그에서 href가 아닌 속성을 전부 제거하고 싶다고 합시다.

```
    let html = '<a class="nope" href="/yep">Yep</a>';
    html = html.replace(/<a .*?(href=".*?").*?>/, '<a $1>');
```
역참조와 마찬가지로 모든 그룹은 1로 시작하는 숫자를 할당받습니다. 정규식에서 첫 번째 그룹은 \1이고, 교체할 문자열에서는 $1이 첫번째 그룹에 해당합니다. 이번에도 소극적 일치를 써서 다른 `<a>`태그까지 검색이 확장되는 일을 막았습니다.
이 정규식은 href속성의 값에 큰 따옴표가 아닌 작은 따옴표를 쓴 문자열에서는 아무것도 찾지 못합니다. 이번에 class속성과 href속성을 남기고 나머지는 모두 없애고 싶습니다.
```
    let html = '<a class="yep" href="/yep" id="nope">Yep</a>';
    html = html.replace(/<a .*?(class=".*?").*?(href=".*?").*?>/, '<a $2 $1>');
```
이 정규식에서는 class와 href의 순서를 바꾸므로 결과 문자열에서는 class 보다 href가 앞에옵니다. 이정규식 역시 위에서 살펴본 정규식과 같이 속성 값에 작은 따옴표를 쓰면 동작하지 않습니다. 또한 $1, $2 등 숫자로 참조하는 것 외에도 일치하는 것 앞에 있는 전부를 참조하는 $\`, 일치하는 것 자체인 $&, 일치하는 것 뒤에 있는 것 뒤에 있는 전부를 참조하는 $’도 있습니다. $기호 자체가 필요한 경우에는 $$를 씁니다.
```
    const input = "One two three";
    input.replace(/two/, '($`)'); // "One (One ) three"
    input.replace(/\w+/g, '($&)'); 
    input.replace(/two/, "($')"); // "One ( three) three"
    input.replace(/two/, "($$)"); 
```
## 17.16 함수를 이용한 교체
함수를 이용하면 아주 복잡한 정규식을 좀 더 단순한 정규식으로 분할할 수 있습니다. 
`<a>`태그를 정확한 규격에 맞도록 바꾸는 프로그램을 만들겁니다. 이 규격은 class, id, href 속성은 허용하지만 나머지 속성은 제거합니다. 문제는 원래 HTML이 어떻게 만들어져 있는지 짐작을 할 수 없을 뿐더러 허용하는 속성이 있다는 보장도 없고, 있더라도 순서가 뒤죽박죽일 수 있습니다.
```
    const html =
     `<a class="foo" href="/foo" id="foo">Foo</a>\n` +
     `<A href='/foo' Class="foo">Foo</a>\n` +
     `<a href="/foo">Foo</a>\n` +
     `<a onclick="javascript:alert('foo!')" href="/foo">Foo</a>`;
```
- 정규식 여러 개와 String.prototype.split을 써서 한 번에 한 가지 숙성만 체크하는 방법을 사용하겠습니다.
```
    function sanitizeATag(aTag) {
        // 태그에서 원하는 부분을 뽑아냅니다.
        const parts = aTag.match(/<a\s+(.*?)>(.*?)<\/a>/i);
        // parts[1]은 여는 <a> 태그에 들어있는 속성입니다.
        // parts[2]은 <a> 와 </a> 태그 사이에 들어있는 텍스트입니다. 
        const attributes = parts[1]
        // 속성분해
        .split(/\s+/);
        return '<a ' + attributes
        // class, id, href 속성 추출
        .filter(attr => /^(?:class|id|href)[\s=]/i.test(attr))
        .join(' ') + '>' + parts[2] + '</a>';
    }
```
위 sanitizeATag 함수는 `<a>` 태그가 들어있는 HTML 블록에 사용하려고 만들었습니다.
`<a>`태그를 찾는 정규식은 아주 쉽게 만들수 있습니다.
```
    html.match(/<a .*?>(.*?)<\/a>/ig);
```
그런데 이걸 어떻게 써야 할까요? String.prototype.replace에는 교체할 매개변수로 함수를 넘길 수 있습니다. 지금까지는 교체할 매개변수에 문자열만 썼지만 함수를 사용하면 훨씬 자유롭게 바꿀 수 있습니다. 예제를 완성하기 전에 console.log를 써서 어떻게 돌아가는지 알아보도록 합니다. 
```
    html.replace(/<a .*?>(.*?)<\/a>/ig, function(m, g1, offset) {
     console.log(`<a> tag found at ${offset}. contents: ${g1}`);
    });

    //console
    `<a>` tag found at 0.
    `<a>` tag found at 44.
    `<a>` tag found at 79.
    `<a>` tag found at 102.
```
String.prototyp.replace에 넘기는 함수는 다음 순서대로 매개변수를 받습니다.
- m: 일치하는 문자열 전체 ($&와 같음)
- g1: 일치하는 그룹(일치하는 것이 있다면). 일치하는 것이 여럿이라면 매개변수도 여러 개를 받음
- offset: 원래 문자열에서 일치하는 곳의 오프셋(숫자)
- 원래 문자열(거의 사용하지 않음)
이제 원래 예제로 돌아가봅시다. 각 `<a>` 태그를 규격화 하는 함수를 만들었고 HTML 블록에서 `<a>` 태그를 찾는 방법도 알고 있으니 그 둘을 합치기만 하면 됩니다.
```
    html.replace(/<a .*?<\/a>/ig, function(m) {
     return sanitizeATag(m);
    });
```
위 태그를 더 간단하게 표기 할 수도 있습니다.
```
    html.replace(/<a .*?<\/a>/ig, sanitizeATag);
```

## 17.17 위치 지정
문자열을 다루다보면 ‘와와로 시작되는 문자열’, ‘뫄뫄로 끝나는 문자열’, ‘그 문자열의 처음’, ‘그 문자열의 끝’과 같은 식으로 위치를 지정하여 문자열을 찾고 싶을 때가 있습니다. 여기서 ‘와와’와 ‘뫄뫄’를 정규식의 앵커라고 부릅니다. 앵커는 캐럿(\^)과 달러($) 두 가지 종류가 있습니다. \^는 문자열의 맨 처음을 나타내고, $는 문자열의 맨 끝을 나타냅니다.
```
    const input = "It was the best of times, it was the worst of times";
    const beginning = input.match(/^\w+/g); // "It"
    const end = input.match(/\w+$/g); // "times"
    const everything = input.match(/^.*$/g); // sames as input
    const nomatch1 = input.match(/^best/ig); // null
    const nomatch2 = input.match(/worst$/ig); // null
```
이 외에도 앵커에는 특이한 기능이 있습니다. 문자열에 줄바꿈 문자가 들어있다면 각 줄의 처음과 끝을 찾을 수 있습니다. 각 줄의 끝과 처음을 찾으려면 m 플래그를 함께 쓰면 됩니다.
```
    const input = "One line\nTwo lines\nThree lines\nFour";
    const beginnings = input.match(/^\w+/mg); // ["One", "Two", "Three", "Four"]
    const endings = input.match(/\w+$/mg); // ["line", "lines", "lines", "Four"]
```
## 17.18 단어 경계 일치
단어 경계 일치는 매우 유용한 기능입니다. /b와 /B는 앵커와 마찬가지로 입력을 소비하지 않습니다. 이는 매우 유용한 특징입니다. 단어 경계는 알파벳 또는 숫자(/w)로 시작하는 부분, 알파벳이나 숫자가 아닌 문자(\W)로 끝나는 부분, 또는 문자열의 시작이나 끝에 일치합니다. 영어 텍스트 안에 들어있는 이메일 주소를 찾아서 하이퍼링크로 바꾼다고 합시다. 
```
    const inputs = [
        "john@doe.com", // 이메일 주소만 있습니다.
        "john@doe.com is my email", // 이메일 주소로 시작
        "my email is john@doe.com", // 이메일 주소로 끝남
        "use john@doe.com, my email", // 이메일 주소가 중간에 있고 바로 뒤에 쉼표가 있음
        "my email:john@doe.com.", // 이메일 주소 주위에 구두점이 있음
    ];
```
이들 이메일 주소의 공통점은 단어 경계 사이에 있다는 것입니다. 단어 경계는 입력을 소비하지 않으므로, 다시 말해 일치하는 이메일 주소인 john@doe.com에서 j가 보존되고 m다음의 문자 역시 보존되므로 교체할 문자열에서 ‘다시 넣는’ 방법을 생각할 필요가 없나는 것입니다.
```
    const emailMatcher = /\b[a-z][a-z0-9._-]*@[a-z][a-z0-9_-]+\.[a-z]+(?:\.[a-z]+)?\b/ig;
    
    inputs.map(s => s.replace(emailMatcher, '<a href="mailto:$&">$&</a>'));
    
    // returns [
    // "<a href="mailto:john@doe.com">john@doe.com</a>",
    // "<a href="mailto:john@doe.com">john@doe.com</a> is my email",
    // "my email is <a href="mailto:john@doe.com">john@doe.com</a>",
    // "use <a href="mailto:john@doe.com">john@doe.com</a>, my email",
    // "my email:<a href="mailto:john@doe.com>john@doe.com</a>.",
    // ]
```
단어 경계는 특정 단어로 시작하거나 특정 단어로 끝나거나, 특정 단어를 포함하는 텍스트를 찾을 때도 유용합니다. 예를 들어 /\bcount는 count와 countdown을 찾지만 discount, recount, accountable은 찾지 못합니다. /\bcount\b/는 discount recout같은 단어만 찾을 수 있고  /\Bcout\B/는  accountable과 같은 단어만 찾을 수 있습니다.
## 17.19 룩어헤드
룩어헤드는 앵커나 단어 경계와 마찬가지로 입력을 소비하지 않습니다. 룩어헤드는 하위 표현식도 소비하지 않고 찾을 수 있으므로, 앵커와 단어 경계보다 범용적으로 쓸 수 있습니다. 단어 경계에서 ‘다시 넣는’ 방법을 고민할 필요가 없는 특징 역시 룩어헤드에도 적용됩니다. 룩어헤드는 문자열이 겹치는 상황에 필요하고, 룩어헤드는 써서 단순화 시킬 수 있는 패턴이 많습니다.

룩어헤드를 설명할 때 주로 사용하는 예제는 비밀번호가 규격에 맞는지 검사하는 겁니다. 룩어헤드로 비밀번호를 검사한다고 가정해봅시다. 비밀번호에는 대문자와 소문자, 숫자가 최소한 하나씩 포함되어야 합니다. 또 글자도 아니고 숫자도 아닌 문자는 들어갈 수 없습니다. 
```
    function validPassword(p) {
        return /[A-Z]/.test(p) && // 대문자가 최소 하나 
        /[0-9]/.test(p) && // 숫자가 최소 하나
        /[a-z]/.test(p) && // 소문자가 최소 하나
        !/[^a-zA-Z0-9]/.test(p); // 영문자와 숫자만 허용
    }
```
이 정규식을 하나로 묶으려면 어떻게 해야 할까요? 아래와 같이 작성하면 됩니다.
```
    function validPassword(p) {
        return /[A-Z].*[0-9][a-z]/.test(p);
    }
```
자바스크립트의 룩어헤드는 (?=[subexpression]) 형태입니다. 하위 표현식 뒤에 이어지지 않는 것만 찾는 부정형 룩어헤드(?![subexpresstion])도 있습니다. 룩어헤드를 사용하면 정규식 하나로 비밀번호의 유효성을 검사할 수 있지요.
```
    function validPassword(p) {
        return /(?=.*[A-Z])(?=.*[0-9])(?=.*[a-z])(?!.*[^a-zA-Z0-9])/.test(p);
    }
```
## 17.20 동적으로 정규식 만들기
정규식을 사용할 때는 일반적으로 RegExp 생성자보다 리터럴을 쓰는 편이 좋습니다. 정규식 리터럴을 쓰면 타이핑하는 수고도 덜 수 있고, 자바스크립트에서 으레 하는 역슬래시를 사용한 이스케이프도 줄어들기 때문지요. 하지만 RegExp가 필요할 때도 있는데 동적으로 정규식을 만들어야 할 때가 그런 경우입니다.
```
    const users = ["mary", "nick", "arthur", "sam", "yvette"];
    const text = "User @arthur started the backup and 15:15, " + "and @nick and @yvette restored it at 18:35.";
    const userRegex = new RegExp(`@(?:${users.join('|')})\\b`, 'g');
    text.match(userRegex); // [ "@arthur", "@nick", "@yvette" ]
```
이 예제를 리터럴 방식으로 표기하면 /@(?:mary|nick|arthur|sam|yvette)\b/g일 겁니다. 
동적으로 정규식을 만들경우에는 역슬래시 두개를 써서 리터럴 문자 b로 인식 되는 것을 방지 하였습니다.

## 17.21 요약
- 이 장에서는 정규식의 요점만 간단히 짚어봤습니다.
- 정규식을 능숙하게 사용하려면 이론 이해 20% 나머지 80%는 연습으로 채워야 합니다. 
- [정규식101](https://regex101.com/#javascript) 같은 테스터를 사용하면 큰 도움이 됩니다.
- 이 장에서 가장 핵심은 정규식이 입력을 어떻게 소비하는지 입니다.
