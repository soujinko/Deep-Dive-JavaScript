_모던자바스크립트 딥다이브 46강_
# async / await?
프로미스 기반으로 동작하는, 더 간편한 API를 제공하는 syntactic sugar   
프로미스의 then/catch/finally 후속 처리 메서드에 콜백함수 전달해서 비동기 처리   결과를 후속 처리할 필요 없이, 마치 동기 처리처럼 프로미스를 사용할 수 있다.  
즉, 후속 처리 메서드 없이 프로미스가 처리 결과를 반환하도록 구현 가능

* * *

### async 함수
언제나 프로미스를 반환한다. 
async 함수는 암묵적으로 반환값을 resolve하는 프로미스를 반환한다.

    async function fetch(){
        return 'ellie'
    }
    const user = fetch();
    console.log(user)

    ---> console에
    Promise { 'ellie' } 라고 찍힘!

클래스의 constructor 메서드는 async의 메서드가 될 수 없다.
클래스의 constructor 메서드는 인스턴스를 반환해야 하지만 async 함수는 언제나 프로미스를 반환해야 한다.

### await 키워드
프로미스가 settled 상태(비동기 처리 수행 상태)가 될 때까지 대기하다
settled 되면 프로미스가 resolve한 처리 결과를 반환한다. await 키워드는 반드시 promise 앞에 사용

### async / await
await을 사용하여 프로미스의 체이닝 중첩문제를 해결한다.

    async function getApple(){
        await delay(1000);
        return '사과';
    }

    async function getBanana(){
        await delay(1000);
        return '바나나';
    }

    function pickFruits(){
        return getApple().then(apple => {
            return getBanana().then(banana => `${apple} + ${banana}`);
        });
    }

    pickFruits().then(console.log);

다음 코드에서와 같이 다른 함수들에서 값을 받아와서 사용할 경우, then을 중첩되게 사용하며 값을 호출해야함.

위 코드를 다음과 같이 개선 가능

    async function pickFruits(){
        const apple = await getApple();
        const banana = await getBanana();
        return `${apple} + ${banana}`
    }

    pickFruits().then(console.log);

하지만 apple값과 banna값이 연관이 없다면 병렬적으로 실행하는 것이 효율적.

    async function pickFruits(){
        const applePromise = getApple();
        const bananaPromise = getBanana();

        const apple = await applePromise;
        const banana = await bananaPromise;
        return `${apple} + ${banana}`
    }

다음과 같이 프로미스로 미리 값을 가져오면
바로 값 리턴 가능

하지만 병렬적(동시다발적) 수행이 가능한 경우에는 promise API 사용하도록 한다.

promise.all
    function pickAllFruits(){
        return Promise.all([getApple(), getBanana()])
        .then(fruits => fruits.join(' + '));
    }

    pickFruits().then(console.log);
다음과 같이 프로미스 배열안에 함수를 동시에 실행시켜 값을 한번에 받을 수 있음

promis.race -> 가장 먼저 전달되는 값 리턴
    function pickOnlyOne(){
        return Promise.race([getApple(), getBanana()]);
    }

    pickOnlyOne().then(console.log);


### 에러 처리
에러는 호출자 방향으로 전파된다. 즉, 콜 스택의 아래 방향인 실행 컨텍스트가 푸시되기 직전에 푸시된 실행 컨텍스트 방향을 전파된다.
하지만 비동기 함수의 콜백 함수를 호출한 것은 비동기 함수가 아니기 때문에
try..catch 문으로 에러를 캐치할 수 없다. 

    try {
        setTimeout(() => { throw new Error('Error!'); }, 1000);
    } catch(e) {
        //에러 캐치하지 못함
        console.error('캐치한 에러', e)
    } 

async / await에서 에러 처리는 try..catch 문을 사용할 수 있다.  
콜백 함수를 인수로 전달받는 비동기 함수와 달리  
프로미스를 반환하는 비동기 함수는 명시적 호출이 가능하여 호출자가 명확하기 때문

    const foo = async () => {
        try {
            const url = '';
            const response = await fetch(url);
            const data = await response.json();
            console.log(data);
        } catch(error) {
            console.log(err);
        }
    };
    foo();

다음 예제에서 catch 문은 HTTP통신에서 발생한 네트워크 에러뿐 아니라 try 코드 블록 내의 모든 문에서 발생한 일반적 에러까지 캐치 가능하다.

async 함수 내에서 catch문을 사용해서 에러 처리를 하지 않으면 async 함수는 발생한 에러를 reject하는 프로미스를 반환한다.
따라서 async 함수를 호출하고 Promis.prototype.catch 후속 처리 메서드를 사용해 에러를 캐치할 수도 있다. 

    const foo = async () => {
        const url = '';
        const response = await fetch(url);
        const data = await response.json();
        console.log(data);
        return data;
    };
    foo()
    .then(console.log)
    .catch(console.error);


에러 핸들링?
[모던js튜토리얼]](https://ko.javascript.info/try-catch).

참조
[드림코딩by앨리]](https://www.youtube.com/watch?v=aoQSOZfz3vQ&t=929s).