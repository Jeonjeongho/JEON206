## 객체지향 언어(OOP)와 함수형 언어(FP)

### 객체와 객체지향 프로그래밍(OOP)
객체지향 프로그래밍(Object Oriented Programming)은 문제를 여러 개의 객체 단위로 나눠 작업하는 방식을 말합니다. 

이 방식은 오늘날 가장 많이 사용하는 대표적인 프로그래밍 방식이고 JAVA, C# 등이 대표적인 객체지향 프로그래밍 언어입니다.  

#### 객체지향 프로그래밍 특징 및 장점
객체지향 프로그래밍의 가장 큰 특징은 클래스를 이용해 연관 있는 처리부분(함수)과 데이터 부분(변수)를 하나로 묶어 객체(인스턴스)를 생성해 사용한다는 점입니다. 

그리고 객체지향 프로그래밍에서는 프로젝트를 독립적인 객체 단위로 분리해서 작업할 수 있기 때문에  여러 개발자와 협업해 규모가 큰 프로젝트를 진행할 수 있으며 유지보수측면도 뛰어나다는 장점이 있습니다.

```
    function Point(x,y){ this.x = x; this.y = y; };
    point1 = new Point(5,5);
    
    var person = {
        // 속성
        name: '홍길동',
        gender: '남자',
        
        // 메소드
        eat: function(food) {
           alert(this.name + '이 ' + food + '을/를 먹습니다.');
        },
        getGender: function() {
           alert(this.name + '은 ' + this.gender + '입니다.');
        }
    }; 
    
    person.eat('밥');
    person.getGender();
```

#### for ... in
객체 프로퍼티를 나열할 할때 for ... in을 주로 사용합니다. 문자열 프로퍼티가 몇개 있고 심볼 프로퍼티가 하나 있는 객체가 있다고 합시다.
```
     const SYM = Symbol();
     const o = {
        a : 1,
        b : 2,
        c : 3,
        [SYM] : 4,
     }
    
    for(let prop in o) {
        if (!o.hasOwnProperty(prop)) continue; 
        console.log(`${prop}: ${o[prop]}`);
    }
    
```
<small>for ...in을 배열에 사용하면 해당 배열에 인덱스 값이 나옵니다. 배열에는 for 루프나 forEach를 사용하십시오. </small>

#### Object.keys
Object.keys는 객체에서 나열 가능한 문자열 프로퍼티를 배열로 반환합니다.
```
    const SYM = Symbol();
    const o = {
    a : 1,
    b : 2,
    c : 3,
    [SYM] : 4,
    }
    Object.keys(o).forEach(prop => console.log(`${prop}: ${o[prop]}`))
```
Object.keys는 hasOwnProperty를 체크할 필요는 없습니다. 
예를 들어 객체에서 x로 시작하는 프로퍼티를 모두 가져온다면 다음과 같이 할 수 있습니다.
```
    const o = {
    	apple : 1,
    	xochitl : 2,
    	balloon : 3,
    	guitar : 4,
    	xylophone : 5,
    };

    Object.keys(o)
    	.filter(prop => prop.match(/^x/))
    	.forEach(prop => console.log(`${prop} : ${o[prop]}`));
```
<small>위에 같이 메소드를 연결해 쓰는 방법을 메소드 체이닝패턴 이라고 부른다. 위 방법은 함수형 자바스크립트에서 자세히 나옵니다.</small>


### 객체지향 프로그래밍 
객체지향 프로그래밍(OOP)은 컴퓨터 과학에서 전통적인 패러다임입니다.
OOP를 공부하기전 기본적으로 클래스에 지식이 있어야합니다. 
클래스는 자바스크립트는 프로토타입 기반(prototype-based) 객체지향형 언어다. 비록 다른 객체지향 언어들과의 차이점에 대한 논쟁들이 있긴 하지만, 자바스크립트는 강력한 객체지향 프로그래밍 능력들을 지니고 있다.

#### 1. 선언법
```
    class Tapmenu {
        constructor(link, width) {
            this.link = link;
            this.width = width;
        }
    }
    
    var tapmenu = new Tapmenu('#', 150);
    console.log(tapmenu);
```
함수를 선언할 때 첫 글자를 대문자로 설정하는데 이를 생성자(constructor) 함수라 하며, new 키워드로 새로운 객체를 생성할 수 있고, this를 사용해 생성자 함수로 생성될 객체의 속성을 지정한다.
클래스 선언은 함수 선언과 달리 호이스팅 되지 않습니다.


