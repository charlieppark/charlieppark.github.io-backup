---
layout: post
type: tech
date: 2023-01-28 17:00
category: Others
title: Tip) 우분투 중요한 로그 메일로 전달받기
subtitle: 원격지 서버 로그를 logwatch를 사용하여 주기적으로 메일로 전달받기
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2301281700/img/bg.png
hash-tag: [Others, Tip, Ubuntu, Logwatch, Server]
draft: false
---

## logwatch

시스템의 정상 동작과 오류를 파악하기 위해 로그 분석은 매우 중요합니다. 그러나 로그의 양은 방대하고 분석하기 어려운 형태로 되어있으므로 로그 분석 툴을 사용하여 중요한 로그만 보고 받을 수 있습니다.

본 포스트에서는 `logwatch`로 중요한 로그를 걸러내고 이를 주기적으로 메일로 전달받는 것까지 해보겠습니다.

먼저 `logwatch`를 설치합니다.

```bash
sudo apt install logwatch
```

그리고 `/usr/share/logwatch/default.conf/logwatch.conf`를 열어 메일을 받을 주소를 설정합니다.

```bash
sudo vi /usr/share/logwatch/default.conf/logwatch.conf
```

설정 파일에서 `MailTo = {전달 받을 메일 주소}` 로 수정합니다.

만약 mail 보내는 프로그램을 mailutils의 mail로 설정하려면 아래의 과정을 거칩니다.

먼저 mail 명령의 경로를 확인합니다.

```bash
$ type mail
mail is /usr/bin/mail
```

다시 설정 파일로 들어가 `mailer = “/usr/bin/mail”` 로 수정합니다.

이후 테스트 메일을 발송해봅니다.

```bash
$ sudo logwatch --detail Med --mailto [chanho.park@dankook.ac.kr](mailto:chanho.park@dankook.ac.kr) --service all --range today
```

정상적으로 발송이 되면 성공입니다.
