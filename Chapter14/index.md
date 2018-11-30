# Chapter 14 비동기적 프로그래밍

- 동기식(Synchronous)
- 비동기식(Asynchronous)

## 14.1 콜백

콜백은 자바스크립트에서 **가장 오래된 비동기적 메커니즘**입니다.<br>
콜백 함수는 일반적으로 다른 함수에 넘기거나 객체의 프로퍼티로 사용합니다.

- 1에서 10까지 더하는 콜백 함수 예제

```a
let idx = 1;

function callFn(idx) {
    if( idx == 10 ) {
        return idx;
    } else {
        return idx + callFn(idx + 1);
    }
}

callFn(idx);
```

- setTimeout을 사용한 예제

```
console.log('Before Timeout : ' + new Date());

function f() {
    console.log('After Timeout : ' + new Date());
}

setTimeout(f, 5*1000);  // 5초

console.log('I happen after setTimeout!');
console.log('Me too!');
```

### 14.1.1 setInterval과 clearInterval

setTimeout은 콜백 함수를 한 번만 실행하고 멈추지만, setInterval은 콜백을 정해진 주기마다 호출하며, clearInterval을 사용할 때까지 멈추지 않습니다.

```
const start = new Date();
let i = 0;

const intervalId = setInterval(function() {
    let now = new Date();

    if( now.getMinutes() !== start.getMinutes() || ++i > 10 )
    return clearInterval(intervalId);

    console.log(`${i} : ${now}`);

}, 1*1000);
```

### 14.1.2 스코프와 비동기적 실행

비동기적 실행에서 혼란스럽고 에러도 자주 일어나는 부분은 스코프와 클로저가 비동기적 실행에 영향을 미치는 부분입니다.

```
function countDown() {
    var i;

    console.log("Countdown start...");

    for(i = 5; i >= 0; i--) {
        setTimeout(function(){
            console.log(i===0 ? "GO!" : i);
        }, ( 5 - i ) * 1000);
    }
}

countDown();        // ??
```


해결하는 방법은 아래와 같습니다.

1. setTimeout 함수가 호출되기 전 익명함수 또는 선언적 함수를 사용하는 방법
2. var → let 그리고 for문에서 변수 선언하는 방법(http://exploringjs.com/es6/ch_variables.html#sec_let-const-loop-heads)

### 14.1.3 오류 우선 콜백과 콜백 지옥

- 오류 우선 콜백(Error-First Callback) 패턴은 노드가 인기를 얻어가던 시기에 생겼습니다. 콜백의 첫 번째 매개변수에 에러 객체를 쓰고, 에러가 null이나 undefined이면 에러가 없습니다.

- 콜백 지옥(Callback Hell)은 비동기 처리 로직을 위해 콜백 함수를 연속해서 사용할 때 발생하는 문제입니다.

```
function add(x, callback) {
  let sum = x + x;
  console.log(sum);
  callback(sum);
}
 
add(3, function(result){
  add(result, function(result2) {
    add(result2, function(result3) {
      add(result3, function(result4) {
        console.log("콜백 지옥 끝");
      })
    })
  })
})
```

## 14.2 Promise

자바스크립트 비동기 처리에 사용되는 객체이며, Promise는 콜백의 단점을 해결하기 위해 만들어졌습니다.

### 14.2.1 Promise의 처리 과정

Promise의 가장 기본 개념은 Promise의 처리 과정입니다.<br>
new Promise() 생성 후 종료될 때까지 총 3가지의 상태가 있습니다.<br>

- 대기(Pending) : 이행 또는 거부가 되기 전의 초기 상태
- 이행(Fulfilled) : 동작이 성공한 상태
- 거부(Rejected) : 동작이 실패한 상태

```
new Promise(function (resolve, reject) {
    //...
});
```

### 14.2.2 Promise 이행

```
function countdown(seconds) {
    return new Promise(function(resolve, reject) {
        for(let i = seconds; i >= 0; i--) {
            setTimeout(function() {
                if( i > 0 ) console.log(i + '...');
                else resolve(console.log("Go!"));
            }, (seconds - i) * 1000);
        }
    });
}

countdown(5).then(
    function() {
        console.log('function Success');
    },
    function(err) {
        console.log(err.message)
    }
)
```

### 14.2.3 Promise 거부

```
function countdown(seconds) {
    return new Promise(function(resolve, reject) {
        for(let i = seconds; i >= 0; i--) {
            setTimeout(function() {
                if( i === 3 ) return reject(new Error('Function Error'));
                if( i > 0 ) console.log(i + '...');
                else resolve(console.log("Go!"));
            }, (seconds - i) * 1000);
        }
    });
}

const p = countdown(5);

p.then(function() {
    console.log('function Success');
})
p.catch(function(err) {
    console.log(err.message)
})
```

### 14.2.4 Promise.all

Promise.all 메서드는 이터레이블 인자의 모든 Promise가 이행하거나 이터레이블 인자가 비어있는 경우 이행하는 프로미스를 반환합니다.

```
var promise1 = Promise.resolve(3);
var promise2 = 42;
var promise3 = new Promise(function(resolve, reject) {
  setTimeout(resolve, 100, 'foo');
});

Promise.all([promise1, promise2, promise3]).then(function(values) {
  console.log(values);
});
```

### 14.2.5 Promise 체이닝

Promise는 여래 개의 Promise를 연결하여 사용할 수 있습니다.

```
new Promise(function(resolve, reject){
  setTimeout(function() {
    resolve(1);
  }, 2000);
})
.then(function(result) {
  console.log(result);
  return result + 10;
})
.then(function(result) {
  console.log(result);
  return result + 20;
})
.then(function(result) {
  console.log(result);
});
```

### 14.2.6 Promise 에러 처리

Promise는 then()의 두 번째 파라미터로 처리할 수도 있고, catch()로도 처리 가능하지만 가급적으로 catch()로 에러처리를 하는게 더 효율적입니다.<br>
그 이유는 then()의 첫 번째 콜백 함수 내부에서 발생하는 오류를 제대로 잡아내지 못하기 때문입니다.

```
// then() 예외처리
function getData() {
  return new Promise(function (resolve, reject) {
    resolve('hi');
  });
}

getData().then(function (result) {
  console.log(result);
  throw new Error("Error in then()");
}, function (err) {
  console.log('then error : ', err);
});


// catch() 예외처리
function getData() {
  return new Promise(function (resolve, reject) {
    resolve('hi');
  });
}

getData().then(function (result) {
  console.log(result); // hi
  throw new Error("Error in then()");
}).catch(function (err) {
  console.log('then error : ', err); // then error :  Error: Error in then()
});
```