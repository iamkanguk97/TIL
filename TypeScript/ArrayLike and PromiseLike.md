# ArrayLike와 PromiseLike 타입

> **Type-Challenge에서 Awaited 문제가 있었는데 Promise 안의 제네릭으로 구성된 타입을 꺼내는 문제이다. 해당 문제에서 PromiseLike를 사용했는데 정확히 무슨 타입인지 몰라서 정리해보려고 한다.**

## ArrayLike

```tsx
interface ArrayLike<T> {
    readonly length: number;
    readonly [n: number]: T;
}
```

-   Array 타입에는 우리가 일반적으로 잘 사용하고 있는 메서드가 정의되어 있는데 **`ArrayLike` 타입은 length 프로퍼티와 index로만 접근할 수 있도록 구현되어 있다.**
-   **이는 유사 배열 객체에서 가지고 있는 성격**이다. 배열처럼 순회할 수 있지만 그것만 가능한 객체이다. 대표적으로는 HTMLCollection과 arguments가 있다.

## Promise와 PromiseLike

### Promise

> **lib.es5.d.ts에는 then과 catch가, lib.2018.promise.d.ts에는 finally까지 추가되어 있다.**

```tsx
interface Promise<T> {
    /**
     * Attaches a callback that is invoked when the Promise is settled (fulfilled or rejected). The
     * resolved value cannot be modified from the callback.
     * @param onfinally The callback to execute when the Promise is settled (fulfilled or rejected).
     * @returns A Promise for the completion of the callback.
     */
    finally(onfinally?: (() => void) | undefined | null): Promise<T>;
}

interface Promise<T> {
    /**
     * Attaches callbacks for the resolution and/or rejection of the Promise.
     * @param onfulfilled The callback to execute when the Promise is resolved.
     * @param onrejected The callback to execute when the Promise is rejected.
     * @returns A Promise for the completion of which ever callback is executed.
     */
    then<TResult1 = T, TResult2 = never>(
        onfulfilled?: ((value: T) => TResult1 | PromiseLike<TResult1>) | undefined | null,
        onrejected?: ((reason: any) => TResult2 | PromiseLike<TResult2>) | undefined | null
    ): Promise<TResult1 | TResult2>;

    /**
     * Attaches a callback for only the rejection of the Promise.
     * @param onrejected The callback to execute when the Promise is rejected.
     * @returns A Promise for the completion of the callback.
     */
    catch<TResult = never>(onrejected?: ((reason: any) => TResult | PromiseLike<TResult>) | undefined | null): Promise<T | TResult>;
}
```

### PromiseLike

```tsx
interface PromiseLike<T> {
    /**
     * Attaches callbacks for the resolution and/or rejection of the Promise.
     * @param onfulfilled The callback to execute when the Promise is resolved.
     * @param onrejected The callback to execute when the Promise is rejected.
     * @returns A Promise for the completion of which ever callback is executed.
     */
    then<TResult1 = T, TResult2 = never>(
        onfulfilled?: ((value: T) => TResult1 | PromiseLike<TResult1>) | undefined | null,
        onrejected?: ((reason: any) => TResult2 | PromiseLike<TResult2>) | undefined | null
    ): PromiseLike<TResult1 | TResult2>;
}
```

-   `PromiseLike`에는 then만 있다.
-   Promise가 정식 스펙이 되기 전에 Promise를 구현하기 위한 다양한 라이브러리가 존재했음.
-   표준 이전에 나왔기 때문에 `catch` 구문 없이 Promise를 처리하고 있었고 타입스크립트는 이를 지원하기 위해서 PromiseLike 타입을 만든 것!
-   Promise 뿐만 아니라 표준 이전에 만들어진 라이브러리로 만들어진 Promise를 처리하기 위해 PromiseLike 타입을 추가하게 된 것이다.

## 그래서 Type-Challenge에서 무슨 문제였는지?

```tsx
type MyAwaited<T extends PromiseLike<any>> = T extends PromiseLike<infer R> ? (R extends PromiseLike<any> ? MyAwaited<R> : R) : never;

/* _____________ Test Cases _____________ */
import type { Equal, Expect } from '@type-challenges/utils';

type X = Promise<string>;
type Y = Promise<{ field: number }>;
type Z = Promise<Promise<string | number>>;
type Z1 = Promise<Promise<Promise<string | boolean>>>;
type T = { then: (onfulfilled: (arg: number) => any) => any };

type cases = [
    Expect<Equal<MyAwaited<X>, string>>,
    Expect<Equal<MyAwaited<Y>, { field: number }>>,
    Expect<Equal<MyAwaited<Z>, string | number>>,
    Expect<Equal<MyAwaited<Z1>, string | boolean>>,
    Expect<Equal<MyAwaited<T>, number>>
];
```

-   `type T`를 보면 then 메서드 유사 타입이 정해져있다. 이를 위해서 PromiseLike를 사용했다고 생각하자.

---

## 참고자료

-   [Array vs ArrayLike, Promise vs PromiseLike](https://yceffort.kr/2021/11/array-arraylike-promise-promiselike)
