---
layout: post
type: tech
date: 2023-01-27 20:00
category: Others
title: Tip) SSH 자동 로그인 및 명령어 단축 하기
subtitle: SSH Key 로그인과 alias를 사용하여 SSH 접속 단축 명령 만들기
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2301272000/img/bg.png
hash-tag: [Others, Tip, Ubuntu, ssh, alias, Server]
draft: false
---

# SSH 접속

일반적인 패스워드 방식의 SSH 접속은 아래와 같이 이뤄집니다

```bash
$ ssh {user}@{ip} -p {port num}
{user}@{ip}'s password: 비밀번호 입력
```

그러나 ~~강조되고~~ 반복되는 접속이 필요할 경우 ~~강아지를 불안하게 하기 때문에~~

생각보다 귀찮을 수 있습니다

따라서 이러한 과정을

1. SSH Key를 통한 자동 로그인
2. alias를 통한 명령어 단축

을 통해 간편하게 바꾸도록 하겠습니다

# SSH key 생성

SSH Key가 생성되어 있지 않다면 키를 생성해줍니다

```bash
$ ssh-keygen -t rsa
```

이때 key의 경로를 따로 지정하지 않았다면 `$HOME/.ssh` 아래에 공개키와 개인키가 생성됩니다.

공개키 암호 알고리즘의 작동 방식이 궁금하다면 아래의 글을 참고하세요.

[공개키 암호 알고리즘](http://wiki.hash.kr/index.php/%EA%B3%B5%EA%B0%9C%ED%82%A4_%EC%95%94%ED%98%B8_%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)

SSH key 경로에 파일이 생성되었는지 확인합니다.

```bash
$ ls -al $HOME/.ssh
drwx------  2 {user} {user} 4096 Oct 30 00:55 .
drwxr-xr-x 50 {user} {user} 4096 Jan 27 23:26 ..
-rw-------  1 {user} {user} 2622 Aug 25  2021 id_rsa
-rw-r--r--  1 {user} {user}  582 Aug 25  2021 id_rsa.pub
```

보안 상의 이유로 생성된 Key의 권한을 수정해주는 것을 권장합니다.

```bash
$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/id_rsa
$ chmod 644 ~/.ssh/id_rsa.pub
```

생성된 Key를 원격 서버에 전송해줍니다. 방법은 두가지가 있습니다

1. `ssh-copy-id` 사용

```bash
$ ssh-copy-id -i $HOME/.ssh/id_rsa.pub {user}@{ip}
```

2. `scp` 사용

```bash
$ scp -P {port num} $HOME/.ssh/id_rsa.pub {user}@{ip}:id_rsa.pub
```

ssh를 통해 서버로 접속한 후 아래의 명령어를 수행합니다.

```bash
$ cat $HOME/id_rsa.pub >> $HOME/.ssh/authorized_keys
```

만약 `$HOME/.ssh` 폴더가 없다면 아래의 명령어로 생성한 후 다시 위의 명령어를 수행합니다.

```bash
$ cd $HOME
$ mkdir .ssh
```

로컬로 이동합니다.

```bash
$ exit
```

ssh 접속을 테스트합니다.

```bash
$ ssh {user}@{ip} -p {port num}
```

비밀번호 입력 없이 접속이 가능하다면 성공입니다!

비밀번호가 여전히 필요하다면 원격 서버에서 공개키 인증을 활성화합니다

```bash
$ vi /etc/ssh/sshd_config
```

`PubkeyAuthentication` 를 찾아 `yes`로 바꿔줍니다. 기본값은 `yes`로 되어있습니다.

# SSH 접속 단축 명령으로 만들기

`alias`를 사용하여 SSH 접속 명령을 단축키로 지정합니다.

사용하는 shell의 설정 파일을 엽니다. bash라면 `bashrc`, zsh라면 `zshrc`와 같은 꼴입니다.

```bash
$ grep {user} /etc/passwd
{user}:x:1000:1000:,,,:/home/{user}:/usr/bin/zsh
```

현재 저는 zsh를 사용중이므로, `.zshrc`를 엽니다.

```bash
$ vi $HOME/.zshrc
```

`.zshrc` 맨 아래에 아래의 구문을 입력합니다.

```bash
alias {단축 명령}='ssh {user}@{ip} -p {port num}'
```

저장하고 종료합니다. 변경된 설정을 적용합니다.

```bash
$ source $HOME/.zshrc
```

`alias`를 실행해봅니다.

```bash
$ {단축 명령}
```

정상적으로 ssh 접속이 되면 성공입니다.