#### 2. 메소드 정의
ES6의 class에서, 클래스의 몸통(body)는 {} 이며, class 메소드는 class의 {} 안에 정의해야 합니다.
* 생성자(constructor)
    - 생성자 메소드는 객체의 생성과 초기화를 하는 특별한 메소드입니다. 클래스에서 constructor 이름을 갖는 메소드는 하나여야 합니다.
   ```
    // es6
    class Tapmenu {
        constructor(link, width) {
            this.link = link;
            this.width = width;
        }
    }
    
    // es3
    function Tapmenu(link, width) {
        this.link = link;
        this.width = width;
    }
   ```
* 프로토타입 메소드(Prototype methods)
    - 기본적인 메소드
    ```
    // es6
    class Tapmenu {
        constructor(link, width) {
            this.link = link;
            this.width = width;
        }
        
        wrap() {
            console.log('wrap');
        }
        
        list() {
            console.log(this);    
        }
    }
    
    // es3
    function Tapmenu(link, width) {
        this.link = link;
        this.width = width;
    }
    
    Tapmenu.prototype.wrap = function() {
        console.log('wrap');
    }
    
    Tapmenu.prototype.list = function() {
        console.log(this);    
    }
    ```
* 정적 메소드(static methods)
    - static 메소드는 클래스의 인스턴스 (var a = new testFunc()) 필요없이 호출 가능합니다. 또한 클래스의 인스턴스에서 static 메소드를 호출 할 수 없습니다.
    ```
    class Tapmenu {
        constructor(link, width) {
            this.link = link;
            this.width = width;
        }
        
        wrap() {
            console.log('wrap');
        }
        
        list() {
            console.log(this);    
        }

        static distance() {
            return 'no porp';
        }

        static blank(a, b) {
            return a + b;
        }
    }
    
    Tapmenu.distance() // "no porp";
    Tapmenu.blank(1,2) // 3
    ```
* getter
    - 클래스 프로퍼티에 접근할 때마다 클래스 프로퍼티의 값을 조작하는 행위가 필요할 때 사용한다.  
      getter는 이름 그대로 무언가를 취득할 때 사용하므로 반드시 무언가를 반환해야 한다. 사용 방법은 아래와 같다.
      ```
      class Tapmenu {
          constructor(link, width) {
              this.link = link;
              this.width = width;
          }
          
          wrap() {
              console.log('wrap');
          }
          
          list() {
              console.log(this);    
          }
  
          static distance() {
              return 'no porp';
          }
  
          static blank(a, b) {
              return a + b;
          }
          
          get firstElem() {
            return this.link ? link : "#";
          }
      }
      
      var menu = new Tapmenu();
      menu.firstElem
      ```
* setter
    -  setter는 클래스 프로퍼티에 값을 할당할 때마다 클래스 프로퍼티의 값을 조작하는 행위가 필요할 때 사용한다. 
       setter는 호출하는 것이 아니라 프로퍼티처럼 값을 할당하는 형식으로 사용하며 할당 시에 메소드가 호출된다. 사용 방법은 아래와 같다..
    ```
        class Tapmenu {
          constructor(link, width) {
              this.link = link;
              this.width = width;
              this.arr = [];
          }
          
          wrap() {
              console.log('wrap');
          }
          
          list() {
              console.log(this);    
          }
        
          static distance() {
              return 'no porp';
          }
        
          static blank(a, b) {
              return a + b;
          }
          
          get firstElem() {
            return this.link ? link : "#";
          }
          
          set lastElem(elem) {
            this.arr = [elem, this.width];
          }
        }
        
        var menu = new Tapmenu("https://www.naver.com/", 150);
        menu.lastElem = 150;
        console.log(menu.arr); // [150, 150]
    ```   
    
