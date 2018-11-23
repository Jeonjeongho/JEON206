# chapter13 : 함수와 추상적 사고
## 13.1 서브루틴으로서의 함수
서브루틴
* 복잡한 코드를 간단하게 만드는 기초적인 수단
* 반복되는 작업의 일부를 떼어내서 이름을 붙이고, 언제든 그 이름을 부르기만 하면 실행됨
* 어떤 알고리즘을 나타내는 형태
* 서브루틴 = 프로시저 = 루틴 = 서브프로그램 = 매크로 = 함수

함수의 이름을 정할 때 주의해야 할 점
* 다른 사람이 함수 이름만 봐도 함수에 대해 이해 할 수 있도록 생각해서 정해야 함

## 13.2 값을 반환하는 서브루틴으로서의 함수
값을 반환하도록 만들 수 있음

<예시>
```
function printLeapYearStatus() {
    const year = new Date().getFullYear();
    if(year % 4 !== 0) console.log(`${year} is NOT a leap year.`)
    else if(year % 100 != 0) console.log(`${year} IS a leap year.`)
    else if(year % 400 != 0) console.log(`${year} is NOT a leap year.`)
    else console.log(`{$year} IS a leap year`);
}
```
함수가 값을 반환하는 서브루틴이 되도록 고쳐 써보자
```
function isCurrentYearLeapYear() {
    const year = new Date().getFullYear();
    if(year % 4 !== 0) return false;
    else if(year % 100 != 0) return true;
    else if(year % 400 != 0) return false;
    else return true;
}
```
새로 만든 함수의 반환값을 활용
```
const daysInMonth =
    [31, isCurrentYearLeapYear() ? 29 : 28, 31, 30, 31, 30,
        31, 31, 30, 31, 30, 31];
if(isCurrentYearLeapYear()) console.log('It is a leap year.');
```


## 13.3 함수로서의 함수
순수한 함수
* 수학적인 정의에 충실한 함수
* 순수한 함수에서는 입력이 같으면 결과도 반드시 같음
* 외부의 상태를 변경하지 않으면서 동일한 인자에 대해 항상 똑같은 값을 리턴하는 함수

순수함수
```
    function add(a,b){
        return a + b;
    }
    console.log(add(10,5));

```
순수함수가 아닌것
```
var c = 10; 
function add2(a,b){
    return a + b + c;
}
console.log(add2(10,3));
c = 20;
console.log(add2(10,3));
```

## 13.4 그래서?
함수를 사용 하는 이유?
* 반복을 없애는 것 => 서브루틴을 쓰면 자주 사용하는 동작을 하나로 묶을 수 있음
* 순수한 함수를 많이 사용해라 

## 13.4.1 함수도 객체다
자바스크립트의 함수는 function 객체의 인스턴스 이다
typeof v 를 사용하면 v가 함수인 경우 "function"이 반환됨
따라서 변수가 함수인지 아닌지 확인하고 싶은 경우 typeof를 사용하면 됨

