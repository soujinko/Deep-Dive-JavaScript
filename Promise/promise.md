
# Promise?
JS에서 제공하는 비동기 코드를 간편하게 처리할 수 있도록 도와주는 object   
state: pending(보류)->fulfilled(이행) or rejected(거부)  
Producer vs Consumer  

* * *

### Producer(생산자)
프로미스가 생성되면 executer라는 콜백함수가 자동으로 실행됨.  
예를들어 사용자가 버튼눌러 서버요청을 하게해야하는 구조라면  
프로미스 활용하여 코드작성 시 불필요한 서버요청이 되는 것 주의해야 한다.

    const promise = new Promise((resolve, reject) => {console.log('ddd');
    setTimeout(()=> {
    resolve('sujin');
    reject(new Error('no network'));
    }, 2000);
    });


### Consumer (소비자)
then, catch, finally

    promise.then(value => {
    console.log(value)
    });
    .catch(error => {
    console.log(error);
    });
    .finally(()=> {
    console.log('finally');
    });


### Promise chaining
then은 리턴으로 Promise를 전달해도 된다.

    const fetchNumber = new Promise ((resolve, reject) => {
    setTimeout(() => resolve(1), 1000);
    });
    fetchNumber
    .then(num => num*2)
    .then(num => num*3)
    .then(num => {
        return new Promise((resolve, reject) => {
            setTimeout(() => resolve(num-1), 1000);
            });
    })
    .then(num => console.log(num));


    #catch를 이용하여 전체적인 Promise 체인에 문제가 발생하지 않도록 오류 처리 

    const getHen = () => 
        new Promise((resolve, reject) => {
            setTimeout(() => resolve('꼬꼬'), 1000);
    });

    const getEgg = hen =>
        new Promise((resolve, reject) => {
            setTimeout(()=> reject(new Error(`error! ${hen} => 달걀`)), 1000);
        });

    const cook = egg =>
        new Promise((resolve, reject) => {
            setTimeout(() => resolve(`${egg} => 후라이!`), 1000);
        });

    getHen()
    .then(getEgg)
    .catch(error => {
        return '대신';
    })
    .then(cook)
    .then(console.log)
    .catch(console.log)


참조
[드림코딩by앨리]](https://www.youtube.com/watch?v=JB_yU6Oe2eE).