#### 3. 프로퍼티 보안
클래스로 작업시 보안이 필요할 때가 있습니다. 아래 예제와 같이 .연산자로 접근하면 클래스에 생성자 함수에 있는 프로퍼티에 접근해 변경이 가능하다. 그래서 예제처럼 접근하면 안되는 프로퍼티 이름앞에 밑줄을 붙이는 소위 '가짜 접근 제한'을 사용했습니다. 하지만 이 방법은 임시 방편입니다.
프로퍼티를 꼭 보호해야 한다면 스코프를 이용해 보호하는 WeakMap 인스턴스(10장에서 설명합니다)를 사용할 수 있습니다. 
```
    {
        class bike {
            constructor(make, model) {
                this.make = make;
                this.model = model;
                this._userGears = ['P','N','R','D'];
                this._userGear = this._userGears[0];
            }
    
            gears (type) {
                this._userGears = type ? this._userGears[type] : "P";
            }
        }
    
        const lode = new bike();
        // lode.gears(1);
        // console.log(lode._userGear)
        // lode._userGear = "N"
        // console.log(lode._userGear);
    };
    
    {
        const Car = (function() {
    
            const carProps = new WeakMap();
            const carProps_back = new WeakMap();
            class Car {
                constructor(make, model) {
                    this.make = make;
                    this.model = model;
                    this._userGears = ['P','N','R','D'];
                    //this._userGear = this._userGears[0];
                    carProps.set(this, {_userGear: this._userGears[0]});
                    carProps_back.set(this, {_userGear_back: this._userGears[0]});
                }
    
                gears (type) {
                    //console.log(this)
                    console.log(carProps.get(this)._userGear);
                    this._userGear = type ? this._userGears[type] : "P";
                }
    
                gears_change (type) {
                    console.log(carProps.get(this)._userGear);
                    carProps.get(this)._userGear = this._userGears[type];
                    console.log(carProps.get(this)._userGear);
                }
            }
            return Car;
        })();
        const lode = new Car();
        //console.log(lode);
        //lode.gears(1);
        //console.log(lode)
        //lode.gears_change(2);
        // console.log(lode._userGears);
        console.log(lode._userGear);
        lode.gears_change(1);
    };

```

#### 4. 프로토타입
클래스의 인스턴스에서 사용할 수 있는 메서드는 프로토타입 메서드를 말하는 겁니다. 예를 들어 Car의 인스턴스에서 사용할 수 있는 shift 메서드는 프로토타입 메서드 입니다. 이제 프로토타입이 무엇인지, 자바스크립트가 프로토타입 체인을 통해 어떻게 동적 피스패치를 구현하는지 알아봅시다.
일반 함수에는 protortpe이라는 특별한 내장 프로퍼티가 있습니다. 객체 생성자로 동작하는 함수에서는 프로토타입이 대단히 중요합니다.

<small>객체 생성자. 즉 클래스는 Car처럼 항상 첫 글자를 대문자로 표기합니다. 암묵적인 약속입니다. 자바스크립트에서 이런 표기법을 요구하는 것은 아니지만 일반적인 함수 이름이 대문자로 생성하거나 객체 생성자가 소문자로 시작하면 이를 경고하는 린트 프로그램이 많습니다.

ESLint ES + Lint입니다. ES는 EcmaScript<br>
ESLint는 자바스크립트 문법 중 에러가 있는 곳에 표시를 달아놓는 도구를 의미합니다.<br>
사용법은 npm을 보시면 됩니다.
</small>
```
    class Car {
        constructor(make, model) {
            this.make = make;
            this.model = model;
            this.userGears = ['P', 'N', 'R', 'D'];
            this.userGear = this.userGears[0];
        }

        shift(gear = 0) {
            if(this.userGears.indexOf(gear) < 0)
                throw new Error(`Invalid gear: ${gear}`);
            this.userGear = gear;
            console.log(this.userGear);
        }
    }

    var newCar1 = new Car('Test', 'sonata');
    //console.log(newCar1.shift);
    console.log(newCar1);
    // console.log(new Car);
    newCar1.shift = function(gear) {
        this.userGear = gear.toUpperCase();
    };
    console.log(newCar1);
    // console.log(newCar1.shift);
    // console.log(newCar1.shift == Car.prototype.shift);

    //
    // console.log(newCar1.shift);
    // console.log(Car.prototype.shift);
    //
    // Car.prototype.shift = function (gear) {
    //     this.userGear = gear.toUpperCase();
    // };
    // console.log(newCar1.shift);
    // console.log(Car.prototype.shift);
    // console.log(Car.prototype.shift == newCar1.shift);

```
함수의 protortpe 프로퍼티가 생성,중요해지는 시점은 new 키워드로 새 인스턴스롤 만들었을 때입니다. new 키워드로 만든 새 객체는 생성자의 prototype 프로퍼티에 접근할 수 있으며 객체 인스턴스는 ₩__proto__₩ 프로퍼티에 저장합니다.
프로토타입에서 중요한거는 동적 디스패치라는 메커니즘입니다.  여기서 디스패치는 메서드 호출과 같은 의미입니다.<br>
인스턴스에서 메서드나 프로퍼티를 정의하면 프로토타입에 있는 것을 가리느 효과가 있습니다.
위 예제는 동정 디스패치를 어떻게 구현하는지 잘 보여줍니다. car1 객체에는 shift 메서드가 없지만 호출하면 car1의 프로토타입에서 그런 메서드를 검색합니다.

