---
layout: post
type: tech
date: 2023-06-27 22:00
category: Software
title: "krrish94/chamferdist 와 pytorch의 결과 차이"
subtitle:
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2306272200/img/bg.png
hash-tag: [Software, python, chamferdist, Pytorch3D]
draft: false
---

Chamfer Distance라는 metric이 있다.

3D 공간에서 두 Point Cloud가 존재할 때, 이 Point Cloud간의 차이를 계산하는 metric으로 아래의 수식을 가진다.

![[chamfer_distance]()](img/cham.png)

그런데, 때때로 이 chamfer distance는 전체 point의 개수에 의해 normalize 되기도 한다.

이때는 아래의 수식처럼 표현된다.

![[normalized_chamfer_distance]()](img/normcham.png)

이 차이로 인해 Pytorch3D와 krrish94/chamferdist 의 결과에는 차이가 생긴다.

먼저 Pytorch3D에서 chamfer distance의 function declaration을 보면

```python
pytorch3d.loss.chamfer_distance(x, y, ... , batch_reduction: Optional[str] = 'mean', point_reduction: str = 'mean', ...)
```

[https://pytorch3d.readthedocs.io/en/latest/modules/loss.html](https://pytorch3d.readthedocs.io/en/latest/modules/loss.html)

Default로 point reduction과 batch reduction에 모두 mean을 사용함을 알 수 있다. 이는 상기한 두 번째 수식처럼 **normalized chamfer distance**를 사용하는 것이다.

반면 krrish94/chamferdist의 reduction 관련 코드를 보면

```python
        chamfer_forward = chamfer_forward.sum(1)  # (batchsize_source,)
        if reverse or bidirectional:
            chamfer_backward = chamfer_backward.sum(1)  # (batchsize_target,)

        if reduction == "sum":
            chamfer_forward = chamfer_forward.sum()  # (1,)
            if reverse or bidirectional:
                chamfer_backward = chamfer_backward.sum()  # (1,)
        elif reduction == "mean":
            chamfer_forward = chamfer_forward.mean()  # (1,)
            if reverse or bidirectional:
                chamfer_backward = chamfer_backward.mean()  # (1,)

```

point reduction에는 sum이 고정으로 사용되고, batch reduction은 선택적으로 mean 또는 sum을 사용할 수 있음을 알 수 있다. 이때, batch reduction의 default는 mean이다.

즉, krrish94/chamferdist은 상기한 첫 번째 수식처럼 **Normalize 되지 않은** chamfer distance를 사용한다.

따라서, 둘 모두 동일한 결과를 만들기 위해서는, pytorch3d에서 point reduction으로 sum을 사용하거나, krrish94/chamferdist에서 아래 부분을 수정할 수 있다.

```python
        chamfer_forward = chamfer_forward.sum(1)  # (batchsize_source,)
        if reverse or bidirectional:
            chamfer_backward = chamfer_backward.sum(1)  # (batchsize_target,)
```

위의 부분을

```python
        chamfer_forward = chamfer_forward.mean(1)  # (batchsize_source,)
        if reverse or bidirectional:
            chamfer_backward = chamfer_backward.mean(1)  # (batchsize_target,)
```

이처럼 수정하면 된다.

그러나 이와 같은 수정은 conda 등의 lib 자체를 건드려야 하고, robust한 방법이 아니다.

따라서, krrish94/chamferdist에서 point reduction에 sum과 mean을 모두 사용할 수 있도록 parameter를 추가한 후 PR을 열었다.

```python

    def forward(
        self,
        source_cloud: torch.Tensor,
        target_cloud: torch.Tensor,
        bidirectional: Optional[bool] = False,
        reverse: Optional[bool] = False,
        batch_reduction: Optional[str] = "mean",
        point_reduction: Optional[str] = "sum",
    ):

   ...

        if point_reduction == "sum":
            chamfer_forward = chamfer_forward.sum(1)  # (batchsize_source,)
            if reverse or bidirectional:
                chamfer_backward = chamfer_backward.sum(1)  # (batchsize_target,)
        elif point_reduction == "mean":
            chamfer_forward = chamfer_forward.mean(1)  # (batchsize_source,)
            if reverse or bidirectional:
                chamfer_backward = chamfer_backward.mean(1)  # (batchsize_target,)
```

![[PR](https://github.com/krrish94/chamferdist/pull/19)](img/pr.png)
[https://github.com/krrish94/chamferdist/pull/19](https://github.com/krrish94/chamferdist/pull/19)

어떤 답변이 올까?
