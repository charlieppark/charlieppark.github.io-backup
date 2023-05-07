---
layout: post
type: tech
date: 2021-12-24 01:00
category: Others
title: 우당탕탕 서버 세팅기
subtitle: 미래의 뻘짓을 막기 위해, 서버 세팅 시 할 일 정리
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2112240100/img/bg.png
hash-tag: [Others, Tip, Ubuntu, GPU, Zshell, Configuration, Server]
draft: false
---

연구실에서 딥러닝에 사용할 서버를 구입했다

최초 사양으로는 GPU 1개만 장착했으나 미래의 확장성을 위해 서버로 구매했다.

납품처에서 기본 OS와 그래픽 카드 등 여러가지를 잘 세팅해서 줬으나, 어제의 내가 LVM 등을 손대다 멍청하게도 파티션을 잘못 건드려 결과적으로 모든 세팅을 날리게 되었다.

물론 어제 하루종일 세팅해놓은 부분도...

다행히 파티션 꼬인 것은 잘 되돌렸으나, 원래 파일시스템을 복구할 수 없어 새로 OS를 설치했다

이에 이 글에 초기 세팅과 관련된 내용을 정리해두려 한다. 우분투 서버나 원격 PC 초기 세팅에 도움이 되길 바란다. (미래의 나를 위해..)

(설치시 ssh는 자동으로 설치된 상황이다)

`Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64) `버전에서 진행하였다

&nbsp

&nbsp

1. `root`
   먼저 모든 작업의 편의성을 위해 root를 활성화 한다

```bash
sudo passwd root
```

비밀번호를 설정한 후

```bash
su -
```

명령어를 통해 root로 전환한다

이후 root 비활성화를 진행할 때까지의 모든 작업은 root에서 진행된다고 가정한다.

&nbsp

&nbsp

2. `apt update / apt upgrade`

설치 시에 apt 서버를 카카오 서버로 선택하지 않았다면 카카오 미러 서버로 변경해준다

apt 설치시에 속도가 훨씬 빠르다. 그럴리는 없겠지만 혹 카카오 서버가 터진다면 카이스트 서버나 우분투 공식 서버 등으로 변경해주면 된다.

```bash
sed -i 's/kr.archive.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list
```

```bash
apt update
apt upgrade
```

본격적인 설치 이전에 `apt update` 와 `upgrade`를 진행한다

&nbsp

&nbsp

3. `build-essential`, `net-tools` 설치

필자는 리눅스 사용 시에 `build-essential`에 포함된 프로그램을 안 쓰는 경우가 거의 없어서 꼭 초기에 설치를 해준다. 그러나 필요에 따라 제외해도 좋다

`net-tools`는 설치해주자. `ssh`부터 해서 원격 컴퓨터라면 자주 쓴다.

```bash
apt install build-essential
apt install net-tools
```

&nbsp

&nbsp

4. (ssh가 설치된 상태에서) 설정 변경

```bash
vi /etc/ssh/sshd_config
```

위 명령을 통해 `sshd config`를 열어준다
파일에서

`PermitRootLogin yes`
`PasswordAuthentication yes`

로 바꿔준다

`PermitRootLogin`은 필요하지 않다면 바로 no로 바꿔주자

추가적으로 22번 포트는 ssh로 너무 잘 알려진 포트라, 포트도 변경하는 편인데 이는 필수는 아니다

```bash
ifconfig

eno1:
inet <ip주소>  netmask ~~~~~

```

&nbsp

&nbsp

5. `system locale` 설정

영어만 쓸거라면 안 해줘도 된다

그러나 한국인이다 보니 어쩔 수 없이 한글이 들어가는 파일을 작성하게 될 수 밖에 없다

```bash
locale

LANG=en_US.UTF-8
LANGUAGE=
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=

locale -a

C
C.UTF-8
en_US.utf8
POSIX
```

locale로 현재 설정을 확인하고, locale -a로 설치된 locale을 확인한다

당연히 한국어 locale 설치가 필요한 상황

설치를 진행한다

```bash
apt install language-pack-ko -y
```

이후 선택에 따라 locale 설정을 달리할 수 있다

이 서버에서는 최종적으로 아래와 같이 설정했다 (한글 메시지가 너무 어색해서..)

```bash

$ locale

LANG=ko_KR.UTF-8
LANGUAGE=
LC_CTYPE="ko_KR.UTF-8"
LC_NUMERIC="ko_KR.UTF-8"
LC_TIME="ko_KR.UTF-8"
LC_COLLATE="ko_KR.UTF-8"
LC_MONETARY="ko_KR.UTF-8"
LC_MESSAGES=en_US.UTF-8
LC_PAPER="ko_KR.UTF-8"
LC_NAME="ko_KR.UTF-8"
LC_ADDRESS="ko_KR.UTF-8"
LC_TELEPHONE="ko_KR.UTF-8"
LC_MEASUREMENT="ko_KR.UTF-8"
LC_IDENTIFICATION="ko_KR.UTF-8"
LC_ALL=
```

