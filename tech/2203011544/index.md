---
layout: post
type: tech
date: 2022-03-01 15:44
category: Blog
title: 코딩테스트 준비
subtitle: 코딩테스트 준비를 위한 알고리즘 개요
writer: KimYC1223
post-header: true
image: /img/title.gif
header-img: /tech/2203011544/img/bg.png
hash-tag: [Algorithm]
---

본 게시글은 2022년 상/하반기 코딩테스트를 보다 체계적으로 준비하기 위해, 코딩 테스트 알고리즘을 정리한 글이다.

실전 코딩 테스트를 진행하면서도 이 페이지의 게시글을 참고하면 해당 문제를 해결하기 위해 어떤 알고리즘을 써야 하는지 결정 할 수 있는 가이드라인이 될 것으로 기대한다.

---
# 📘 코딩 테스트 준비 오버뷰

코딩 테스트는 위와 같이 크게 자료구조와 알고리즘쪽으로 구분 할 수 있을 것이다.

본 내용은 추가되거나 수정 될 수 있으며, 자세한 설명이나 예제는 따로 포스팅 할 예정이다.

본 내용은 cpp을 기준으로 작성되었다.

## 🗄️ 자료 구조

### I. 선형 구조

#### 💠Array

배열은 연속된 변수들을 저장하고 관리 할 수 있는 가장 간단한 자료구조이다.

검색은 매우 빠르나 추가/제거가 불편하다는 단점이있다.

``` cpp
int testArray[] = {1,2,3,4};
```

#### 💠 List(Vector)

배열과 다르게 길이나 용량을 가변적으로 정할 수 있다는 장점이 있다.

배열보다는 조금 느리다는 단점이 있다.

``` cpp
#include <vector>

vector<int> testVector_1;             // 빈 벡터 만들기
vector<int> testVector_2(5);            // 원소 5개로 만들기
vector<int> testVector_3(testVector_2); // 다른 벡터로 만들기
```

### II. 비선형 구조

#### map

원소들간 순서가 중요하지 않은 경우, **중복을 허락하지 않는** 집합인 map을 사용 할 수 있다.