#### 5. 정적 메서드
클래스에는 인스턴스 메서드 외에도 정적 메서드(클래스 메서드)가 있습니다.  정적 메서드에서 this는 인스턴스가 아니라 클래스 자체에 묶입니다. 
```
    {
        class Car {
            constructor(make, model) {
                this.make = make;
                this.model = model;
                this.nextVin = 0;
            }

            static getNextCin() {
                console.log(this)
                return this.nextVin++;
            }

            shift() {
                console.log(this)
            
        }
        Car.getNextCin()

        var car1 = new Car();
        car1.shift()
    }

```
#### 6. 상속
우리는 앞서 프로토타입에 동적 메서드를 봤습니다. 인스턴스 메서드 -> 프로토타입 <br>
클래서에서 상속은 인스턴스는 클래스의 기능을 모두 상속합니다. 삭속은 한 단계로 끝나지 않습니다. 
인스턴스 -> 프로토타입 -> 프로토타입을 검색합니다. 이것을 프로토타입 체인이라고 하며 체인은 이런식으로 만들어집니다. 만약 조건에 만족하는 프로토타입을 못찾으면 에러를 일으킵니다. 
상속은 단방향입니다. 
```
    {
        class Car {
            constructor(make, model) {
                this.make = make;
                this.model = model;
                this.nextVin = 0;
                this.passengers = [];
            }

            static getNextCin() {
                console.log(this)
                return this.nextVin++;
            }

            shift() {
                console.log(this)
            }

            addPassenger(p) {
                this.passengers.push(p);
            }

        }

        class Sonata extends Car {
            constructor(make, option) {
                super(make);
                this.option = option;
            }

            change() {

            }
        }

        var car1 = new Car('Test', 'sonata');
        var sonata = new Sonata('sonata_suv', 'Test');
        console.log(car1)
        console.log(sonata);
        sonata.addPassenger("Alice");
        sonata.addPassenger("Cameron");
        console.log(sonata.passengers);
    }
```
* extends
    - extends 키워드는 부모 클래스(base class)를 상속받는 자식 클래스(sub class)를 정의할 때 사용한다. 
* super
    - super 키워드는 부모 클래스를 참조(Reference)할 때 또는 부모 클래스의 constructor를 호출할 때 사용한다.
    
#### 7. 다향성
다향성이라는 단어는 객체지향 언어에서 여러 슈퍼클래스의 멤버인 인스턴스를 가르키는 말입니다.
자바스크립트 코드를 작성하다 보면 '이런 메서드가 있고 저런메서드가 있으니 아마 그 클래스의 인스턴스일 것이다' 처럼 짐작할 때가 많습니다. 그럴경우 사용하는 연산자가 있습니다. 
instanceof 연산자가 있습니다. 
```
    {
        class Car {
            constructor(make, model) {
                this.make = make;
                this.model = model;
                this.nextVin = 0;
                this.passengers = [];
            }
    
            static getNextCin() {
                console.log(this)
                return this.nextVin++;
            }
    
            shift() {
                console.log(this)
            }
    
            addPassenger(p) {
                this.passengers.push(p);
            }
    
        }
    
        class Sonata extends Car {
            constructor(option) {
                super();
                this.option = option;
            }
    
            change() {
    
            }
        }
    
        class vehicle extends Sonata {
            constructor(addoption) {
                super();
                this.addoption = addoption;
            }
        }
    
        class Ship {
            constructor(addoption) {
                this.addoption = addoption;
            }
        }
    
        var car1 = new Car('Test', 'sonata');
        var sonata = new Sonata('Test');
        var vehicle = new vehicle('addTest');
        var ship = new Ship('addTest');
        console.log(sonata instanceof Car);
        console.log(Ship instanceof Car);
    }
```

