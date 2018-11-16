# CHAPTER 12 이터레이터와 제너레이터
- ES6에서 새로 도입
- 제너레이터 : 이터레이터에 의존하는 개념입니다.
- 이터레이터 : '지금 어디 있는지' 파악할 수 있도록 돕는다는 면에서 일종의 **책갈피**와 비슷한 개념입니다. 배열은 이터러블 객체의 좋은 예입니다.

## 이터레이터
- **values() 메서드** : 이터레이터 생성
    - book이라는 배열에 values 메서드를 사용하여 이터레이터로 만들었습니다.
    ```
        const book = [
            "Twinkle, twinkle, little bat!",
            "How I wonder what you're at!",
            "Up above the world you fly,",
            "Linke a tea tray in the sky.",
            "Twinkle, twinkle, little bat!",
            "How I wonder what you're at!",
        ];

        // book이라는 배열에 values메서드를 사용하여 이터레이터로 만듭니다.
        const it = book.values();
    ```
- **next() 메서드** : 이터레이터 실행
    - book이라는 배열이 책이라면 책을 읽기 전에는 책갈피를 꽂을 수 없습니다.
    - '읽기 시작'하려면 **next** 메서드를 호출합니다.
    - next메서드가 반환하는 객체에는 value, done 프로퍼티가 있습니다.
    - 진행할 것이 있으면 done 프로퍼티 값이 false이고, 마지막 페이지인 경우 value는 undefined, done은 true 값을 반환 합니다.
    - 계속 next로 호출할 수 있으나 끝까지 진행하면 뒤로 돌아가서 다른 데이터를 제공할 수는 없습니다.
    ```
        it.next(); // { value: "Twinkle, twinkle, little bat!", done: false }
        it.next(); // { value: "How I wonder what you're at!", done: false }
        it.next(); // { value: "Up above the world you fly,", done: false }
        it.next(); // { value: "Like a tea tray in the sky.", done: false }
        it.next(); // { value: "Twinkle, twinkle, little bat!", done: false }
        it.next(); // { value: "How I wonder what you're at!", done: false }
        it.next(); // { value: undefined, done: true }
        it.next(); // { value: undefined, done: true }
        it.next(); // { value: undefined, done: true }
    ```
- **독립적**
    - 새 이터레이터를 만들 때마다 처음에서 시작합니다.
    - 모두 독립적이며 여러 이터레이터를 동시에 사용할 수도 있습니다.
    ```
        const it1 = book.values();
        const it2 = book.values();
        // 어느 이터레이터도 아직 시작하지 않았습니다.
        // it1으로 두 페이지를 읽습니다.
        it1.next(); // { value: "Twinkle, twinkle, little bat!", done: false }
        it1.next(); // { value: "How I wonder what you're at!", done: false }
        // it2로 한 페이지를 읽습니다.
        it2.next(); // { value: "Twinkle, twinkle, little bat!", done: false }
        // it1으로 한 페이지를 더 읽습니다.
        it1.next(); // { value: "Up above the world you fly,", done: false }
    ```


