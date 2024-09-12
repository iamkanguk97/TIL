# Git Flow와 GitHub Flow 그리고 Trunk-based Development

## Introduction

본래 우리 회사 개발팀에서는 팀장님께서 DB만 거의 관리하시고 사실상 API 개발하는 팀원은 나 포함 신입 2명뿐이었다. 그래서 그런지 팀장님께서는 SSH로 직접 원격 서버에 접속해서 개발을 하면 된다고 말씀하셨다. 하지만 이게 과연 맞는 방법일까 많은 생각을 해봤다.

약 2개월간 근무중인데 맞지 않는 방법이라고 확실하게 생각이 들게 된 계기가 있었다. 백엔드 서버가 오류가 나면 프론트엔드 분들은 작업을 아예 하지 못한다는 점이었다.<br/>
좋은 기회로 이번에 신규 서비스 출시의 학습자 도메인의 벡엔드 부분을 맡게 되었다. 그리고 같이 입사하신 신입분은 선생님 도메인의 백엔드 부분을 맡게 되었다.

어제 팀장님이랑 얘기를 할 기회가 있었는데 소심한 성격이 있지만 나름 나의 의견을 정리해서 말씀드렸다. `"이제는 동료분이랑 같이 협업을 하게 되었으니 원격 서버에 같이 접속해서 작업은 못한다. 최소한 Git이라도 도입해서 분리를 시켜야한다."`

처음에는 Git FLow를 말씀드렸지만, 팀장님께서는 왜 굳이 분기를 하나 더 추가해서 개발 비용을 늘리냐 라는 말씀을 하셨다. 그래서 GitHub Flow 전략으로 갈 것 같은데 아직은 조금 두루뭉실하다. 그래도 나는 뿌듯하다. Git이라도 도입해서 개발이 100 힘들 것을 한 7-80? 힘들게 한 것 같아서..

이번 기회로 Git Flow와 GitHub Flow, 그리고 Trunk-Based Development에 대해서 알아보려고 한다.

## Branch란?

브랜치는 독립적으로 어떤 작업을 진행하기 위해 도입된 개념이다. 만들어진 각각의 브랜치는 다른 브랜치의 영향을 받지 않아서 여러 작업을 동시에 진행할 수 있다.

보통 개발자들이 협업을 진행할 때 동일한 소스코드를 함께 공유하고 다룬다. 어떤 사람은 버그를 수정하고 어떤 사람은 기능을 개발한다. 동일한 코드를 여러 사람이 다른 작업을 할 때 서로 다른 버전의 코드가 만들어지게 되는데 이 때 동시에 여러 작업을 할 수 있도록 브랜치를 사용하는 것이다.

## Git Flow

## Trunk-based Development

Git-Flow의 어려움에 대응하는 전략이라고 알려져있다.

## GitHub Flow

<img src="https://github.com/user-attachments/assets/5bb7f701-a186-4fb3-99f5-523df7649632" />

> - Git-Flow가 GitHub에서 사용하기에는 복잡하다고 나온 브랜치 전략이다.
> - HotFix 브랜치나 Feature 브랜치를 분기하지 않는다. 우선순위만 다르다고 생각하면 된다.
> - 수시로 배포가 일어나고, CI와 배포가 자동화되어있는 프로젝트에서는 유용하다.

## GitLab Flow

<img src="https://github.com/user-attachments/assets/6e4afba8-81a1-46a2-ac40-dd28897d259a" />

- GitLab에는 Production 브랜치가 있는데, Git-Flow의 Master 브랜치와 동일하다.
- GitLab-Flow의 Master 브랜치는 Production 브랜치로 병합한다. Production 브랜치는 배포만을 담당한다.
- Pre-Production 브랜치는 Production 브랜치로 결과를 넘기기 전에 테스트를 수행하는 브랜치이다.
- Production 브랜치에서 Release된 코드가 항상 프로젝트의 최신버전 상태를 유지해야할 필요가 없다는 장점이 있다.
- 복잡한 Git-Flow와 너무 간단한 GitHub-Flow의 절충안이다.

> - **신규 Branch에서 소스롤 Commit하고 Push한다. 기능 구현이 완료되면 Master로 PR을 보낸다. 그리고 Merge되면 신규 브랜치는 삭제한다. (신규 브랜치 = Feature 브랜치)**
> - **Master 브랜치에서는 Feature 브랜치에서 병합된 기능에 대해서 테스트를 진행한다. 테스트 이후 기능에 대해 보장이 되었다면 Production 브랜치로 PR한다. 하지만, Staging 단계가 필요하다면 Pre-Production 브랜치로 PR한다.**
> - **Production 브랜치는 Git-Flow의 Master 브랜치와 동일하다고 생각하자. 테스트가 끝난 기능에 대해 배포를 하기 위한 브랜치이다.**

## 결론

---

### Reference

- [Git branch 전략(Git-Flow, GitHub-Flow, GitLab-Flow)](https://velog.io/@kw2577/Git-branch-%EC%A0%84%EB%9E%B5)
- [[밍글] GitHub 브랜치 전략 도입하기](https://velog.io/@tanggu01/%EB%B0%8D%EA%B8%80-GitHub-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EC%A0%84%EB%9E%B5-%EB%8F%84%EC%9E%85%ED%95%98%EA%B8%B0)
- [[Git] GitLab-Flow](https://velog.io/@jhchoi94/Git-GitLab-flow)