#### 8. 객체 프로퍼티 나열 다시보기
es6 클래스를 설계 의도대로 사용한다면 데이터 프로퍼티는 항상 프로토타입 체인이 아니라 인스턴스에 정의해야합니다.
하지만 그것을 강제로 막는 방법은 없으므로 확실히 확인하려면 항상 hasOwnProperty를 사용하는 편이 좋습니다.
Object.keys를 사용하면 별도의 조건 필요없이 프로퍼티를 나열하는 문제를 피할 수 있습니다.
```
    {
        class Car {
            constructor(make, model) {
                this.make = make;
                this.model = model;
                this.nextVin = 0;
                this.passengers = [];
            }

            static getNextCin() {
                console.log(this)
                return this.nextVin++;
            }

            shift() {
                console.log(this)
            }

            addPassenger(p) {
                this.passengers.push(p);
            }

        }

        class Sonata extends Car {
            constructor(option,make) {
                super(make);
                this.option = option;
            }

            change() {

            }
        }



        var car = new Car('Test', "sonata");
        var sonat = new Sonata('addTest','Test2');
        // Car.prototype.sneaky = 'not recommended!'
        // console.log(new Car());
        // car.__proto__.sneaky = 'not recommended!';
        // console.log(car);
        for(let p in sonat) {
            console.log(`${p}: ${sonat[p]}` + (sonat.hasOwnProperty(p) ? "" : "(inherited)"));
        }
        
        console.log(Object.keys(sonat))
    }

```

#### 9. 다중 상속, 믹스인 인터페이스
일부 객체지향 언어에서는 다중 상성이란 기능을 지원합니다.자바스크립트는 흥미로운 방식으로 이들을 절충했습니다. 자바스크립트는 프로토타입 체인에서 여러부모를 검색하지는 않으므로 단일 상속 언어라고 해야 하지만, 어떤 면에서는 다중 상속이나 인터페이스 보다 더 나은 방법을 제공합니다.

자바스크립트가 다중 상속이 필요한 문제에 대한 해답으로 내놓은 개념은 믹스인입니다.
믹스인이랑 기능을 필요한 만큼 섞어 놓은것입니다.
 ```
    function makeInsurable(o) {
        o.addInsurancePolicy = function(p) {
            this.insurancePolicy = p;
        }
        o.getInsurancePolicy = function() {
            return this.insurancePolicy;
        }
        o.isInsured = function() {
            return !!this.insurancePolicy;
        }
    }
 ```
이제 필요한 기능과 또 하나의 클래스를 추가합니다.

 ```
    class InsurancePolicy {
        constructor (member) {
            this.member = member;
        }

        total () {
            console.log('total');
        }
    }
    function makeInsurable(o) {
        o.addInsurancePolicy = function(p) {
            this.insurancePolicy = p;
        }
        o.getInsurancePolicy = function() {
            return this.insurancePolicy;
        }
        o.isInsured = function() {
            return !!this.insurancePolicy;
        }
    }
    class Car {
        constructor(make, model) {
            this.make = make;
            this.model = model;
            this.userGears = ['P', 'N', 'R', 'D'];
            this.userGear = this.userGears[0];
        }

        shift(gear = 0) {
            if(this.userGears.indexOf(gear) < 0)
                throw new Error(`Invalid gear: ${gear}`);
            this.userGear = gear;
            console.log(this.userGear);
        }
    }
    makeInsurable(Car.prototype);
    var car = new Car();
    car.addInsurancePolicy(new InsurancePolicy())
    console.log(car);
 ```
 위와 같이 정의를 하면 클래스가 정의된 것처럼 동작합니다. 
 물론 믹스인이 모든 문제를 해결해 주지는 않습니다. 부모 클래스에서 메서드가 겹친다면 코드가 꼬여 원하는 정보를 얻지 못할 수 있습니다. 
 
 심볼을 이용하면 이런문제 일부를 경감할 수 있습니다. 
 심볼을 항상 고유하므로 믹스인 클래스의 기능과 충돌할 가능성이 없습니다.
 ```
    class InsurancePolicy {
        constructor (member) {
            this.member = member;
        }

        total () {
            console.log('total');
        }
    }

    const ADD_POLICY = Symbol();
    const GET_POLICY = Symbol();
    const IS_POLICY = Symbol();
    const _POLICY = Symbol();
    function makeInsurable(o) {
        o[ADD_POLICY] = function(p) {
            this[_POLICY] = p;
        }

        o[GET_POLICY] = function() {
            return this[_POLICY];
        }

        o[IS_POLICY] = function() {
            return !!this[_POLICY];
        }

    }
    class Car {
        constructor(make, model) {
            this.make = make;
            this.model = model;
            this.userGears = ['P', 'N', 'R', 'D'];
            this.userGear = this.userGears[0];
        }

        shift(gear = 0) {
            if(this.userGears.indexOf(gear) < 0)
                throw new Error(`Invalid gear: ${gear}`);
            this.userGear = gear;
            console.log(this.userGear);
        }
    }
    makeInsurable(Car.prototype);
    var car = new Car();
    car[ADD_POLICY](new InsurancePolicy())
    console.log(car);
 ```
 
