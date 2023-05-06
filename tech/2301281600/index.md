---
layout: post
type: tech
date: 2023-01-28 16:00
category: Others
title: Tip) 우분투 mailutils 설치 및 메일 발송
subtitle: mailutils 세팅하고 메일 보내기
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2301281600/img/bg.png
hash-tag: [Others, Tip, Ubuntu, Mailutils, Mail, Server]
draft: false
---

# Mailutils

우분투에서 메일을 발송하는 다양한 방법이 있습니다.

본 포스트에서는 `mailutils`를 설치하고, 간단한 설정으로 메일을 발송해보겠습니다.

먼저 `mailutils`를 설치합니다

```bash
$ sudo apt install mailutils
```

만약 설치 시에 `E: Sub-process /usr/bin/dpkg returned an error code (1)` 에러가 발생하면 아래의 포스트를 참고해주세요

[E: Sub-process /usr/bin/dpkg returned an error code (1) 발생할 때](/tech/2301281530/)

이제 메일을 발송해봅니다. `user_to`와 `email_to`에는 받을 사람의 이메일 주소를 알맞게 넣어주면 되고, `user_from`과 `email_from`에는 보내는 사람의 메일 주소를 넣어주는데 이는 실제 존재하는 메일이 아니어도 괜찮습니다.

```bash
$ echo "message" | mail -s "test" {user_to}@{email_to} -aFrom:{user_from}@{email_from}
```

만약 명령 실행 후 메일이 바로 도착하지 않으면 로그를 확인해봅니다. 에러가 명시적으로 표시된 경우는 `/var/log/mail.err`를, 명시적으로 표시되지 않은 경우는 warning이므로 `/var/log/mail.log`를 확인합니다.

필자는 최초 실행 시 `postfix/main.cf`에 hostname 오류가 발생하여 수정하였습니다.

그럼에도 메일 발생이 되지 않았습니다.

```bash
$ sudo tail /var/log/mail.log
```

확인 결과 아래의 warning이 있었습니다.

```bash
Jan 28 10:05:40 {host} postfix/postdrop[2487]: warning: unable to look up public/pickup: No such file or directory
```

이와 같은 warning이 발생할 경우, `mkfifo`로 fifo파일을 생성합니다.

```bash
$ sudo mkfifo /var/spool/postfix/public/pickup
```

이후 postfix를 재실행한 후 메일을 발송합니다.

```bash
sudo service postfix restart
```

메일이 정상적으로 발송되는 것을 확인했습니다.