`LANG`, `LANGUAGE`, `LC_ALL` 등을 `/etc/default/locale` 에서 수정하면 세팅 변경이 가능하다.

&nbsp

&nbsp

6. 시스템 시간 설정

```bash
$ date
Thu Dec 23 22:09:45 UTC 2021
```

`date` 명령어로 현재 시간을 조회하면 시간이 맞지 않는다

```bash
$ timedatectl set-timezone Asia/Seoul
```

위 명령어로 현재 시간대를 변경해준다

```bash
$ date
Fri Dec 24 07:12:01 KST 2021
```

올바른 결과가 출력된다

&nbsp

&nbsp

7. `zsh` 설치 및 기본 쉘 변경

먼저 root에서 zsh를 설치하고, 추후 유저별로 `oh-my-zsh`를 설치한다

```bash
$ apt install zsh
```

추후 유저를 생성할 때 기본 쉘이 zsh로 자동 설정 되도록 하기 위하여 `useradd`의 설정을 변경한다

```bash
$ useradd -D

GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/sh
SKEL=/etc/skel
CREATE_MAIL_SPOOL=no
```

현재의 세팅은 위와 같다

```bash
$ cat /etc/shells

# /etc/shells: valid login shells
/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/bin/dash
/usr/bin/dash
/usr/bin/tmux
/usr/bin/screen
/bin/zsh
/usr/bin/zsh
```

사용가능한 쉘 목록을 확인하고

```bash

$ vi /etc/default/useradd

```

에서 `SHELL=/bin/zsh` 로 변경한다

```bash
$ useradd -D

GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/zsh
SKEL=/etc/skel
CREATE_MAIL_SPOOL=no
```

제대로 변경되었음을 확인할 수 있다.

&nbsp

&nbsp

8. neo-vim + 유용한 툴 설치

`oh-my-zsh`, `powerlevel10k` 등의 편의성 세팅이나 `anaconda`, `cuda`, `cudnn`, `tensorflow` 등의 세팅을 하기 전, 계속 vi를 사용하기는 찝찝하니 `neo-vim`부터 설치한다. vim을 사용해도 좋으나, `neovim`에 여러 플러그인을 올려서 사용할 경우 상당한 편의성을 보장한다. 물론 현재 vscode를 통해 원격으로 접속하여 주로 사용하는데 사실 `neovim`까지 갈 일이 잘 없긴하다.

그럼에도 깔아주자

`neovim`, `tree`, `unzip`, `git`, `curl을` 한번에 설치하도록 하겠다

이 중에서 이미 설치했거나, 필요없는 것은 빼주도록 하자

```bash
apt install neovim tree unzip git curl -y
```

이후 기본 에디터를 설정한다. 기본 에디터로 정하고 싶은 에디터의 번호를 입력하면 된다. 이렇게 설정하면, 기본 에디터가 열리는 경우 `neovim`이 열린다.

```bash
$ sudo update-alternatives --config editor
There are 5 choices for the alternative editor (providing /usr/bin/editor).

  Selection    Path                Priority   Status
------------------------------------------------------------
  0            /bin/nano            40        auto mode
  1            /bin/ed             -100       manual mode
  2            /bin/nano            40        manual mode
  3            /usr/bin/nvim        30        manual mode
* 4            /usr/bin/vim.basic   30        manual mode
  5            /usr/bin/vim.tiny    15        manual mode

Press <enter> to keep the current choice[*], or type selection number: 3
update-alternatives: using /usr/bin/nvim to provide /usr/bin/editor (editor) in manual mode

```

&nbsp

&nbsp

9. root 비활성화

이제부터의 일은 root가 아니어도 큰 차이가 없기 때문에 root를 비활성화하려 한다. 그전에 root에서 진행했을 때 더 편리한 일들이 남아있다면 처리하도록 하자. hostname 변경이나 그런것들?

```bash
exit # root 로그아웃

$ sudo passwd -l root
[sudo] password:
passwd: password expiry information changed.

$ su -
Password:
su: Authentication failure
```

`passwd -l root`로 해제 후, `su - `로 확인했을 때 제대로 해제된 것을 확인할 수 있다

&nbsp

&nbsp

10. Anaconda3 설치

```bash
$ wget https://repo.anaconda.com/archive/Anaconda3-2021.11-Linux-x86_64.sh
$ chmod +x Anaconda3-2021.11-Linux-x86_64.sh
```

공용 경로에 설치를 진행하기 위해 sudo 로 실행한다

```
$ sudo ./Anaconda3-2021.11-Linux-x86_64.sh
```

위와 같이 진행하면 설치 스크립트가 작동하는데, 설치 시에 설치 경로를 `/usr/anaconda3` 와 같은 공용 경로로 지정한다

```bash
$ sudo groupadd anaconda
$ sudo chgrp -R anaconda /usr/anaconda3
$ sudo chmod 770 -R /usr/anaconda3
```