자바스크립트의 OOP는 매우 유연하며 강력합니다. 
위의 방법도 또 다른 방법을 보여드리겠습니다. 이 방법은 MDN사이트에 나와있는 방법이기도 합니다. 
```
    var calculatorMixin = function ( Base ) {
        return class extends Base {
            calc() { }

            randomize() {
                console.log('calculatorMixin');
            }
        };
    }

    var randomizerMixin = function ( Base ) {
        return class extends Base {
            randomize() {
                console.log('randomizerMixin');
            }
        };
    }

    class Foo {
        constructor(point, color) {
            this.point = point;
            this.color = color;
        }

        colorFn(changeColor) {
            this.color = changeColor ? changeColor : this.color;
        }
    }

    class Bar extends calculatorMixin(randomizerMixin(Foo)) {

    }

    const bar = new Bar();
    console.log(bar);
    bar.randomize();
```
 Foo 클래스가 randomizerMixin, calculatorMixin 두 개의 함수를 통과하면서 extends를 통해 함수 내의 클래스들이 상송됩니다..
 
 mixin 패턴을 쉽게 사용하기 위해 또다른 방법 사용해봅니다. 위의 코드를 그대로 두고 아래의 MixinBuilder, mix를 작성합니다.
```
    var calculatorMixin = function ( Base ) {
        return class Notmember extends Base {
            calc() { }
        };
    }

    var randomizerMixin = function ( Base ) {
        return class Member extends Base {
            randomize() { }
        };
    }
    class MixinBuilder {
        constructor(superclass) {
            this.superclass = superclass;
        }

        with(...mixins) {
            return mixins.reduce((c,mixins) => mixins(c), this.superclass);
        }
    }

    let mix = (superclass) => new MixinBuilder(superclass);


    class Foo {
        constructor(point, color) {
            this.point = point;
            this.color = color;
        }

        colorFn(changeColor) {
            this.color = changeColor ? changeColor : this.color;
        }
    };
    //console.log(Foo);
    class MyClass extends mix(Foo).with(calculatorMixin, randomizerMixin) {

    }
    //console.log(Foo);
    //console.log(new MyClass);
    console.log(new MyClass());
```
최초 작성했던 mixin 코드에서는 calculatorMixin(randomizerMixin(Foo)) 이런 방식으로 작성을 해야하지만 위의 코드를 이용하면 mix(Foo).with(calculatorMixin, randomizerMixin)이라는 보기 좋은 형태로 mixin을 사용할 수 있습니다.
 
 
### 함수형 언어(FP)
* 입출력이 순수해야합니다. (순수함수)
    - 출력이 순수하다는 것은 반드시 하나 이상의 인자를 받고, 받은 인자를 처리하여 반드시 결과물을 돌려주어야한다는 겁니다. 인자를 제외한 다른 변수는 사용하면 안 됩니다. 받은 인자만으로 결과물을 내어야 하죠. 이러한 함수를 순수함수라고 부릅니다. 
* 부작용(부산물)이 없어야합니다.
    - 부작용이 없어야한다는 것은, 프로그래머가 바꾸고자하는 변수 외에는 바뀌어서는 안 된다는 뜻입니다. 원본 데이터는 불변해야함을 기억하세요! 함수형 프로그래밍에서는 프로그래머가 모든 것을 예측하고 통제할 수 있어야합니다. 
* 함수와 데이터를 중점으로 생각합니다.

