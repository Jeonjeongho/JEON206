# CHAPTER 6 함수
- 함수는 하나의 단위로 실행되는 문의 집합입니다.
- 모든 함수에는 바디가 있습니다. 
- 함수 바디는 함수를 구성하는 문(statement)의 모음입니다.

**함수선언(function declaration)**
```
    function sayHello() {
        //함수 바디는 여는 중괄호로 시작하고
        console.log("Hello World");
        //닫는 중괄호로 끝납니다.
    }
```
**함수호출(call)**
```
    sayHello(); //콘솔에 "Hello World"가 출력됩니다.
```

## 6.1 반환 값
- 함수 호출도 표현식이고, 표현식은 값이 됩니다. 
- 함수 바디 안에 return 키워드를 사용하면 현재 함수를 즉시 종료하고 그 함수로부터 값을 반환합니다. 
- 명시적 return 문이 없을 경우는 udefined를 반환합니다. 따라서 모든 함수는 값을 반환합니다.
```
    function getGreeting() {}
    getGreeting(); // undefined

    function getGreeting() {
        return "Hello world!";
    }
    getGreeting();  // "Hello, World!"
```

## 6.2 호출과 참조
자바스크립트에서는 함수도 객체입니다. 따라서 다른 객체와 마찬가지로 **넘기거나 할당**할 수 있습니다. 함수 호출과 참조의 차이를 이해하는 것이 중요합니다.
"Hello, World!"를 리턴하는 getGreeting 함수가 있다면 호출과 참조에 따라 반환 값이 달라집니다.
```
    getGreeting();  // "Hello, World!" [호출]
    getGreeting;    // function getGreeting() [참조]
```
함수를 호출하지 않고 다른 값과 마찬가지로 참조하기만 할 수 있다는 특징은 자바스크립트를 매우 유연한 언어로 만듭니다.
함수를 변수에 할당하여 다른 이름으로 함수를 호출 할수도 있고, 객체 프로퍼티에 할당 할 수도 있습니다.

* 함수를 변수에 할당하는 경우
```
    const f = getGreeting;
    f();    // "Hello, World!" 
```

* 함수를 객체 프로퍼티에 할당하는 경우
```
    const o = {};
    o.f = getGreeting;
    o.f();  // "Hello, World!" 
```

* 함수를 배열 요소에 할당하는 경우
```
    const arr = [1,2,3];
    arr[1] = getGreeting;   // arr은 이제 [1, function getGreeting(), 2]입니다.
    arr[1]()    // "Hello, World!" 
```
마지막 예제에서 arr\[1\]()를 보면 괄호가 하는 일을 명확히 알 수 있습니다. 자바스크립트는 괄호를 썼을때 함수로 간주하고 **호출**합니다.

## 6.3 함수와 매개변수(Function Arguments)
매개변수는 함수를 호출하는 쪽과 호출된 함수를 연결하는 매개가 되는 변수입니다. 이를 파라미터라고 부르기도 합니다.

> 함수 매개변수(parameter)는 함수 정의에 나열된 이름이고
> 
> 함수 인수(argument)는 함수에 전달된 실제 값입니다.

실제 매개변수는 변수와 매우 비슷하지만, 함수 바디 안에서만 존재합니다. 
```
    const a =5, b=10;
    avg(a,b);
```
첫 행의 변수 a,b는 함수 avg의 배개변수인 a,b와 같은 이름이지만, 엄연히 다른 변수입니다.
함수를 호출하면 함수 매개변수는 변수 자체가 아니라 그 값을 전달받습니다.
```
    function f(x) {
        console.log(`f 내부: x= ${x}`);
        x = 5;
        console.log(`f 내부: x= ${x} (할당 후)`);
    }
    let x = 3;
    console.log(`f를 호출하기 전: x= ${x}`);
    f(x);
    console.log(`f를 호출한 다음: x= ${x}`);
```

