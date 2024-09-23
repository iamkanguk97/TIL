# Index Signature과 unknown 타입

> Type-Challenge에서 Tuple To Object 문제를 복습하면서 Index Signature를 사용했는데 unknown 타입과 호환이 되지 않아 고생했던 경험이 있어 정리해둔다.

## 문제 상황

```tsx
type TupleToObject<T extends readonly unknown[]> = {
    [key in T[number]]: key;
    //      ~~~~~~~~~ Type 'T[number]' is not assignable to type 'string | number | symbol'
};

/* _____________ Test Cases _____________ */
import type { Equal, Expect } from '@type-challenges/utils';

const tuple = ['tesla', 'model 3', 'model X', 'model Y'] as const;
const tupleNumber = [1, 2, 3, 4] as const;
const sym1 = Symbol(1);
const sym2 = Symbol(2);
const tupleSymbol = [sym1, sym2] as const;
const tupleMix = [1, '2', 3, '4', sym1] as const;

type cases = [
    Expect<Equal<TupleToObject<typeof tuple>, { tesla: 'tesla'; 'model 3': 'model 3'; 'model X': 'model X'; 'model Y': 'model Y' }>>,
    Expect<Equal<TupleToObject<typeof tupleNumber>, { 1: 1; 2: 2; 3: 3; 4: 4 }>>,
    Expect<Equal<TupleToObject<typeof tupleSymbol>, { [sym1]: typeof sym1; [sym2]: typeof sym2 }>>,
    Expect<Equal<TupleToObject<typeof tupleMix>, { 1: 1; '2': '2'; 3: 3; '4': '4'; [sym1]: typeof sym1 }>>
];

// @ts-expect-error
type error = TupleToObject<[[1, 2], {}]>;
```

-   위의 코드에서 TupleToObject 타입을 보면 T에는 어떤 타입이 들어올 지 모르기 때문에 unknown 타입을 적용했다.
-   unknown 타입을 적용했더니 `T[number]` 부분에서 에러가 발생하고 있다.
-   해당 부분에서 왜 에러가 나는지 도저히 이해가 되지 않아서 레퍼런스를 찾아보았다.

## 궁금증 해결

> **Index Signature에서 key의 타입은 string, number, symbol, template-literal 타입만 가능하다는 점!**

```tsx
type userType = {
    // ERROR!
    [key: string | boolean]: string;
};
```

위의 예제처럼 key 타입을 string, number 타입이 아닌 다른 타입으로 선언하는 경우 에러가 발생한다.

### key 타입을 템플릿 리터럴로 선언하는 경우

```tsx
type userInfoType = 'name' | 'age' | 'address';

type userType = {
    [key in userInfoType]: string;
};

// OK
let user: userType = {
    name: '홍길동',
    age: '88',
    address: '행당동'
};

// ERROR!
let user2: userType = {
    이름: '홍길동',
    나이: 88,
    주소: '행당동'
};
```

-   위의 코드와 같이 in 키워드를 사용해서 인덱스 시그니처를 적용할 수 있다.

```tsx
type userType = {
    [key: string]: string;
};

let user: userType = {
    이름: '또치',
    나이: '38'
};

console.log(user.address); // undefined
```

위의 예제는 정상적으로 동작하지만, address는 undefined로 출력된다. 그래서 더욱 정확한 타입을 구현하기 위해서는 undefined가 가능하다는 것을 명시해주어야 한다.

```tsx
type userType = {
    [key: string]: string | undefined;
};

let user: userType = {
    이름: '또치',
    나이: '38'
};

console.log(user.address); // undefined
```

**추가적으로, 인덱스 시그니처는 동일한 key를 여러개 가질 수 없다.**

## 그래서 위의 코드를 어떻게 수정할까?

**먼저, 결론적으로 unknown 타입을 사용했을 때 에러가 나는 이유는 index-signature key로 string, number, symbol, template literal 타입 뿐만 아니라 함수, 객체 등 다양한 타입이 올 수 있다는 의미이기 때문에 에러를 뱉는 것이다.**

그래서 unknown 대신에 다른 타입을 적용시켜주어야 한다.

