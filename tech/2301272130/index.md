---
layout: post
type: tech
date: 2023-01-27 21:30
category: Others
title: Tip) 우분투에서 그래픽카드 확인하기
subtitle: lspci와 update-pciids를 사용하여 그래픽카드 정보 확인하기
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2301272130/img/bg.png
hash-tag: [Others, Tip, Ubuntu, lspci, GPU, Server]
draft: false
---

# 버전 확인

우분투에서 설치된 그래픽카드를 확인하려면

`lspci | grep VGA`를 하면 버전을 확인할 수 있습니다.

`lshw -C display`를 하면 조금 더 자세한 정보를 볼 수 있습니다.

여기서는 `lspci | grep VGA`를 해보겠습니다.

```bash
$ lspci | grep VGA
08:00.0 VGA compatible controller: ASPEED Technology, Inc. ASPEED Graphics Family (rev 41)
8a:00.0 VGA compatible controller: NVIDIA Corporation Device 2204 (rev a1)
```

NVIDIA Corporation Device 2204 와 같은 우리가 익숙하지 않은 넘버링이 나옵니다. 이는 pciid를 업데이트 하지 않아서 생기는 일입니다. 업데이트를 하여 2090, 3080 처럼 우리가 아는 넘버링이 출력되도록 해줍니다.

```bash
$ update-pciids
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  285k  100  285k    0     0   125k      0  0:00:02  0:00:02 --:--:--  125k
```

이후 다시 정보를 출력해봅니다

```bash
lspci | grep VGA
08:00.0 VGA compatible controller: ASPEED Technology, Inc. ASPEED Graphics Family (rev 41)
8a:00.0 VGA compatible controller: NVIDIA Corporation GA102 [GeForce RTX 3090] (rev a1)
```

정보가 잘 출력되는 것을 확인할 수 있습니다.

`lshw -C display`로 자세한 정보도 출력해봅니다.

```bash
$ lshw -C display
  *-display
       description: VGA compatible controller
       product: ASPEED Graphics Family
       vendor: ASPEED Technology, Inc.
       physical id: 0
       bus info: pci@0000:08:00.0
       version: 41
       width: 32 bits
       clock: 33MHz
       capabilities: pm msi vga_controller bus_master cap_list rom
       configuration: driver=ast latency=0
       resources: irq:17 memory:9b000000-9bffffff memory:9c000000-9c01ffff ioport:1000(size=128) memory:c0000-dffff
  *-display UNCLAIMED
       description: VGA compatible controller
       product: GA102 [GeForce RTX 3090]
       vendor: NVIDIA Corporation
       physical id: 0
       bus info: pci@0000:8a:00.0
       version: a1
       width: 64 bits
       clock: 33MHz
       capabilities: pm msi pciexpress vga_controller cap_list
       configuration: latency=0
       resources: iomemory:203f0-203ef iomemory:203f0-203ef memory:e5000000-e5ffffff memory:203fe0000000-203fefffffff memory:203ff0000000-203ff1ffffff ioport:d000(size=128) memory:e6000000-e607ffff
```

제대로 출력되는 것을 확인할 수 있습니다.