**실행 결과값**
```
    f를 호출하기 전: x=3
    f 내부: x=3
    f 내부: x=5 (할당 후)
    f를 호출한 다음: x=3
```
이 예제를 실행하면 함수 안에서 x에 값을 할당하더라도 함수 바깥의 변수 x에는 아무 영향도 없는 겁니다. 이름은 같지만, 둘은 다른 개체(entity)입니다. 
하지만 함수 안에서 객체(Object) 자체를 변경하면, 그 객체는 함수 바깥에서도 바뀐 점이 반영 됩니다. 다시 말해 원시 값은 불변으로 수정할 수 없지만 원시 값을 담은 변수는 수정할 수 있습니다.
```
    function f(o) {
        o.message = "f에서 수정함";  //참조값
        o = {
            message: "새로운 객체!"; //원시값
        };
        console.log(`f 내부: o.message= "${o.message}" (할당 후)`);
    }
    let o = {
        message: '초기값'
    };
    console.log(`f를 호출하기 전: o.message= "${o.message}"`);
    f(o);
    console.log(`f를 호출한 다음: o.message= "${o.message}"`);
```

**실행 결과값**
```
    f를 호출하기 전: o.message= "초기값"
    f 내부: o.message= "새로운 객체!" (할당 후)
    f를 호출한 다음: o.message= "f에서 수정함"
```
이 예제를 이해하는 핵심은 함수 내부의 매개변수 o와 함수 바깥의 변수 o가 다르다는 겁니다.
f를 호출하면 둘은 같은 객체를 가리키지만, f 내부에서 o에 할당한 객체는 새로운, 전혀 다른 객체입니다. 함수 바깥의 o는 여전히 원래 객체를 가리키고 있습니다.
> 원시 값(value type) : 단순한 데이터, undefined, null, boolean, number, string
> 
> 참조 값(reference type) : 메모리에 저장된 객체. 해당 객체에 대한 '참조'를 조작한다는 표현이 맞습니다.

### 6.3.1 매개변수가 함수를 결정하는가?
- 자바스크립트에서는 매개변수를 한 개 전달하든 열 개 전달하든 같은 함수를 호출합니다.
- 정해진 매개변수보다 적게 전달되거나 값을 제공하지 않으면 암시적으로 undefined가 할당됩니다.

예를 들어 C언어에서 매개 변수 없는 f()와 배개변수가 하나 있는 f(x), 배개변수가 두개있는 f(x,y)는 모두 다른 함수로 보지만 자바스크립트에서는 그런 차이를 두지 않습니다.
```
    function f(x) {
        return `in f: x=${x}`;
    }
    f();    // "in f: x=undefined"
    f(1);    // "in f: x=1"
```

