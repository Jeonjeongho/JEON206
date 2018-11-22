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

함수 이름 예시
* 불리언을 반환하거나, 불리언이 필요한 컨텍스트에서 사용하도록 만든 함수는 is로 시작하는 이름을 붙이는 게 일반적

항상 현 날짜를 기준으로 한다면 current 라는 이름을 붙이기도 함

## 13.3 함수로서의 함수
순수한 함수
* 수학적인 정의에 충실한 함수
* 순수한 함수에서는 입력이 같으면 결과도 반드시 같음
* side effect 가 없어야 함 => 함수를 호출한다고 해서 프로그램의 아태가 바뀌어서는 안됨

## 13.4 그래서?
함수를 사용 하는 이유?
* 반복을 없애는 것 => 서브루틴을 쓰면 자주 사용하는 동작을 하나로 묶을 수 있음
* 순수한 함수를 많이 사용해라 

## 13.4.1 함수도 객체다
자바스크립트의 함수는 function 객체의 인스턴스
typeof v 를 사용하면 v가 함수인 경우 "function"이 반환됨
따라서 변수가 함수인지 아닌지 확인하고 싶은 경우 typeof를 사용하면 됨

## 13.5 IIFE와 비동기적 코드

## 13.6 변수로서의 함수
* 함수를 가리키는 변수를 만들어 별명을 정할 수 있음
* 배열에 함수를 넣을 수 있음
* 함수를 객체의 프로퍼티로 사용할 수 있음
* 함수를 함수에 전달할 수 있음
* 함수가 함수를 반환할 수 있음
* 함수를 매개변수로 받는 함수를 반환할 수 있음
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

## 13.6.2함수에 함수 전달
함수에 함수를 전달하는 함수 : 콜백(callback)
```
function sum (arr, f) {
    // 함수가 전달되지 않았으면 매개변수를 그대로 반환하는 null 함수를 사용
    if (typeof f != 'function') f = x => f
    
    return arr.reduce((a, x) => a += f(x), 0);
}
​
sum([1, 2, 3]); // 6
sum([1, 2, 3], x => x*x); // 14
sum([1, 2, 3], x => Math.pow(x, 3)); // 36
```

## 13.6.3함수를 반환하는 함수
패턴이 조금 복잡한 편

자바스크립트 웹 개발 프레임워크로 널리 쓰이는 익스프레스(Express)나 Koa 같은 미들웨어 패키지는 함수를 반환하는 함수 형태로 만들어짐

커링 : 매개변수 여러 개를 받는 함수를 매개변수 하나만 받는 함수로 바꾸는 것

<예제>
```
function sumOfSquared(arr) {
    return sum(arr, x => x*x);
}
​
function newSummer(f) {
    return arr => sum(arr, f);
}
​
const sumOfSquared = newSummer(x => x*x);
const sumOfCubes = newSummer(x => Math.pow(x, 3));
​
sumOfSquared([1, 2, 3]); // returns 14
sumOfCubes([1, 2, 3]); // return 36

```

## 13.7재귀
자기 자신을 호출하는 함수

재귀 함수에 종료 조건이 있어야 함

종료 조건이 없다면 자바스립트 인터프리터에서 스택이 너무 깊다고 판단할 때 까지 재귀 호출을 계속 하다가 프로그램이 멈춤

<예제>
```
function fact(n) {
    if (n === 1) return 1;
    return n * fact(n-1);
}
```