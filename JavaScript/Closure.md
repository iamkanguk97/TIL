# Closure (클로저)

> **매일메일 이라는 서비스를 구독하기 시작했다. 매일메일은 개발자 기술면접 관련해서 매일 오전7시에 메일로 면접 질문을 준다.**<br/>
> 클로저라고 한다면 프론트엔드 면접에서 질문할만한 주제이지만 필자는 JS기반 백엔드 개발자이기 때문에 충분히 알고 있어야 한다고 생각해서 TIL을 작성해보려고 한다.

## 정의

-   클로저는 함수가 선언될 때의 스코프를 기억해서 함수가 생성된 이후에도 그 스코프에 접근할 수 있는 기능을 말한다. 한마디로 **함수가 자신이 생성된 환경을 기억하는 것이라고 할 수 있다.**
-   클로저는 `자바스크립트의 함수가 일급 객체라는 특성`과 `렉시컬 스코프`의 조합으로 만들어진다.
    -   `일급 객체다?` => **값으로 평가될 수 있는 것!**
    -   `렉시컬 스코프` => **함수를 어디서 호출하는지가 아니라 어디에 선언하였는지에 따라 결정되는 것! => 함수를 어디서 선언하였는지에 따라 상위 스코프를 결정한다는 뜻이고 함수의 선언에 따라 결정된다는 점!**

### 렉시컬 스코프

```javascript
var x = 1; // global

function first() {
    var x = 10;
    second();
}

function second() {
    console.log(x);
}

first(); // ?
second(); // ?
```

-   정답은 `1 1`이 출력된다.
-   먼저 second 함수를 호출한 부분을 보면 second 함수는 지금 global 범위에 선언이 되어있기 때문에 global 범위에 있는 변수 x를 console.log 하였다. 그래서 결과가 1이다.
-   **이제 first 함수를 호출한 부분을 보면, first 함수 내에 지역변수로 x가 10이라고 선언했다. 그리고 second 함수를 호출했지만 결과값이 1이 나왔다.**
-   **즉, 함수를 어디서 호출했는지는 중요하지 않다는 뜻이다. 함수를 어디서 선언했는지가 중요하다는 것이다.**
    -   **함수를 어디서 호출했는지는 스코프 결정에 의미를 부여하지 않는다.**

## 예시 코드

```javascript
function outerFunction(outerVariable) {
    return function innerFunction(innerVariable) {
        console.log('Outer Variable': outerVariable);
        console.log('Inner Variable': innerVariable);
    }
}

const newFunc = outerFunction('outside');
newFunc('inside');
```

-   innerFunction은 outerFunction 함수 내부에 정의되어 있다. **그렇다면 innerFunction의 scope는 outerFunction 스코프일 것이다.**
-   **그래서 outerFunction을 호출하고 난 뒤에도 innerFunction에 접근할 수 있다는 것이고, innerFunction에서도 outerVariable에 접근할 수 있는 것이다.**

## 활용은 어디서 하는지?

> Java 진영은 잘 모르겠다. 그렇지만 Node 진영에서는 크게 Closure를 사용하는 것 같지는 않았다.<br/>
> 그래도 가끔 라이브러리 쪽 보면 closure가 적용된 부분도 적지 않게 봤던 것 같다.<br/>
> 보통 React에서 useState와 같은 메서드에서 closure가 적용되는 것으로 알고 있다.

-   변수나 함수의 접근 범위를 제어하고 특정 데이터와 상태를 유지하기 위해서 자주 활용된다.

### 데이터 은닉

-   외부에서 접근할 수 없는 비공개 변수와 함수를 만들 수 있다. 이를 통해서 데이터를 은닉해서 외부 접근을 막고 데이터 무결성을 유지할 수 있다.
-   예를 들어, 특정 함수 내부에서만 접근 가능한 변수를 생성하고 이를 조작할 수 있는 함수만 외부로 노출하여 안전하게 관리할 수 있다.

### 비동기 작업

-   비동기 작업에서 이전의 실행 컨텍스트를 유지해야 할 때 유용하다.
-   콜백 함수가 비동기적으로 실행될 때 클로저를 사용하면 함수 실행 시점의 변수를 참조할 수 있다.

```javascript
function createLogger(name) {
    return function () {
        console.log(`Logger: ${name}`);
    };
}

const logger = createLogger('myApp');
setTimeout(logger, 1000);
```

-   위 예시에서는 클로저가 name 변수인 myApp을 저장해서 1초 후에도 값이 유지되어 출력된다.

### 모듈 패턴

-   모듈 패턴: 특정 기능을 캡슐화하고 외부에 공개하고자 하는 부분만 선택적으로 노출해서 코드의 응집력을 높이고 유지보수성을 높이는 패턴.
-   클로저를 활용하면 필요한 함수와 데이터만 외부로 노출하면서 모듈 패턴을 쉽게 구현할 수 있다.

---

### 참고자료

-   https://velog.io/@heonys/JS-%ED%81%B4%EB%A1%9C%EC%A0%80%EC%99%80-%EB%AA%A8%EB%93%88%ED%8C%A8%ED%84%B4-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0
-   https://ljtaek2.tistory.com/145