```
    var arr = [1, 2, 3, 4, 5];
    var map = arr.map(function(x) {
      return x * 2;
    }); // [2, 4, 6, 8, 10]
    
```
처음에 배열(arr)을 넣어서(입력), 결과(map)를 얻었습니다(출력). arr도 사용은 됐지만, 값은 변하지 않았고, map이라는 결과를 내고 아무런 부작용도 낳지 않았습니다. 바로 이런 게 함수형 프로그래밍에 적합한 함수, 순수함수입니다. 물론 이것은 배열의 메소드지만, 충분히 map이라는 함수를 따로 만들 수 있습니다.

### 함수형 반복문
함수형은 반복문을 사용하지 않습니다.
```
    var sum = 0;
    for (var i = 1; i <= 10; i++) {
        sum += i;
    }
    console.log(sum)

    function add(sum, count) {
        sum += count;
        if (count > 0) {
            return add(sum, count - 1);
        } else {
            return sum;
        }
    }
    console.log(add(0, 10));

```
add 안에서 add를 또 호출합니다.. 복잡해보이지만 한 번 실행 결과를 생각해봅니다. 이렇게 함수형으로 표현하면 장점이, 코드의 재사용성이 매우 높아진다는 겁니다. 한 번 함수로 만들어놓으면 언제든지 add 함수를 다시 쓸 수 있습니다.

### 함수형 메서드체이닝, 함수 체이닝
es6에는 함수형에 좋은 여러가지 api가 있습니다. 로대시, RxJS, 람다JS 간단한 예제를 보며 체이닝이 어떤 형태인지 확인하겠습니다.

#### 메서드 체이닝
메서드 체이닝은 여러 메서드를 단일 구문으로 호출하는 OOP 패턴입니다. 메서드가 모두 동일한 객체에 속해 있으면 메서드 흘리기라고도 합니다.
```
 console.log('Functional Programming'.substring(0, 10).toLowerCase() + 'is fun')
```
위 예제는 객체지향 프로그램에서 불편 객체에 많이 적용하는 패턴이지만 함수형 프로그래밍에도 잘 맞습니다.
위 예제는 원본과 무관한 문자열을 생성합니다. 그러므로 함수형에 잘 맞습니다.
정확한 함수형으로 리팩터링한 코드는 다음과 같습니다.
```
 concat(toLowerCase(substring('Functional Programming', 1, 10)), ' is fun')
```
매게변수는 무도 함수 선언부에 명시해서 부수효과를 없애고 원본 객체를 바꾸지않습니다.
가독성은 떨어지지만 위험요소는 없습니다.

#### 함수 체이닝
객체지향 프로그램은 주로 상속을 통해 코드를 재사용합니다. 함수는 함수를 제사용하며 함수 참조를 체이닝합니다.
```
    const number = 1;
    function boxFn1 (number) {
        return number + "function Chaining";
    }

    function boxFn2 (number) {
        return number++;
    }
    console.log(boxFn1(boxFn2(number)));
```


#### 함수형 라이브러리
* 로대시(lodash.js)<br>
과거 함수형 자바스크립트 프로그래머들이 널리 사용해왔고 백본JS 같은 주요 자바스크립트 프레임워크의 디펜던시인 언더스코어JS에서 갈라져 나온 유틸리티 라이브러리입니다. 모듈화 함수 체인을 구성할때 좋습니다.
```
_.defaults({ 'a': 1 }, { 'a': 3, 'b': 2 });
// → { 'a': 1, 'b': 2 }
_.partition([1, 2, 3, 4], n => n % 2);
// → [[1, 3], [2, 4]]
```
* 람다JS<br>
함수형 프로그래밍 전용 유틸리티 라이브러리로, 함수 파이프라인을 생성할 때 쓰기 편합니다.
```
import * as R from 'ramda'

const {identity} = R
R.map(identity, [1, 2, 3])
```

* RxJS<br>
RzJS는 리액티브 프로그래밍 패러다임을 구현한 라이브러리입니다. 옵저버/이터레이터 패턴과 함수형 프로그래밍에 비동기,이벤트 기반의 프로그램을 작성할때 유용합니다.
```
Rx.Observable.fromEvent(searchInputField, 'keyup') 
  .map(function (e) {return e.target.value;}) 
  .debounce(300) 
  .distinctUntilChanged() 
  .scan(function(prev, current) { 
        if (prev == null && current == originalValue) {
            return null; 
        } 
        return current; 
  }, null) 
  .filter(function(text) {return text != null}) 
```