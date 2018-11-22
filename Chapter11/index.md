# chapter11 예외와 에러처리
견고하고 품질좋은 소프트웨어를 만드는 첫번째 단계는 에러가 일어날 수 있다는 사실을 받아드리는 것, 두번째 단계는 에러를 예상하고 타당한 방법으로 처리하는 것.

예외처리는 에러를 컨트롤하는 매커니즘입니다.
에러처리라고 하지 않고 예외처리라고 하는 이유는 예상치 못한 상황에 대처하는 방식이기 때문입니다.

## 11.1 Error 객체
자바스크립트 내장된 Error 객체
```
const err = new Error('invalid email');
```

Error 인스턴스를 만드는 것만으로 아무 일도 일어나지 않는다.
이 인스턴스는 에러와 통신 수단이다.

```
function validateEmail(email){
    return email.match(/@/) ? //정규식 email에 '@' 있는지 체크
        email : 
        new Error(`invalid email : ${email}`);
}
```

이 함수를 사용할때는 instanceof 연산자를 써서 Error 인스턴스가 반환됐는지 확인.
에러메세지는 message 프로퍼티에 있습니다.

```
const email = 'jane@doe.com';

const validatedEmail = validateEmail(email);
if( validatedEmail instanceof Error ){
    console.error(`Error : ${validatedEmail.message}`);
}else{
    console.log(`valid email : ${validatedEmail}`)
}
```
이방법도 Error 인스턴스를 활용하는 유효하고 유용한 방법이긴 하지만,
<strong>Error 인스턴스는 예외처리</strong>에서 더 자주 사용됩니다.

## 11.2 try/catch 와 예외 처리
예외처리는 try...catch 문을 사용합니다.
뭔가를 시도하고(try) 예외가 있으면 그것을 캐치(catch) 한다는 뜻
예상치 못한 에러에 대처하려면 try...catch 문으로 전체 를 감쌀 수있습니다.

```
const email = null; //email에 아무것도 입력안하거나 특수문자 입력했을때 위의 코드로하면 에러 나올수있으므로.

try {
    //오류가 발생할 수 있는 문
    const validatedEmail = validateEmail(email);

    if(validatedEmail instanceof Error){
        console.error(`Error : ${validatedEmail.message}`)
    }else{
        console.log(`Valid email : ${validatedEmail}`);
    }
}catch(err){
    //선택적요소
    // try문의 오류를 처리하는 문
    console.error(`Error : ${err.message}`);
}
```
try 블록에는 오류를 가져올수 있는 코드를 포함.
catch 블록에는 일부 또는 모든 오류를 처리하는 코드 포함.
실행의 흐름은 에러가 일어나는 즉시 catch 블록 이동.
즉 validateEmail을 호출한 다음에 있는 if 문은 실행되지 않는다.
try 블록에서 오류가 발생하면 catch 블록으로 넘깁니다다. err 값은 try블록에서 발생한 오류값이다.
에러가 일어나지 않으면 catch 블록은 실행되지 않는다.


## 11.3 에러 일으키기
직접 에러를 일으켜서 예외 처리 작업을 할 수 있다.
자바스크립트는 에러를 일으킬 때 객체,숫자,문자열 어떤값이든 catch 절에 넘길 수 있다.
하지만 Error 인스턴스를 넘기는 것이 가장 편함.
```
function billPay(amount, payee, account){ // 찾는금액, 잔고
    if(amount > account.balance)
        throw new Error('insufficient funds');
    account.transfer(payee,amount);
}
```
throw를 호출하면서 현재 함수는 즉시 실행을 멈춘다.
account.transfer가 호출되지 않음.

## 11.4 예외 처리와 호출 스택
완료되지 않은 함수가 쌓이는 것을 호출스택(call stack) 이라고 한다.
Error 인스턴스에는 스펙을 문자열로 표현한 stack 프로퍼티가 있습니다.

```
function a(){
    console.log('a : calling b');
    b();
    console.log('a : done');
}

function b(){
    console.log('b : calling c');
    c();
    console.log('b : done');
}

function c(){
    console.log('c : throwing error');
    throw new Error('c error');
    console.log('c :done');
}

function d(){
    console.log('d : calling c');
    c();
    console.log('d : done');
}

try {
    a();
} catch(err){
    console.log(err.stack);
}

try {
    d();
} catch(err) {
    console.log(err.stack);
}

```
console 에서 @ 가 있는 행은 스택추정이며 ' 가장 깊은 함수 (c)'에서 시작하고 함수가 남지 않았을때 끝납니다.
스택추적 두가지가 나타나있습니다.
하나는 a가 b를, b가 c를 호출 했음을 보여주고 d에서 c를 호출 했음을 보여줍니다.

## 11.5 try...catch...finally
finally 블록은 에러가 일어나든, 일어나지 않든 반드시 호출된다.

```
try {
    console.log('this line is exeuted...');
    throw new Error('whoops');
    console.log('this line is not...');
} catch(err){
    console.log('there was an error...');
} finally{
    console.log('...always executed');
    console.log('perform cleanup here');
}

```

## 11.6 요약

예외는 catch 블록을 만날때 까지 스텍을 거슬러 올라가야하므로, 자주 실행되는 코드에서 예외를 발생시키면 성능 문제가 발생할 수 있습니다.
예외 처리는 예상 할 수 없는 상황에 대비한 마지노선으로 생각하고, 예상할 수 있는 에러는 조건문으로 처리하는 것이 최선입니다.
