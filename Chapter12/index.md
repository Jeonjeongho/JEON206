# CHAPTER 12 이터레이터와 제너레이터
- ES6에서 새로 도입
- 제너레이터 : 이터레이터에 의존하는 개념입니다.
- 이터레이터 : '지금 어디 있는지' 파악할 수 있도록 돕는다는 면에서 일종의 **책갈피**와 비슷한 개념입니다. 배열은 이터러블 객체의 좋은 예입니다.
- https://gist.github.com/qodot/ecf8d90ce291196817f8cf6117036997

### 이터레이터
#### values() 메서드
- **values** 메서드를 사용하여 이터레이터로 만듭니다.
- 아래 예제에서는 book이라는 배열에 values 메서드를 사용하여 이터레이터로 만들었습니다.
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
- book이라는 배열이 책이라면 책을 읽기 전에는 책갈피를 꽂을 수 없습니다.
- '읽기 시작'하려면 **next** 메서드를 호출합니다.

#### next() 메서드
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


#### 독립적
- 새 이터레이터를 만들 때마다 처음에서 시작합니다.
- 모두 독립적으로 여러 이터레이터를 동시에 사용할 수도 있습니다.
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
- 메시지에 타임스탴프를 붙이는 로그 클래스가 필요하다고 생각해 봅시다. 내적으로 타임 스탬프가 붙은 메시지는 배열에 저장합니다.
- 로그를 기록한 항목을 순회(이터러블)하고 싶다면? log.messages에 접근 할 수 있지만, log를 배열을 조작하듯 가능하게 해주는 것이 이터레이션 프로토콜입니다.
- 이터레이터 프로토콜을 구현하는 방법은 다음과 같이 두가지 방법이 있습니다.

**이터레이터를 가져와 이터레이터 프로토콜을 구현하는 방법**
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

**직접 이터레이터를 만드는 방법**
```
class Log {
    //...
    [Symbol.iterator]() {
        let i = 0;
        const messages = this.messages;
        return {
            next() {
                if(i > = messages.length)
                    return { value: undefined, done: true };
                return { value: messages[i+ +], done: false };
            }
        }
    }
}
```

**피보나치수열 예**

## 12.2  제너레이터
- 이터레이터를 사용해 자신의 실행을 제어하는 함수 입니다.
- 제너레이터의 두 가지 새로운 개념을 도입했습니다.
    - 함수의 실행을 개별적 단계로 나눠 next() 메서드를 통해 함수의 실행을 제어
    - 실행 중인 함수와 통신
- 제너레이터의 특징
    