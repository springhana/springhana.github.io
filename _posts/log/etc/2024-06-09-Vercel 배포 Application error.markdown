---
layout: post
related_posts:
  - /log/etc/2024-05-14-옵시디언 복사 붙여넣기.markdown
title: Vercel 배포 Application error
categories:
  - log
  - etc
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  Vercel 배포 Application error
---

* toc
{:toc}

혼자 토이 프로젝트를 진행하던 중에 Vercel에 간단하게 배포를 했다. 하지만 외부에서 데이터를 가져오는 컴포넌트에서는 Application error가 발생했다.

<img src="/assets/img/etc/application error.png" />

이유를 찾아보니 `dynamic`을 사용해서 처리해라, 쿠키를 지우고 다시해봐라 등 많은 시도를 했는데 해결되지 않았다.

그래서 콘솔에서 확인을 해보니 외부에서 가져오는 `url`이 이상해서 vercel 배포를 할 때 설정을 해준 `env`를 확인해봤는데 

<img src="/assets/img/etc/vercel 배포 env.png" />


위의 사진 같이 `BASE_URL`을 이상하게 설정해줬다.

만약 Application error같이 오류가 발생했을 때 콘솔을 확인해보자!

여러분들은 저같이 바보같은 실수 하지마세요ㅠㅠ