## 12.1 이터레이션 프로토콜
> '순회가능한(iterable)' 객체란 무엇일까? 바로 Symbol.iterator 심볼을 속성으로 가지고 있고, 이터레이터 객체를 반환하는 객체를 뜻한다. 이런 스펙을 이터러블 프로토콜 이라고 하고 이 프로토콜을 지킨 객체를 이터러블 객체라고 한다. [참고](https://gist.github.com/qodot/ecf8d90ce291196817f8cf6117036997)

- 이터레이터 프로토콜은 symbol 메서드를 통해 모든 객체를 이터러블 객체로 바꿀 수 있습니다.
```
    class Log {
        constructor() {
           this.messages = [];
        }
        add(message) {
           this.messages.push({ message, timestamp: Date.now() });
        }
    }
```
- 메시지에 타임스탬프를 붙이는 로그 클래스가 필요하다고 생각해 봅시다. 내적으로 타임 스탬프가 붙은 메시지는 배열에 저장합니다.
- 로그를 기록한 항목을 순회(이터러블)하고 싶다면? log.messages에 접근 할 수 있지만, log를 배열을 조작하듯 가능하게 해주는 것이 이터레이션 프로토콜입니다.
- 이터레이터 프로토콜을 구현하는 방법은 다음과 같이 두가지 방법이 있습니다.
    - **이터레이터를 가져와 이터레이터 프로토콜을 구현하는 방법**
    ```
        class Log {
            constructor() {
                this.messages = [];
            }
            add(message) {
                this.messages.push({ message, timestamp: Date.now()});
            }

            [Symbol.iterator]() {
                return this.messages.values();
            }
        }
        
        const log = new Log();
        log.add("first day at see");
        log.add("spotted whale");
        log.add("spotted another vessel");
        ​
        //로그를 배열처럼 순회합니다.
        for (let entry of log) {
            console.log(`${entry.message} @ ${entry.timestamp}`);
        }

        //결과
        first day at see @ 1542356233131
        spotted whale @ 1542356233131
        spotted another vessel @ 1542356233131
    ```
    - **직접 이터레이터를 만드는 방법**
    ```
    class Log {
        constructor() {
            this.messages = [];
        }
        add(message) {
            this.messages.push({ message, timestamp: Date.now()});
        }
        [Symbol.iterator]() {
            let i = 0;
            const messages = this.messages;
            return {
                next() {
                    if(i >= messages.length)
                        return { value: undefined, done: true };
                    return { value: messages[i++], done: false };
                }
            }
        }
    }
    ```
> 지금까지 책의 페이지나 타임스탬프가 붙은 로그처럼 숫자가 정해진 요소들을 순회하는 요소들을 살펴 보았습니다. 하지만 이터레이터는 무한한 데이터도 사용할 수 있으며(피보나치 수열 참조), 이때 이터레이터가 done을 절대 true로 반환하지 않으므로 for..of 사용시 조건을 만들어 break 문으 무한 루프에 빠지는 것을 유의 하세요.

## 12.2  제너레이터
- 이터레이터를 사용해 자신의 실행을 제어하는 함수 입니다.
- 제너레이터의 두 가지 새로운 개념을 도입했습니다.
    - 함수의 실행을 개별적 단계로 나눠 next() 메서드를 통해 함수의 실행을 제어
    - 실행 중인 함수와 통신한다는 것입니다.
- 제너레이터는 두 가지 예외를 제외하면 일반적인 함수와 같습니다.
    - 언제든 호출자에게 제어권을 넘길(yield) 수 있습니다.
    - 호출한 즉시 실행되지는 않습니다. 대신 이터레이터를 반환하고, 이터레이터의 next 메서드를 호출함에 따라 실행됩니다.
- 제너레이터 만드는 방법
    - function 키워드 뒤에 애스터리스크(\*)를 붙입니다.
        - 화살표 표기법으로 만들 수 없으며 반드시 function\*을 써야 합니다.
    - return 외에 yield 키워드를 쓸 수 있습니다.
    ```
        function* rainbow() { // * 기호는 제너레이터 문법입니다.
            yield 'red';
            yield 'orange';
            yield 'yellow';
            yield 'green';
            yield 'blue';
            yield 'indigo';
            yield 'violet';
        }
    ```
- 제너레이터 호출 방법
    ```
    const it = rainbow();
    it.next(); // { value: "red", done: false }
    it.next(); // { value: "orange", done: false }
    it.next(); // { value: "yellow", done: false }
    it.next(); // { value: "green", done: false }
    it.next(); // { value: "blue", done: false }
    it.next(); // { value: "indigo", done: false }
    it.next(); // { value: "violet", done: false }
    it.next(); // { value: undefined, done: true }
    ```

### 12.2.1 yield 표현식과 양방향 통신
- 제너레이터는 호출자와 yield 표현식을 통해 양방향 통신을 합니다.
- yield 표현식은 제너레이터의 이터레이터에서 next를 호출 할때 제공하는 매개변수입니다.
- 호출자가 제너레이터에 정보를 전달하므로, 제너레이터는 그 정보에 따라 자신의 동작 방식 자체를 바꿀 수 있습니다.
    ```
    function* interrogate() {
        const name = yield "What is your name?";
        const color = yield "What is your favorite color?";
        return `${name}'s favorite color is ${color}.`;
    }

    const it = interrogate();
    it.next(); // { value: "What is your name?", done: false }
    it.next('Ethan'); // { value: "What is your favorite color?", done: false }
    it.next('orange'); // { value: "Ethan's favorite color is orange.", done: true }
    ```
    1. 제너레이터는 이터레이터를 반환 return하고 일시 정지한 상태로 시작합니다.
    2. undefined를 제너레이터에 넘깁니다(이 값은 사용되지 않습니다). 제너레이터는 "What is your name?"
을 넘기고(yield) 일시 정지합니다.
    3. "Ethan"을 제너레이터에 넘깁니다. 제너레이터는 "What is your favorite color?"를 넘기고 일시 정
    지합니다.
    4. "orange"를 제너레이터에 넘깁니다. 제너레이터는 "Ethan's favorite color is orange"를 반환하고
멈춥니다.

### 12.2.2 제너레이터와 return
- yield 문 : 제너레이터의 마지막 문이더라도 제너레이터를 끝내지 않습니다.
- return 문 : 그 위치와 관계없이 done은 true가 되고, value 프로퍼티는 return이 반환하는 값이 됩니다.
    -  제너레이터에 return을 쓸 때는 반환값을 쓰지 않는 습관을 들이길 권합니다.
```
    function* abc() {
        yield 'a';
        yield 'b';
        return 'c';
    }
    const it = abc();
    it.next(); // { value: 'a', done: false }
    it.next(); // { value: 'b', done: false }
    it.next(); // { value: 'c', done: true }

    // "a"와 "b"는 출력되지만 "c"는 출력되지 않습니다.
    for(let l of abc()) {
        console.log(l);
    }
```

### 12.3 요약
이터레이터는 배열이나 객체처럼 여러 가지 값을 제공할 수 있는 컬렉션의 동작 방식을 표준화했습니다. 
이터레이터로 할 수 있는 일은 ES6 이전에도 모두 할 수 있었으므로, 어떤 기능이 추가된 것은 아닙니다. 
중요하면서도 자주 사용하는 패턴을 표준화했다는 데 의미가 있는 겁니다.
