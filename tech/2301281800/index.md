---
layout: post
type: tech
date: 2023-01-28 18:00
category: Others
title: Tip) 우분투 부팅 시 메일로 알림받기
subtitle: 원격지 서버 부팅 & 재부팅을 편하게 확인하고 싶을 때, 메일로 부팅 알림설정하기
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2301281800/img/bg.png
hash-tag: [Others, Tip, Ubuntu, Mailutils, Mail, Server]
draft: false
---

원격 서버를 재부팅하는 경우가 왕왕 있습니다. 그런데 원격지의 서버를 재부팅했을 때, 언제 재부팅이 완료되는지 직접 확인하기가 어렵습니다. 또한, 어떠한 오류로 원격 서버가 스스로 재부팅하기도 합니다. 이처럼 부팅이 발생할 경우, 메일로 이를 보고받도록 하겠습니다.

## 보낼 메일 테스트 발송

부팅 시 보내질 메일을 미리 작성하고 테스트 발송해봅니다.

```bash
$ echo "서버가 부팅되었습니다." | mail -s "Boot Alarm" {받을 사람}@{메일 주소} -aFrom:boot@alarm
```

`mailutils`가 설치되어 있지 않다면 아래의 글을 참고해주세요.

[우분투 mailutils 설치 및 메일 발송](/tech/2301281600/)

메일이 정상적으로 받아졌다면 이제 이를 시스템 시작 스크립트로 등록합니다.

`/etc/init.d`에 위치한 스크립트는 시스템 시작과 함께 자동으로 실행됩니다.

따라서 이 위치에 스크립트를 생성하고, 위의 명령을 복사합니다.

```bash
$ sudo vi /etc/init.d/bootalarm.sh
```

파일을 저장하고, 권한을 설정합니다.

```bash
$ sudo chmod 775 /etc/init.d/bootalarm.sh
```

이를 자동실행에 등록합니다.

```bash
$ sudo update-rc.d bootalarm.sh defaults
```

재부팅을 하여 메일이 오는지 확인합니다

```bash
$ sudo reboot
```

메일이 오면 성공입니다.
