# TypeScript에서의 infer

> Type-Challenge를 풀면서 모르는 문제에 대해 답을 봤는데 infer를 사용하는 경우를 봤는데 infer가 정확히 무엇인지 잘 몰라서 정리하게 되었다.

조건부 타입의 조건식이 참으로 평가될 때에는 infer 키워드를 사용할 수 있다. 예를 들어서, `Element<number> extends Element<infer U>`와 같이 타입을 작성하면 U 타입은 number 타입으로 추론된다. 그리고 참인 경우에 대응되는 식에서 추론한 U 타입을 사용할 수 있다.

즉, U가 추론 가능한 타입이면 참이고 그렇지 않으면 거짓이다.

`infer` 키워드는 `제약 조건 extends`가 아닌 `조건부 타입 extends 절`에서만 사용이 가능하다. 다시 말해서, 조건부 타입 절을 벗어나면 유용하게 키워드를 사용할 수 없을 것 같다.

## 기본 구조

```tsx
T extends infer U ? X : Y;
```

## 예제1: ReturnType 유틸리티 타입

```tsx
// 유틸리티 타입
type _ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;

function fn(num: number): string {
    return num.toString();
}

const a: _ReturnType<typeof fn> = 'Hello';
console.log(a); // Hello
```

-   **위 예제의 핵심은 ReturnType 이라는 유틸리티 타입이 infer 키워드를 통해 구현되었다는 점이다.**
-   만약에 infer를 사용하지 않고 리턴 타입을 string으로 강제했다고 가정해보면, fn 함수의 리턴이 string이 아닌 boolean, number가 추가된다면 추가될 때마다 수정을 해야하기 때문에 유연하지 않은 코드가 된다.
-   그래서 infer를 사용해서 유연하게 코드를 작성하는 것이다. (infer를 통해 타입을 추론시킴)

## 예제2: Promise 객체안의 타입 꺼내기

Promise 객체 안에 있는 값의 타입을 편하게 꺼내려고 하는 경우 infer 키워드를 사용하면 런타임에서 결정되는 타입을 쉽게 정의할 수 있다.

```tsx
// 1번예제
type UnpackPromiseArray<P> = P extends Promise<infer K>[] ? K : any;

const arr = [Promise.resolve(true), Promise.resolve(1), Promise.resolve('asdf')]; // (Promise<booelan> | Promise<number> | Promise<string>)[]

type ExpectedBoolean = UnpackPromiseArray<typeof arr>; // boolean | number | string
```

-   P가 제네릭이기 때문에 제네릭에서의 조건부 타입은 분산적으로 이루어진다.
-   실제로 불어보면 다음과 같을 것이다.

```tsx
type TestResult = Promise<boolean> extends Promise<infer K>[]
    ? K
    : any | Promise<number> extends Promise<infer K>[]
    ? K
    : any | Promise<string> extends Promise<infer K>[]
    ? K
    : any;
```

```tsx
// 2번예제
type PromiseType<T> = T extends Promise<infer U> ? U : never;

type A = PromiseType<Promise<number>>; // number
type B = PromiseType<Promise<string | boolean>>; // string | boolean
type C = PromiseType<number>; // never
```

## 예제3: Tuple 다루기

예를 들어, `[string, number, boolean]`과 같은 튜플 타입에서 `[number, boolean]`과 같은 부분만 가져오고 싶은 상황이 있다고 가정해보자. 우리는 Conditional Type과 Variadic Tuple Type을 활용해서 구현할 수 있다.

```tsx
type TailOf<T> = T extends [unknown, ...infer U] ? U : [];

type A = TailOf<[string, boolean, number]>;

const a: A = [true, 123];
const a1: A = ['hello', 123]; // ERROR!
```

첫 요소인 string을 제외하고 `...infer U` 구문을 사용해서 나머지 boolean과 number 부분을 선택했다.
위의 구문을 함수를 통해서도 표현할 수 있다.

```tsx
function tailOf<T extends unknown[]>(arr: readonly [unknown, ...T]) {
    const [_ignored, ...rest] = arr;
    return rest;
}

type A = [string, boolean, number];

const myTailOf: A = ['hello', true, 123];
const testTailOf = tailOf(myTailOf);
console.log(testTailOf);
```

---

## 참고자료

-   [TypeScript Infer](https://velog.io/@from_numpy/TypeScript-infer)
