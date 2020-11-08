---
title: "Jekyll Blog(Minimal Mistakes Theme)에 Utterances 댓글 기능 추가하기"
categories:
  - Jekyll
tags:
  - Jekyll
toc: true
toc_sticky: true
last_modified_at: 2020-11-08T14:05:00-05:00
---

## 1. Utterances

구글링을 하다 보면 GitHub 계정으로도 댓글을 달 수 있는 블로그들을 자주 볼 수 있다. Utterances라는 오픈 소스를 사용한 것인데, GitHub Issue 기반으로 Comment를 작성하게 해준다. 댓글에 마크다운 문법을 지원하며, 플랫폼을 이전해도 기존 댓글들을 그대로 가져갈 수 있다는 장점이 있다.

Jekyll에서는 Disqus 및 Facebook 등 다양한 댓글 라이브러리를 간편하게 사용할 수 있고, 나 역시 Disqus를 사용했다. 그런데 사람들이 댓글을 달려면 Disqus 회원가입 및 로그인 절차를 거쳐야 하기 때문에 다소 불편하다고 생각했다. Facebook도 좋은 대안이지만 비슷한 이유로 패스.

어차피 개발 블로그라서 대부분의 방문자가 GitHub 계정이 있을테니, Utterances가 가장 유용하다고 생각했다.

<br>

## 2. 설정

[App 설치하기](https://github.com/apps/utterances) 링크로 가서 GitHub App에 Utterances를 설치한다. 댓글이 등록되는 Issue들을 관리할 Repository에 권한을 제공해주면 된다.

**주의**할 점은, 해당 Repository가 반드시 Public이어야 한다. Private이면 타인이 댓글을 볼 수 없다. 나는 그냥 GitHub Page Repository에 권한을 제공했으나, GitHub Page가 Private이라면 별도의 Public Repository에 권한을 제공해야 한다.

> _config.yml

```yml
repository: xlffm3/xlffm3.github.io
# GitHub username/repo-name e.g. "mmistakes/minimal-mistakes"

...

comments:
  provider: "utterances"
  utterances:
    theme: "github-light" # "github-dark"
    issue_term: "pathname"
```

repository 부분은 본인의 username과 repo-name으로 수정해주고, comments 부분은 예제와 똑같이 설정하면 된다.

<br>


<br>

---

## Reference

* [Minimal Mistakes Configuration](https://mmistakes.github.io/minimal-mistakes/docs/configuration/#utterances-comments)
