---
layout: post
type: tech
date: 2023-02-15 18:00
category: ComputerGraphics
title: "[Hong] 2-1 이미지 처리 기초"
subtitle: 홍정모 그래픽스 새싹코스 파트 1 - 챕터 2
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2302151800/img/bg.png
hash-tag: [ComputerGraphics, honglab, DirectX11, IMGUI, brighten, stb]
draft: false
---

> 본 포스트의 내용은 홍정모 그래픽스 새싹코드 파트 1을 공부하며 기록한 것으로, 직접 인용하거나 요약한 내용임을 밝힙니다. https://honglab.co.kr/courses/graphicspt1

# 2 - 1 이미지 처리 기초

## stb 추가 설치

이미지 처리를 위해 stb를 설치한다

```powershell
vcpkg install stb:x64-windows
```

## 이미지의 channel

RGB : 3 channels

RGBA : 4 channels (png)

## 메모

- Blur와 Bloom 함수를 집중해서 구현

- 일반적으로 C++에서 헤더 하나에 클래스 하나 이런 식으로 짜는 게 좋다

- Open MP : Multithreading 기반의 공유 메모리 병렬 프로그램

- stb : C / C++ 이미지 처리 라이브러리

- std::clamp (C++ 17) :

  - 범위를 한정시켜서 반환 (범위에 대해 min, max 동시에)

  - 타입 간의 변환 등을 수행할 때 미리 clamp로 범위를 잘라내고 변환

- 이 코드의 image 읽어들이는 방식은 구식의 C api 스타일

- stbi_load로 읽어들임 → unsigned char에서 0.0f ~ 1.0f의 float으로 변경 (rgb 값 / 255.0f) → 이미지 처리 → x 255.0f → unsigned char로 이미지 저장

- 최신 그래픽스 파이프라인은 픽셀 값을 하나하나 지정해서 그리지 않음. 3차원 공간에서 박스를 하나 그리고 거기에 사진을 그림.

---

원본 이미지

![original.png](img/original.png)

---

천천히 밝아지고 어두워지는 것 구현

(gif로 바꾸다보니 프레임이 끊기고 화질이 떨어져보인다)

![brighten.gif](img/brighten.gif)
