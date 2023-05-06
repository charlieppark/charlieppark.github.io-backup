---
layout: post
type: tech
date: 2023-02-13 18:00
category: Others
title: Tip) LVM 최대 용량 만들기
subtitle: LVM 용량이 작을 때 뻘짓하지 않고 바로 최대 용량으로 만드는 방법
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2302131800/img/bg.png
hash-tag: [Others, Ubuntu, LVM, Server]
draft: false
---

## LVM과 함께한 아픈 추억..

아마 1년 정도 전, 서버를 세팅하며 lvm을 손대려다 파티션을 건드려 데이터를 다 날리고 포맷했더랬다..

~~아직도 그 생각을 하면 가슴이 끔찍한 것이, 백송골을 쳐다보고 두엄에 자빠진 두꺼비나 다름이 없다.~~

이번 서버 세팅에도 왜인지 모르겠으나 굳이 lvm으로 세팅을 해서, SSD의 용량인 1TB 중 100GB 밖에 사용하지 않고 있었다.

이에 백업을 서버로 복원하는 과정에서 용량 문제가 발생했다.

---

## 해결 방법

간단한 해결 방법을 확인해보자

먼저 lvm의 용량이 물리적 공간에 비해서 작아서 생기는 문제가 맞는지 확인해야 한다.

먼저 물리적 공간의 크기를 확인하자

```bash
$ sudo fdisk -l
```

그 다음 사용 중인 용량을 확인하자.

```bash
$ df -hP
```

/dev/mapper 어쩌구로 표시된 lvm의 크기를 확인한다.

출력된 물리적 공간과 lvm의 용량 차이를 비교해보면 글쓴이는 /dev/sda의 용량에 비해 실제 사용중인 lvm의 용량이 턱없이 작았다.

이러한 경우, 아래의 명령어로 lvm 용량을 최대로 채워줄 수 있다. 글쓴이의 {lvm_path} = /dev/mapper/ubuntu--vg-ubuntu--lv 이다

```bash
$ sudo lvextend -l +100%FREE {lvm_path}
$ sudo resize2fs {lvm_path}
```

lvextend로 lvm의 용량을 늘려주고, 확장된 lvm의 크기를 제대로 인식할 수 있게 resize2fs를 수행한다.

해결!