### 6.3.2 매개변수 해체
[5장](../Chapter5/index.md#해체-할당)에서 배웠듯, 객체나 배열처럼 매개변수도 변수로 해체할 수 있습니다.

* 객체를 변수로 해체
```
    function getSentece({subject, verb, object}) {
        return `${subject} ${verb} ${object}`;
    }

    const o = {
        subject: "I",
        verb: "love",
        object: "JavaScript"
    };

    getSentece(o);  // "I love JavaScript"
```
해체 할당과 마찬가지로 프로퍼티 이름은 반드시 유효한 식별자여야 하고, 들어오는 객체에 해당 프로퍼티가 없는 변수는 undefined를 할당받습니다.
```
    function getSentece({subject, verb, object}) {
        return `${subject}, ${verb, object}`;
    }

    getSentece(o);  // "I, JavaScript"
```


* 배열을 변수로 해체
```
    function getSentece([subject, verb, object]) {
        return `${subject} ${verb} ${object}`;
    }

    const arr = [ "I", "love", "JavaScript" ];
    getSentece(arr);  // "I love JavaScript"
```

* 확산 연산자(...)를 써서 남는 매개변수를 이용
```
    function addPrefix(prefix, ...words) {
        //나중에 더 좋은 방법을 배웁니다.
        const prefixedWords = [];
        for(let i=0;i<words.length;i++) {
            prefixedWords[i] = prefix + words[i];
        }
        return prefixedWords;
    }

    addPrefix("con","verse","vex"); // ["converse","convex"]
```
함수를 선언할 때 확산 연산자는 **반드시 마지막 매개변수** 여야 합니다. 확산 연산자 뒤에 다른 매개변수가 있으면 자바스크립트는 전달된 값 중 어디까지를 확산 매개변수에 할당해야 하는지 판달 할 수 없어서 에러를 일으킵니다.

> ES5에서는 함수 바디 안에서만 존재하는 특별한 변수 arguments를 사용해서 확산과 비슷한 일을 할 수 있습니다. arguments는 실제 배열이 아니라 일반적인 객체로 변환해야 한다는 약점을 ES6에서 보완하여 확산 매개변수를 사용하는 것이 좋습니다.

* 인수 객체(argument object)라는 내장 객체를 활용
```
    function sumAll() { 
        var i,sum = 0;
        for(i=0;i<arguments.length;i++) {
            sum += arguments[i];
        }
        return sum;
    }

    sumAll(1,123,500,115,44,88);    //871
```

### 6.3.3 매개변수 기본값
- 일반적으로 매개변수에 값을 제공하지 않으면 undefined가 값으로 할당됩니다. 
- ES6에서는 기본값을 지정 할 수 있도록 추가되었습니다.
```
    function f(a, b="default", c=3) {
        return `${a} - ${b} - ${c}`;
    }

    f(5,6,7);   // "5 - 6 - 7"
    f(5,6);     // "5 - 6 - 3"
    f(5);       // "5 - default - 3"
    f();        // "undefined - default - 3"
```

## 6.4 객체의 프로퍼티인 함수
객체의 프로퍼티인 함수를 메서드라고 불러서 일반적인 함수와 구별합니다. 객체 리터럴에서도 메서드를 추가할 수 있었는데, ES6에서는 더욱 간편하게 메서드를 추가할 수 있는 문법이 새로 생겼습니다. 다음 예제는 동일합니다.

* ES5 
```
    const o = {
        name: 'Wallace',    //원시 값 프로퍼티
        bark: function() {return 'Woof!';}, //함수 프로퍼티(메서드)
    }
```

* ES6 
```
    const o = {
        name: 'Wallace',    //원시 값 프로퍼티
        bark() {return 'Woof!';},    //함수 프로퍼티(메서드)
    }
```

## 6.5 this 키워드
- 함수 바디 안에서의 this는 일반적으로 객체의 프로퍼티인 함수에서 의미가 있습니다.
- 메서드를 호출하면 this는 호출한 메서드를 소유하는 객체가 됩니다.
- 중첩된 함수 안에서 this를 사용하면 의도한 대로 동작하지 않아 다른 변수 self나 taht에 this를  할당해서 사용하는 경우가 많은데, 화살표 함수를 써도 이 문제를 해결할 수 있습니다.
```
    const o = {
        name: 'Wallace',    //원시 값 프로퍼티
        speak() {return `My name is ${this.name}!`;},
    }
```
this는 함수를 어떻게 선언했느냐 보다 어떻게 호출했느냐에 따라 달라지는 것을 이해해야 합니다. o.speak()의 this는 o를 가리키지만 speak의 this는 window를 가리키기 때문에 this.name은 undefined가 됩니다.
```
    o.speak();              //"My name is Wallace!"
    const speak = o.speak;  
    speak === o.speak;      //true; 두 변수는 같은 함수를 가리킵니다.
    speak();                //"My name is undefined!"
```

> this 키워드는 수행되는 영역, 호출자 두 가지 조건에 의해 값이 결정 되는데 쉽게 생각해서 호출 함수 앞에 점(.)이 있다면 그 점(.)앞의 객체를 의미한다고 생각 하면 쉽습니다.

* 중첩함수에서 this 키워드 사용
```
    const o = {
        name: 'Julie',
        greetBackwards: function() {
            const self = this; // 화살표 함수에서는 생량 가능
            function getReverseName() {
                let nameBackWards = '';
                for(let i=self.name.length-1;i>=0;i--) {
                    nameBackWards += self.name[i];
                }
                return nameBackWards;
            }
            return `${getReverseName()} si eman ym ,olleH`;
        },
    };
    o.greetBackwards();
```

## 6.6 함수 표현식과 익명 함수
- 익명 함수에는 식별자가 주어지지 않습니다.
- 익명 함수를 호출하는 방법에는 함수 표현식이 있습니다.
- 함수 표현식은 익명 함수를 할당할 수도 있고, 이름 있는 함수를 할당 할 수도 있습니다.
- 나중에 호출할 생각으로 함수를 만든다면 함수 선언을 사용하고, 
- 다른 곳에 할당하거나 다른 함수에 넘길 목적으로 함수를 만든다면 함수 표현식을 사용하면 됩니다.


* 함수 표현식
```
    const f = function() {
        //...
    }

    const g = function f() {
        //...
    }
```
위 예제처럼 g에 f함수를 할당한 경우 함수에 접근 할때는 g를 써서 호출하고 함수 안에서 자신을 호출할 때(재귀) f를 써서 자기 자신을 참조 합니다.
```
    const g = function f(stop) {
        if(stop) {
            console.log('f stopped');
        } else {
            console.log(stop);
            f(true);
        }
    };
    g(false);
```

## 6.7 화살표 표기법
- ES6에서 새로만든 function이라는 단어와 중괄호 숫자를 줄이려고 고안된 **단축 문법**입니다.
- 화살표 함수는 항상 **익명 함수**이고 다른 곳에 전달하려 할 때 가장 유용합니다. [[8장 참조]](../Chapter8/index.md)
- 일반적인 함수와 달리 객체 생성자로 사용할 수 없고, arguments 변수도 사용할 수 없습니다.[[9장 참조]](../Chapter9/index.md)
- this 키워드가 다른 변수와 마찬가지로 정적으로 묶입니다. 위 예제에서 본 greetBackwards 예제에서 처럼 this를 self 변수에 할당 하지 않고 내부 함수에서 this를 사용할 수 있습니다.


화살표 함수에는 세 가지 단축 문법이 있습니다.
- function을 생략해도 됩니다.
- 함수에 매개변수가 단 하나 뿐이라면 괄호()도 생략할 수 있습니다.
- 함수 바디가 표현식 하나라면 중괄호와 return 문도 생략할 수 있습니다.
```
    const f1 = function() {return "hello!";}
    //또는
    const f1 = () => "hello!";

    const f2 = function(name) {return `Hello, ${name}!`;}
    //또는
    const f2 = name => `Hello, ${name}!`;

    const f3 = function(a,b) {return a+b;}
    //또는
    const f3 = (a,b) => return a+b;

```

## 6.8 call과 apply, bind
### call
- 모든 함수에서 사용할 수 있습니다.
- this를 특정 값으로 지정할 수 있습니다. (명시적 this 활용)
```
    const bruce = {name: "Bruce"};
    const madeline = {name: "Madeline"};

    function greet() {
        return `Hello, I'm ${this.name}!`;
    }

    greet();                //"Hello, I'm !"
    greet.call(bruce);      //"Hello, I'm Bruce!"
    greet.call(madeline);   //"Hello, I'm Madeline!"
```
- call의 첫 번째 매개변수는 this로 활용할 값이고, 나머지 매개변수는 호출하는 함수로 전달됩니다.
```
    function update(birthYear, occupation) {
        this.birthYear = birthYear;
        this.occupation = occupation;
    }

    update.call(bruce, 1949, 'singer'); 
    //bruce = {name: "Bruce", birthYear: 1949, occupation: "singer"}
    
    update.call(madeline, 1942, 'actress');
    //madeline = { name: "Madeline", birthYear: 1942, occupation: "actress" }

```

### apply
- 함수 매개변수를 배열로 받는 것을 제외하면 call과 완전히 같습니다.
```
    update.aplly(bruce, [1955, 'actor']); 
    //bruce = {name: "Bruce", birthYear: 1949, occupation: "singer"}
    
    update.aplly(madeline, [1918, 'writer']);
    //madeline = { name: "Madeline", birthYear: 1942, occupation: "actress" }
```
- 배열 요소를 함수 매개변수로 사용할 때 유용합니다. 
```
    const arr2 = [ 2, 3, -5, 15, 7 ];
    console.log(Math.min.apply(null, arr2)); // -5
    console.log(Math.max.apply(null, arr2)); // 15
```


### bind

## 요약