## 13.5 IIFE와 비동기적 코드
IIFE : 즉시 실행 함수 표현(Immediately Invoked Function Expression)은 정의되자마자 즉시 실행되는 Javascript Function 를 말한다.
<예제>
```
    (function(){
        var aName = "Baryy";
    })();
    // IIFE 내부에서 정의된 변수는 외부 범위에서 접근이 불가능하다.
    aName // throws "Uncaught ReferenceError: aName is not defined"
```
IIFE를 변수에 할당하면 IIFE 자체는 저장되지 않고, 함수가 실행된 결과만 저장된다.
```
    // 
    var result = (function(){
        var name = "Baryy";
        return name;
    })();
    // 즉시 결과를 생성한다.
    result; // "Barry"
```
비동기적 : 전 명령의 수행이 끝나지 않아도 다음 명령을 실행한다는 의미이다.
<예제>
```
    for (var i = 0; i < 10; i++) {
        console.log(i);
    }
    console.log('done');
```
이 코드는 아마 아래와 같은 결과를 출력할 것이다.
```
    0
    1
    2
    3
    4
    5
    6
    7
    8
    9
    done
```
뻔하다. 그럼 다음 코드를 보자
```
    for (var i = 0; i < 10; i++) {
        setTimeout(function() {
            console.log(i);
        }, 10);
    }
    console.log('done');
```
다를 바가 없어보이지만 출력한 결과는 사뭇 다르다.
```
    done
    10
    10
    10
    10
    10
    10
    10
    10
    10
    10
```
왜 done 이 먼저 출력되고 출력되지 말아야할 10만 열 번 출력되는 걸까? 그 이유는 JavaScript가 비동기적으로 동작하기 때문이다.
JavaScript는 특정 명력이 실행된 후 그 명령이 끝나기 전에 다음 명령이 실행될 수 있다. 앞선 첫 코드에서는 명령이 동작하는데 걸리는 시간이 모두 동일하기에 예상한대로 순서대로 출력되었지만, 둘째 코드에서는 for문 내에서 출력하는 부분에 시간지연이 생겨 결국 done이 먼저 출력된 것이다. done 이 출력되기전에 이미 for loop를 모두 반복하였으므로 i는 이미 10이 된 상태였고 그것을 열 번 출력했기 때문에 방금같은 결과가 나온 것이다.
JavaScript 코딩 시에는 서버쪽에서 request를 날릴 일이 많은데 reponse를 받는 속도는 명령 실행속도보다는 훨씬 느리기 때문에 이걸 원래 짜왔던 대로 절차지향적으로 짜게 되면 문제가 생긴다. 따라서 JavaScript가 동기적으로 동작하기 원한다면 [callback](https://goo.gl/9oJcGt)을 잘 이용해야 한다.


## 13.6 변수로서의 함수
* 함수를 가리키는 변수를 만들어 별명을 정할 수 있음
* 배열에 함수를 넣을 수 있음(물론 다른 타입의 데이터와 섞일 수 있음)
* 함수를 객체의 프로퍼티로 사용할 수 있음(9장에서 설명했음)
* 함수를 함수에 전달할 수 있음
* 함수가 함수를 반환할 수 있음
* 함수를 매개변수로 받는 함수를 반환할 수 있음

함수에 별명을 붙이는 것부터 생각해보자. 짧은 코드 안에서 여러 번 호출해야 하는 함수가 있다. 그런데 이 함수의 이름이 너무 길어서 타이핑하기 번거로울 뿐 아니라, 코드를 읽기도 무척 어려울 것 같다. 함수도 데이터이므로 짧은 이름의 변수에 저장할 수 있다.
```
function addThreeSquareAddFiveTakeSquareRoot(x) {
    return Math.sqrt(Math.pow(x+3, 2)+5);
}
​
// 별명을 쓰기 전
const answer = (addThreeSquareAddFiveTakeSquareRoot(5) + addThreeSquareAddFiveTakeSquareRoot(2)) / addThreeSquareAddFiveTakeSquareRoot(7);
​
// 별명을 쓴 후
cost f = addThreeSquareAddFiveTakeSquareRoot;
const answer = (f(5) + f(2)) / f(7);
```

## 13.6.1배열 안의 함수
예시
* 자주 하는 일을 한 셋으로 묶는 파이프라인
* 장점 : 배열을 사용하면 작업 단계를 언제든 쉽게 바꿀 수 있음(제거, 추가 자유로움)

<예제>
```
const sin = Math.sin;
const cos = Math.cos;
const theta = Math.PI/4;
const zoom = 2;
const offset = [1, -3];
​
const pipeline = [
    function rotate(p) {
        return {
            x: p.x * cos(theta) - p.y * sin(theta),
            y: p.x * sin(theta) + p.y * cos(theta),
        };
    },
    function scale(p) {
        return { x: p.x * zoom, y: p.y * zoom };
    },
    function translate(p) {
        return { x: p.x + offset[0], y: p.y + offset[1] };
    },
]
​
const p = { x: 1, y: 1 };
let p2 = p;
for (let i=0; i<pipeline.length; i++) {
    p2 = pipeline[i](p2);
}
```
```
    //es6
    let arr = [1, () => 2]
```

## 13.6.2함수에 함수 전달
함수에 함수를 전달하는 함수 : 콜백(callback)
```
1   function test(f){
2       f()
3   }
4   
5   //test(함수)
6   test(function(){
7       console.log('Callback Function!!')
8   });
```
test라는 함수는 인자로 함수를 받는다.
6행을 보면 test 함수를 호출하면서 새로운 함수를 전달하고 있다.
전달된 함수는 test 함수의 매개변수 f에 저장이 될 것이고
2행에서 f()를 호출하고 있다.
호출한 결과는 당연히 전달한 함수 내의 코드가 실행될 것이다.

```
    //es6
    const fun(n, f) => n + f();
    fun(1, () => 2);
```

## 13.6.3함수를 반환하는 함수
함수가 함수를 반환하는 패턴은 좀 복잡한 편

함수를 반환하는 함수의 예제를 더 보고 싶다면 
자바스크립트 웹 개발 프레임워크로 널리 쓰이는 익스프레스(Express)나 Koa 같은 미들웨어 패키지를 살펴 보자
미들웨어는 함수를 반환하는 함수 형태로 만들어짐

커링 : 매개변수 여러 개를 받는 함수를 매개변수 하나만 받는 함수로 바꾸는 것

<예제>
```
function outerFunction(){
    return function() {
        alert('Hello World...!');
    };
}
outerFunction()();

```
```
    //es6
    () => 1;
    () => () => 1;
```

## 13.7재귀
재귀함수는 말 그대로 함수 내에서 자기 자신을 다시 또 호출하면서 로직을 수행하는 함수

재귀 함수에 종료 조건이 있어야 함

종료 조건이 없다면 자바스립트 인터프리터에서 스택이 너무 깊다고 판단할 때 까지 재귀 호출을 계속 하다가 프로그램이 멈춤

<예제>
```
    function fact(n) {
        if (n === 1) return 1;
        return n * fact(n-1);
    }
```
```
    function add(){
    add();
    }
```
<다른예제>
```
function factorial(num)
{
    
    if (num < 0) {
        return -1;
    }

    else if (num == 0) {
        return 1;
    }
    var tmp = num;
    while (num-- > 2) {
        tmp *= num;
    }
    return tmp;
}

var result = factorial(8);
document.write(result);

// Output: 40320
```
재귀함수로 아래와 같이 만들 수 있다.
```
function factorial(num)
{
    
    if (num < 0) {
        return -1;
    }
  
    else if (num == 0) {
        return 1;
    }
   
    else {
        return (num * factorial(num - 1));
    }
}

var result = factorial(8);
document.write(result);

// Output: 40320
```