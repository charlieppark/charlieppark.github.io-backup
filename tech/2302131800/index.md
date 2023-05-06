---
layout: post
type: tech
date: 2023-02-13 18:00
category: Others
title: Tip) LVM 최대 용량 만들기
subtitle: 물리적 용량에 비해 LVM 용량이 작을 때 뻘짓하지 않고 바로 최대 용량으로 만드는 방법
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2302131800/img/bg.png
hash-tag: [Others, Tip, Ubuntu, LVM, fdisk, lvextend, Server]
draft: false
---

## LVM과 함께한 아픈 추억..

아마 1년 정도 전, 서버를 세팅하며 `lvm`을 손대려다 파티션을 건드려 데이터를 다 날리고 포맷했습니다.

아직도 그 생각을 하면 가슴이 끔찍한 것이.. ~~백송골을 쳐다보고 두엄에...~~

이번 서버 세팅에도 왜인지 모르겠으나 굳이 lvm으로 세팅을 해서, SSD의 용량인 1TB 중 100GB 밖에 사용하지 않고 있었습니다.

이에 백업을 서버로 복원하는 과정에서 용량 문제가 발생했습니다.

---

## 해결 방법

간단한 해결 방법을 확인해봅시다.

먼저 lvm의 용량이 물리적 공간에 비해서 작아서 생기는 문제가 맞는지 확인해야 합니다.

물리적 공간의 크기를 확인합니다

```bash
$ sudo fdisk -l
```

그 다음 사용 중인 용량을 확인합니다.

```bash
$ df -hP
```

`/dev/mapper` 어쩌구로 표시된 lvm의 크기를 확인합니다.

출력된 물리적 공간과 lvm의 용량 차이를 비교해보면 제 환경에서는 `/dev/sda`의 용량에 비해 실제 사용중인 lvm의 용량이 턱없이 작았습니다.

이러한 경우, 아래의 명령어로 lvm 용량을 최대로 채워줄 수 있습니다. 이 환경에서는 `{lvm_path} = /dev/mapper/ubuntu--vg-ubuntu--lv` 입니다

```bash
$ sudo lvextend -l +100%FREE {lvm_path}
$ sudo resize2fs {lvm_path}
```

`lvextend`로 lvm의 용량을 늘려주고, 확장된 lvm의 크기를 제대로 인식할 수 있게 `resize2fs`를 수행합니다.

해결!
