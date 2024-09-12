# @types 적용하기 (tsconfig - typeRoots)

> 회사에서는 Koa를 사용하고 있는데 타입 파일을 작성했지만 ctx.state.user에 타입이 적용되지 않고 있었다. 그래서 문제를 해결했는데 어떤식으로 해결했는지를 작성하려고 한다.
> 블로그에 쓸까 했지만 블로그까지는 쪼꼼 아닌 것 같아서!

## tsc 사용하기

-   `tsconfig.json`을 만들지 않아도 tsc를 그냥 사용할 수 있다.
-   `tsc`를 통해서 우리는 ts 파일을 js 파일로 컴파일할 수 있다.

tsconfig을 설정하는 이유는 명령어에 옵션을 주기가 힘들고 프로젝트에서 일정한 설정을 유지시키기 위해서이다.

## tsconfig.json

`VSCode`에는 기본적으로 TypeScript에 대한 Intellisense를 지원하는데 이 intellisense가 .ts 파일을 인식하는 방법을 제어하기 위해서는 `tsconfig.json`을 작성해야 한다.
여기서 주의할 점은 vscode의 intellisense와 tsc는 전혀 상관이 없다는 점이다.
vscode는 intellisense가 .ts 파일을 인식하는 방법을 제어하기 위해서 tsconfig.json을 사용하는 것이고, tsc 또한 typescript를 javascript로 컴파일하는 과정에서 tsconfig.json을 사용할 것이다.

vscode와 tsc는 분명하게 분리되어 있다. vscode의 경우 typescript project directory의 root에 반드시 tsconfig.json을 넣고 이것을 별도의 세팅을 통해서 위치를 설정하는 방법을 제공하지 않고 있지만 tsc의 경우는 --build 옵션을 통해서 원하는 설정 파일을 지정하게 할 수 있다.
**즉, vscode 에디터 상에는 에러가 나오지 않지만, tsc를 통해 컴파일을 시도하면 에러가 날 수도 있고 그 반대의 상황이 나올 수 있다.**

## tsconfig.json 설정하기

tsconfig.json을 설정하는 이유는 크게 2가지가 있다.

-   vscode의 intellisense가 typescript 처리하는 방법을 제어하기 위해서
-   tsc가 typescript를 컴파일하는 방법을 제어하기 위해서

1번의 경우는 vscode에서 코드를 작성할 때 잘못된 코드를 작성하게 하는 것을 방지한다.
2번의 경우는 동시에 실제 출력물의 형태에 대한 것도 포함된다.

tsc를 사용하는 결론적인 이유는 ts를 js로 변환하기 위해서이다. 그렇다면 tsconfig.json을 설정하는 이유는 전체 typescript 프로젝트 최종 결과물이 어떻게 변환되어 출력되는지를 결정하기 위해서이다.

우리가 원하는 것이 tsc를 통해서 실제 결과물을 원하는 것인지 아니면 단순히 vscode에서 가이드라인을 제시하는 것인지를 생각하면서 설정코드를 작성해야 한다.

## 해결방법: typeRoots 설정하기

-   `typeRoots`의 기본값은 ["node_modules/@types"] 이다.
-   TypeScript가 정의되어 있는 type을 찾는 공간이 된다.
-   추가적인 타입들을 정의한다면 별도의 type 디렉토리를 만들고 그 안에 `.d.ts` 파일을 만든 뒤 디렉터리를 typeRoots에 추가해주면 된다.
-   회사에서는 root 디렉토리로 `@types` 라는 디렉토리를 생성해서 사용하고 있었다.
-   include에 이미 포함된 곳이면 굳이 추가해줄 필요는 없다. include에 포함되어 있는 .d.ts 파일은 자동으로 TS가 인식하기 때문에 넣을 필요가 없다.

```json
{
    "compilerOptions": {
        "typeRoots": ["./node_modules/@types", "./@types"]
    }
}
```

-   추가로, `node_modules/@types`를 빼놓으면 기본으로 추가가 되지 않아서 문제가 발생할 수 있다.
-   `typeRoots`를 통해 지정한 type 경로는 일반적으로 외부 라이브러리가 제공하는 모듈의 타입을 정의하기 위해서 사용한다.
-   만약 외부 모듈을 가져오려고 할 때 "Couldn't find a declaration file for module" 이라는 에러가 발생하면 @types/xxx 패키지를 설치하거나 직접 정의를 해줘야 하는데 직접 정의하는 경우에만 이 경로로 작성하면 된다.
-   프로젝트 내의 타입을 정의하기 위해서 사용하지 말자 <- 이부분은 잘 이해가 되지 않는다!

---

## 참고자료

-   [TypeScript: TSConfig Reference - typeRoots](https://www.typescriptlang.org/tsconfig/#typeRoots)
-   [ {tsconfig.json} 제대로 알고 사용하기](https://velog.io/@sooran/tsconfig.json-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%95%8C%EA%B3%A0-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)
