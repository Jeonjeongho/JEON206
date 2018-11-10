# chapter10 : 맵과 셋 
맵은 키와 값을 연결한다는 점에서 객체와 비슷하고, 셋은 중복을 허용하지 않는다는 점만 제외하면 배열과 비슷하다.

## 10.1 Map(맵)
es6 이전에는 키와 값을 연결하려면 객체를 사용해야 했다. 하지만 객체를 이런 목적으로 사용하면 여러 가지 단점이 생긴다.
* 프로토타입 체인 때문에 의도하지 않은 연결이 생길 수 있다. 
    * [프로토타입 체인](https://goo.gl/LrMeeg) : 객체와 객체의 연결을 통한 단방향 공유 관계
* 객체 안에 연결된 키와 값이 몇 개나 되는지 쉽게 알아낼 수 있는 방법이 없다.
* 키는 반드시 문자열이나 심볼이어야 하므로 객체를 키로 써서 값과 연결할 수 없다.
* 객체는 프로퍼티 순서를 전혀 보장하지 않는다.

[Map](https://goo.gl/DUs6c8) 객체는 이들 결함을 모두 해결했고, 간단한 키와 값을 서로 연결(매핑)시켜 저장하며 저장된 순서대로 각 요소들을 반복적으로 접근할 수 있도록 한다.

1.사용자 객체가 여럿 있고 이들에게 각각 역할을 부여 한다고 해보자.
```
    const u1 = {name: 'Cynthia'};
    const u2 = {name: 'Jackson'};
    const u3 = {name: 'Olive'};
    const u4 = {name: 'James'};
```
2.맵을 만든다.
```
    const userRoles = new Map();
```
3.맵의 set() 메서드를 써서 사용자 역할을 할당한다.
```
    userRoles.set(u1, 'User');
    userRoles.set(u2, 'User');
    userRoles.set(u3, 'Admin');
    //애석하지만 제임스에게는 역할이 없다.
```
4.set() 메서드는 체인으로 연결할 수 있어서 타이핑하는 수고를 덜어 준다. (3번과동일)
```
    userRoles
        .set(u1, 'User')
        .set(u2, 'User')
        .set(u3, 'Admin');
```
5.생성자에 배열의 배열을 넘기는 형태로 써도 된다. (2번과동일)
```
const userRoles = new Map([
    [u1, 'User'],
    [u2, 'User'],
    [u3, 'Admin']
]);
```
6.이제 u2의 역할을 알아볼 때는 get() 메서드를 쓰면 된다.
```
    userRoles.get(u2);      //"User"
```
7.맵에 존재하지 않는 키에 get을 호출하면 undefined를 반환한다. 맵에 키가 존재하는지 확인하는 has() 메서드도 있다.
```
    userRoles.has(u1);      //true
    userRoles.get(u1);      //"User"
    userRoles.has(u4);      //false
    userRoles.get(u4);      //undefined
```
8.맵에 이미 존재하는 키에 set()을 호출하면 값이 교체된다.
```
    userRoles.get(u1);          //'User'
    userRoles.set(u1,'Admin');
    userRoles.get(u1);          //'Admin'
```
9.[size 프로퍼티](https://goo.gl/2dNN3p)는 맵의 요소 숫자를 반환한다.
```
    userRoles.size;         //3
```
10.keys() 메서드는 맵의 키를, values() 메서드는 값을, entries() 메서드는 첫 번째 요소가 키이고 두 번째 요소가 값인 배열을 각각 반환 한다. 이들 메서드가 반환 하는 것은 모두 이터러블 객체이므로 [for...of](https://goo.gl/QJS81J) 루프를 쓸 수 있다.
* [이터러블 객체](https://goo.gl/9HeANk): 반복가능한 객체(예 : 문자열이나 파일을 반복 할 수 있음)
```
    for(let u of userRoles.keys())
        console.log(u.name);

    for(let r of userRoles.values())
        console.log(r);

    for(let ur of userRoles.entries())
        console.log(`${ur[0].name}: ${ur[1]}`);
    
    //맵도 분해(destruct)할 수 있다.
    //위 코드는 다음과 같이 단축해서 쓸 수 있다.
    for(let [u,r] of userRoles)
        console.log(`${u.name}: ${r}`);
```
11.이터러블 객체보다 배열이 필요하다면 확산 연산자(spread operator)를 쓰면 된다.
```
    [...userRoles.values()];        //["User","User","Admin"]
```
12.맵의 요소를 지울 때는 delete() 메서드를 사용한다.
```
    userRoles.delete(u2);
    userRoles.size;         //2
```
13.맵의 요소를 모두 지울 때는 clear() 메서드를 사용한다.
```
    userRoles.clear();
    userRoles.size;         //0
```

>>예제
```
    var sayings = new Map();
    sayings.set("dog", "woof");
    sayings.set("cat", "meow");
    sayings.set("elephant", "toot");
    sayings.size; // 3
    sayings.get("fox"); // undefined
    sayings.has("bird"); // false
    sayings.delete("dog");

    for (var [key, value] of sayings) {
    console.log(key + " goes " + value);
    }
    // "cat goes meow"
    // "elephant goes toot"
```
### Object(객체)와 Map 비교
전통적으로 objects 는 문자열을 값에 매핑하는 데 사용되었다. Object는 키를 값으로 설정하고, 값을 검색하고, 키를 삭제하고, 키에 저장된 내용을 검색 할 수 있게 만들어준다. 그러나 Map 객체는 더 나은 맵이 되도록 하는 몇 가지 장점을 가지고 있다.
* Object의 키는 Strings이며, Map의 키는 모든 값을 가질 수 있다.
* Object는 크기를 수동으로 추적해야하지만, Map은 크기를 쉽게 얻을 수 있다.
* Map은 삽입된 순서대로 반복된다.
* 객체(Object)에는 prototype이 있어 Map에 기본 키들이 있다. 

Object 혹은 Map중에 어느 것을 사용할지를 결정하는데 도움을 줄 두가지 팁
* 실행 시까지 키를 알수 없고, 모든 키가 동일한 type이며 모든 값들이 동일한 type일 경우에는 objects를  대신해서 map을 사용해라. 
* 각 개별 요소에 대해 적용해야 하는 로직이 있을 경우에는 objects를 사용해라. 

## 10.2 WeakMap(위크맵)
WeakMap은 다음 차이점을 제외하면 Map과 완전히 같다.

* 키는 반드시 객체여야 한다.
* WeakMap의 키는 [가비지 콜렉션](https://goo.gl/RKJmPN)에 포함될 수 있다.
* WeakMap의 키들은 열거형이 아니다.(즉, 키 목록을 제공해 주는 메서드가 없다는 것이다.)

WeakMap객체는  object만을 키로 허용하고 값은 임의의 값을 허용하는  키/값 형태의 요소의 집합이다. 키가 가지고 있는 객체에 대한 참조는 객체에 대한 참조가 더이상 존재하지 않을 경우 garbage collection(GC)의 수거 대상이 되는 약한 참조를 의미한다.

WeakMap의 이런 특징은 객체 인스턴스의 전용(private)키를 저장하기에 알맞는다.
```
    const SecretHolder = (function(){
        const secrets = new WeakMap();
        return class {
            setSecret(secret) {
                secrets.set(this, secret);
            }
            getSecret() {
                return secrets.get(this);
            }
        }
    })();
```
앞의 예제에서는 WeakMap과 그 위크맵을 사용하는 클래스를 함께 [IIFE](https://goo.gl/V7USWa)(즉시 실행 함수 표현)에 넣었다. IIFE 외부에서는 그 인스턴스에 비밀스런 내용을 저장할 수 있는  SecretHolder 클래스를 얻게 된다. 비밀을 저장할 때는 setSecret 메서드를 써야만 하고, 보려 할 때는 getSecret 메서드를 써야만 한다.
```
    const a = new SecretHolder();
    const b = new SecretHolder();

    a.setSecret('secret A');
    b.setSecret('secret B');

    a.getSecret();      //"secret A"
    b.getSecret();      //"secret B"
```
일반적인 Map을 써도 되지만, 그렇게 하면 SecretHolder인스턴스에 저장한 내용은 가비지 콜렉션에 포함되지 않는다.

>>예제
```
    var wm1 = new WeakMap(),
        wm2 = new WeakMap(),
        wm3 = new WeakMap();
    var o1 = {},
        o2 = function(){},
        o3 = window;

    wm1.set(o1, 37);
    wm1.set(o2, "azerty");
    wm2.set(o1, o2);        // 값은 무엇이든 될 수 있음, 객체 또는 함수 포함
    wm2.set(o3, undefined);
    wm2.set(wm1, wm2);      // 키와 값은 어떤 객체든 될 수 있음. 심지어 WeakMap도!

    wm1.get(o2);            // "azerty"
    wm2.get(o2);            // undefined, wm2에 o2에 대한 키가 없기에
    wm2.get(o3);            // undefined, 이게 설정값이기에

    wm1.has(o2);            // true
    wm2.has(o2);            // false
    wm2.has(o3);            // true (값 자체가 'undefined'이더라도)

    wm3.set(o1, 37);
    wm3.get(o1);            // 37

    wm1.has(o1);            // true
    wm1.delete(o1);
    wm1.has(o1);            // false
```
## 10.3 Set(셋)

셋은 값들의 집합이다. 입력된 순서에따라 저장된 요소를 반복처리할 수 있다. Set은 중복된 값을 허용하지 않는다. 따라서 특정 값은 Set내에서 하나만 존재 하게 된다.

1.먼저 Set 인스턴스를 만들자.
```
    const roles = new Set();
```
2.사용자 역할을 추가할 때는 add() 메서드를 사용한다.
```
    roles.add("User");      //Set [ "User" ]
```
3.사용자에게 관리자 역할을 추가하려면 add()를 다시 호출한다.
```
    roles.add("Admin");     //Set [ "User","Admin" ]
```
4.Map과 마찬가지로 Set에도 size 프로퍼티가 있다.
```
    roles.size      //2
```
5.셋의 장점은 아주 단순하다. 추가하려는 것이 셋에 이미 있는지 확인할 필요가 없다. 이미 있다면 아무 일도 일어나지 않는다.
```
    roles.add("User");      //Set ["User","Admin"]
    roles.size;            //2
```
6.역할을 제거할 때는 delete()를 호출한다. 제거에 성공했다면, 즉 그런 역할이 셋에 존재했다면 true를 반환하고, 그렇지 않다면 false를 반환한다.
```
     roles.delete("Admin");         //true
     roles;
     roles.delete("Admin");         //false
```
>>예제
```
    var mySet = new Set();
    mySet.add(1);
    mySet.add("some text");
    mySet.add("foo");

    mySet.has(1); // true
    mySet.delete("foo");
    mySet.size; // 2

    for (let item of mySet) console.log(item);
    // 1
    // "some text"
```
###  Array(배열)과 Set비교
일반적으로 Javascript에선 배열에 특정 요소의 집합을 저장한다. 하지만 Set객체는 몇가지 이점을 제공한다.

* [indexOf](https://goo.gl/5wBmpP)메서드를 사용하여 배열내에 특정 요소가 존재하는지 확인하는 것은 느리다.
* 배열에선 해당 요소를 배열에서 잘라내야 하는 반면 Set객체는 요소의 값으로 해당 요소를 삭제하는 기능 제공한다.
* [NaN](https://goo.gl/5DR4Pg)은 배열에서 indexOf메서드로 찾을 수 없다
* Set객체는 값의 유일성을 보장하기 때문에 직접 요소의 중복성을 확인할 필요가 없다.

## 10.4 WeakSet(위크셋)
WeakSet객체는 객체를 저장하는 일종의 집합이다. WeakSet내의 중복된 객체는 없으며 WeakSet내의 요소를 열거할 수는 없다.  

Set과 가장 큰 차이점은 다음과 같다.

* Set과는 다르게 WeakSet은 객체의 집합이며 객체만 저장할 수 있다. 특정 type의 값을 저장할 수는 없다. 
* WeakSet은 약한 참조를 가진다. WeakSet내의 객체에 대한 참조는 약하게 연결이 되어 있다. WeakSet내에 저장되어 있는 객체에 대한 참조가 없게되면 garbage collection 대상이되어 수거 된다. 따라서 현재 저장되어 있는 객체에 대한 목록은 없으며 WeakSet은 열거형이 아니다.

WeakSet객체의 사용 사례는 제한되어 있다. 메모리 누수가 발생되지 않기때문에 안전하게 DOM요소를 키로 저장할 수 있고 예를 들면 추적을 위해 DOM요소들을 WeakSet에 저장할 수 있다. 
```
    var ws = new WeakSet();
    var obj = {};
    var foo = {};

    ws.add(window);
    ws.add(obj);

    ws.has(window); // true
    ws.has(foo);    // false, foo가 집합에 추가되지 않았음

    ws.delete(window); // 집합에서 window 제거함
    ws.has(window);    // false, window가 제거되었음

```
## 요약

map 간단한 키와 값을 서로 연결 시켜 저장하며 저장된 순서대로 각 요소드를 반복적으로 접근할수 있는 객체

set(); 사용자 역할(값)을 할당

get(); 역할(값)을 확인

has(); 맵에 키가 존재하는지 확인

set(); 값을 교체

.size; 맵의 요소 숫자 확인

keys() 맵의 키를 확인

values() 맵의 값을 확인

entries() 첫 번쨰 요소가 키 이고 두 번째 요소가 값인 배열을 각각 반환

delete(); 맵의 요소를 삭제

clear(); 맵의 요소를 모두 삭제

set 값들의 집합이며 입력된 순서에 따라 저장된 요소를 반복처리 하고 특정 값은 Set내에서 하나만 존재 하는것

add(); 역할을 추가할때 사용
