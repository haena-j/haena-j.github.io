---
title: ubuntu-latest(ubuntu-22.04)로 인한 Git Actions Failure 발생
category: React
tag:
- javascript
- gitActions
- errors
---

잘되던 git actions 가 갑자기 failure 발생.

Git Actions workflow 파일의 ubuntu-latest 가 원인이였다.
```
jobs:
  build:
    runs-on: ubuntu-latest
```

ubuntu-22.04부터 openssl 3.0 을 사용, 현재 적용중인 prisma 3.12.0 버전에서는 openssl 3.0을 지원안하여 Git Actions fail이 뜨는 이슈가 발생한 것이다.

현재 사용하고있는 prisma 버전은 3.12.0 인데 3.13.0 부터는 지원한단다.
https://github.com/prisma/prisma/issues/11356

```
Error
Query engine library for current platform "debian-openssl-1.1.x" could not be found.
You incorrectly pinned it to debian-openssl-1.1.x

This probably happens, because you built Prisma Client on a different platform.
```


당장 build를 진행해야해서 일단 Git Actions workflow.yaml파일의 runs-on: ubuntu-latest 에서 ubuntu-20.04로 ubuntu 버전을 고정하여 처리했다.

github action 의 changelog 팔로업을 잘해야겠다.

참고문서
- https://github.blog/changelog/2022-11-09-github-actions-ubuntu-latest-workflows-will-use-ubuntu-22-04/
- https://github.com/prisma/prisma/issues/11356