```tsx
type TupleToObject<T extends readonly any[]> = {
    [key in T[number]]: key;
};

/* _____________ Test Cases _____________ */
import type { Equal, Expect } from '@type-challenges/utils';

const tuple = ['tesla', 'model 3', 'model X', 'model Y'] as const;
const tupleNumber = [1, 2, 3, 4] as const;
const sym1 = Symbol(1);
const sym2 = Symbol(2);
const tupleSymbol = [sym1, sym2] as const;
const tupleMix = [1, '2', 3, '4', sym1] as const;

type cases = [
    Expect<Equal<TupleToObject<typeof tuple>, { tesla: 'tesla'; 'model 3': 'model 3'; 'model X': 'model X'; 'model Y': 'model Y' }>>,
    Expect<Equal<TupleToObject<typeof tupleNumber>, { 1: 1; 2: 2; 3: 3; 4: 4 }>>,
    Expect<Equal<TupleToObject<typeof tupleSymbol>, { [sym1]: typeof sym1; [sym2]: typeof sym2 }>>,
    Expect<Equal<TupleToObject<typeof tupleMix>, { 1: 1; '2': '2'; 3: 3; '4': '4'; [sym1]: typeof sym1 }>>
];

// @ts-expect-error
type error = TupleToObject<[[1, 2], {}]>;
```

사실 any 타입을 적용하면 문제가 해결되긴 하는데 필자는 any 타입을 사용하는 것이 마음에 들지 않는다. 왜냐하면 any도 결국에 모든 타입을 허용하기 때문에 타입 안전성을 보장할 수 없는 것이다. 다시 말해서 any 타입을 사용하면 문제는 해결되지만 Function, object와 같은 타입이 들어올 수 있는 가능성이 남아있다는 것이다.

**그리고 any 타입은 TypeScript의 타입 검사를 우회하고, TS가 타입 검사를 포기하고 JS처럼 동작하기 때문에 개발자가 경고를 확인할 수 없다.**

그래서 필자는 어떤 방법을 선택했냐?

```tsx
type IndexSignatureKey = string | number | symbol;

type TupleToObject<T extends readonly IndexSignatureKey[]> = {
    [key in T[number]]: key;
};

/* _____________ Test Cases _____________ */
import type { Equal, Expect } from '@type-challenges/utils';

const tuple = ['tesla', 'model 3', 'model X', 'model Y'] as const;
const tupleNumber = [1, 2, 3, 4] as const;
const sym1 = Symbol(1);
const sym2 = Symbol(2);
const tupleSymbol = [sym1, sym2] as const;
const tupleMix = [1, '2', 3, '4', sym1] as const;

type cases = [
    Expect<Equal<TupleToObject<typeof tuple>, { tesla: 'tesla'; 'model 3': 'model 3'; 'model X': 'model X'; 'model Y': 'model Y' }>>,
    Expect<Equal<TupleToObject<typeof tupleNumber>, { 1: 1; 2: 2; 3: 3; 4: 4 }>>,
    Expect<Equal<TupleToObject<typeof tupleSymbol>, { [sym1]: typeof sym1; [sym2]: typeof sym2 }>>,
    Expect<Equal<TupleToObject<typeof tupleMix>, { 1: 1; '2': '2'; 3: 3; '4': '4'; [sym1]: typeof sym1 }>>
];

// @ts-expect-error
type error = TupleToObject<[[1, 2], {}]>;
```

어짜피 인덱스 시그니처에서 키는 `IndexSignatureKey`로 커버가 가능하기 때문에 다음과 같이 코드를 작성했다.
하지만.... 더욱 좋은걸 발견했으니!

```tsx
type TupleToObject<T extends readonly PropertyKey[]> = {
    [key in T[number]]: key;
};
```

`PropertyKey`는 애초에 Property의 key로 올 수 있는 타입이라고 생각하면 된다. PropertyKey는 위에서 구현한대로 `string | number | symbol`로 구현되어 있다.
이건 꿀팁!

---

## 참고자료

-   [[TypeScript] 인덱스 시그니처](https://velog.io/@ahsy92/TypeScript-%EC%9D%B8%EB%8D%B1%EC%8A%A4-%EC%8B%9C%EA%B7%B8%EB%8B%88%EC%B2%98)
-   [The `PropertyKey` Type](https://www.totaltypescript.com/concepts/propertykey-type)
