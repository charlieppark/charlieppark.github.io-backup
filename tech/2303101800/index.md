---
layout: post
type: tech
date: 2023-03-10 18:00
category: ComputerGraphics
title: "[Hong] 3-1 Vector 기초"
subtitle: 홍정모 그래픽스 새싹코스 파트 1 - 챕터 3
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2303101800/img/bg.png
hash-tag:
  [ComputerGraphics, honglab, DirectX11, IMGUI, RelativeLuminance, Bloom]
draft: false
---

> 본 포스트의 내용은 홍정모 그래픽스 새싹코드 파트 1을 공부하며 기록한 것으로, 직접 인용하거나 요약한 내용임을 밝힙니다. https://honglab.co.kr/courses/graphicspt1

## Laytracing

laytracing

광원 역추적

우리 눈이 바라보는 스크린의 픽셀 → 물체 표면의 색 ← 광원에서의 빛

![[https://www.scratchapixel.com/lessons/3d-basic-rendering/introduction-to-ray-tracing/implementing-the-raytracing-algorithm.html](https://www.scratchapixel.com/lessons/3d-basic-rendering/introduction-to-ray-tracing/implementing-the-raytracing-algorithm.html)](img/raytracing.png)

[https://www.scratchapixel.com/lessons/3d-basic-rendering/introduction-to-ray-tracing/implementing-the-raytracing-algorithm.html](https://www.scratchapixel.com/lessons/3d-basic-rendering/introduction-to-ray-tracing/implementing-the-raytracing-algorithm.html)

벡터를 가지고 계산

![[https://www.educba.com/ray-tracing-algorithm/](https://www.educba.com/ray-tracing-algorithm/)](img/raytracing_vec.png)

[https://www.educba.com/ray-tracing-algorithm/](https://www.educba.com/ray-tracing-algorithm/)

벡터만 사용해도 구현 가능

파트 2에서 행렬을 사용해서 다시 구현

## Vector 기초

아래의 내용은 너무 기본적인 내용이라 간단하게 강의 내용을 메모만 했습니다.

벡터 : 방향과 거리를 포함

위치 벡터랑 방향, 거리를 의미하는 벡터를 구분해서 사용

벡터 거리 l2

단위벡터 보통 hat을 씌움

dot product : scalar product, 내적

![[https://byjus.com/maths/dot-product-of-two-vectors/](https://byjus.com/maths/dot-product-of-two-vectors/)](img/inner_product.png)

[https://byjus.com/maths/dot-product-of-two-vectors/](https://byjus.com/maths/dot-product-of-two-vectors/)

cross product : vector product, 외적

![[https://www.aplustopper.com/cross-product/](https://www.aplustopper.com/cross-product/)](img/cross_product.png)

[https://www.aplustopper.com/cross-product/](https://www.aplustopper.com/cross-product/)

cross product 는 3차원에서 주로 사용

왼손 좌표계

**a**와 **b** cross product의 결과인 vector **c.** 크기는 **a**와 **b** 사이에서 생기는 삼각형 넓이의 두 배, **a**와 **b**에 수직

보통 **a**와 **b**에서 생기는 평행사변형의 넓이 이런식으로 배우는데 그래픽스에서는 저 삼각형의 넓이를 구할 때 cross product를 많이 사용하므로 이렇게 설명