아나콘다를 사용할 그룹을 만들고, 그룹에 속하면 아나콘다를 사용할 수 있게 설정한다.

이후 모든 유저가 zsh 에서 아나콘다를 사용할 수 있도록, `zprofile`에 PATH를 추가한다

```
$ sudo vim /etc/zsh/zprofile
$ source /etc/zsh/zprofile
```

&nbsp

&nbsp

11. 유저 추가

실제 사용할 유저들을 추가한다

유저를 추가할 때 공통적으로 해야하는 작업은 다음과 같다

유저 추가 -> 비밀번호 설정 -> (sudo 권한 부여) -> (sudoers 파일에서 비밀번호 입력 제거) -> (아나콘다 그룹에 추가)

괄호 안의 것들은 상황에 따라 생략해도 좋다

`adduser`를 사용하면 유저 추가와 비밀번호 설정을 한 번에 진행하고, `useradd`를 사용하면 `passwd`로 비밀번호를 별도 지정하여야 한다

`adduser`로 진행하도록 하겠다

그러나 `adduser`는 기본 쉘이 zsh로 지정되어있지 않으므로, 옵션에 추가한다.

```bash
$ sudo adduser charlieppark -s /bin/zsh

Adding user `charlieppark' ...
Adding new group `charlieppark' (1002) ...
Adding new user `charlieppark' (1001) with group `charlieppark' ...
Creating home directory `/home/charlieppark' ...
Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for charlieppark
Enter the new value, or press ENTER for the default
        Full Name []:
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] Y
```

`sudo` 권한을 부여한다

```bash
$ sudo usermod -aG sudo charlieppark
```

`visudo`로 `sudo` 사용 시 패스워드를 필요하지 않도록 한다

```bashad
$ sudo visudo
```

`charlieppark ALL=(ALL) NOPASSWD: ALL`
위와 같은 내용을 추가한다

이후 유저를 아나콘다 그룹에 추가한다

```bash
$ sudo adduser charlieppark anaconda
```

해당 유저로 이동한 후 `oh-my-zsh`와 `powerlevel10k` 테마 설정을 진행하겠다.

&nbsp

&nbsp

12. `oh-my-zsh` / `powerlevel10k`

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

`oh-my-zsh` 깃헙 레포에서 제공하는 대로 설치하면 된다. 간단하다

이후 `powerlevel10k` 테마를 사용하기 위해, `powerlevel10k` 레포에 설치가 요구되는 폰트들을 다운받아 설치한다.

```bash
wget https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Regular.ttf
wget https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold.ttf
wget https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Italic.ttf
wget https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold%20Italic.ttf
sudo mkdir /usr/share/fonts
sudo cp MesloLGS\ NF\ * /usr/share/fonts
sudo apt install fontconfig -y
sudo fc-cache -fv
```

&nbsp

`powerlevel10k`를 설치한다

```bash
sudo git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

에디터를 열어 `powerlevel10k`로 테마를 변경한다

```bash
nvim ~/.zshrc
```

`ZSH_THEME`을 `powerlevel10k/powerlevel10k` 로 변경해준다

그리고

```bash
source ~/.zshrc
```

를 진행하면, `powerlevel10k` 테마 세팅이 시작된다.

13. CUDA + cuDNN

현재 gpu 는 rtx 3090으로 3000번대 라인업이다

가장 최신의 gpu에 [맞는 버전](https://www.tensorflow.org/install/source#tested_build_configurations)은
tensorflow-2.6.0 / python 3.6~3.9 / cuDNN 8.1 / CUDA 11.2 이므로 맞춰서 설치하도록 하겠다.

현재 python은 `anaconda3`에 의해 `python 3.9.7`로 설치되어있다.

gpu 드라이버는 굳이 설치할 필요가 없다. 어차피 cuda 설치 시에 깔려있는 드라이버가 있으면 지우길 권고한다는 메시지가 뜬다.

```bash
▶ wget https://developer.download.nvidia.com/compute/cuda/11.2.0/local_installers/cuda_11.2.0_460.27.04_linux.run
```

cuda를 다운 받고

```bash
❯ sudo sh cuda_11.2.0_460.27.04_linux.run
```

설치한다

`cudnn`은 로그인 후 버전에 맞는 deb 파일을 다운 받는다. runtime과 developer 버전의 deb를 모두 받고, runtime 설치 후 developer 버전을 설치하면 된다

이후 `tensorflow`를 설치한다. 현재 `tensorflow 2.6.0` 버전에서는 일부 import 시의 문제가 발생하므로, tensorflow `2.5.0` 으로 다운그레이드 해서 사용하기를 권하고 있다. 따라서 2.5.0 으로 설치한다

```bash
 pip install tensorflow-gpu==2.5.0
```

`tensorflow` test는 제공되는 메소드와 gpu 실시간 사용량, CNN 예제 코드를 돌려보며 확인했다.

우당탕탕 세팅기.. 완성!
