# NestJS Hot Reload

> **서버는 보통 변경사항이 생기면 프로세스를 종료하고 다시 동작시켜야 변경사항이 반영되는 것으로 알고 있다. 물론 때마다 수동으로 할 수 있겠지만 그건 너무 불편하다. 그래서 보통 watch 옵션을 많이 사용하고는 했지만 Hot Reload 방법도 있으니 한번 알아보자!**

## watch 옵션

CLI를 통해 Nest 프로젝트를 생성하고 package.json을 바로 확인해보면 `"start:dev": "nest start --watch"` 라고 되어있는걸 볼 수 있다. 이 때 사용되는 watch 옵션을 의미한다.

## Hot Reload

watch 옵션을 사용해도 반영할 수 있지만 **프로젝트 전체를 재시작한다는 특징이 있다.**
그래서 작은 규모라면 별로 문제가 되지 않겠지만, 대규모의 프로젝트라면 프로젝트를 재시작하는데 문제가 있을 수 있을 것이다.

NestJS 공식문서에서는 변경된 파일만 번들링해서 변경사항을 적용하는 방법을 소개하고 있는데 그것이 `Hot Reload` 이다.
자세한 사용법은 공식문서나 외부 블로그를 통해 확인하자!

## 참고자료

-   [NestJS 공식문서 - Hot Reload](https://docs.nestjs.com/recipes/hot-reload)
-   [[NestJS] Hot Reload 설정하기](https://tasddc.tistory.com/83)
