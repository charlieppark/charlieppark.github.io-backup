---
layout: post
type: tech
date: 2023-01-28 15:30
category: Others
title: "Tip) E: Sub-process /usr/bin/dpkg returned an error code (1) 발생할 때"
subtitle: mailutils 세팅하고 메일 보내기
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2301281530/img/bg.png
hash-tag: [Others, Tip, Ubuntu, dpkg, error, Server]
draft: false
---

이러한 에러는 보통 특정 패키지의 문제와 함께 발생합니다.

필자의 경우는 mailutils 설치시에 아래의 에러가 발생했습니다.

```bash
Errors were encountered while processing:
 postfix
E: Sub-process /usr/bin/dpkg returned an error code (1)
```

이때, -f 옵션을 사용하는 등의 방법으로 강제 설치를 진행할 수도 있고, `dpkg configure`를 수행하는 방법도 있으나, 이러한 방법은 해결이 안되는 경우가 많습니다.

따라서 아래의 명령으로 에러를 발생시키는 패키지를 `/var/lib/dpkg/info` 에서 제거하고, 설치를 진행하면 문제가 해결됩니다.

```bash
sudo rm /var/lib/dpkg/info/{에러 발생 패키지명}.*
sudo apt install {설치할 패키지명}
```
