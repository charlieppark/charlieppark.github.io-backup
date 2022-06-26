---
layout: post
type: tech
date: 2022-03-01 15:44
category: Blog
title: 코딩테스트 준비
subtitle: 코딩테스트 준비를 위한 알고리즘 개요
writer: KimYC1223
post-header: false
image: /img/title.png
hash-tag: [Algorithm]
draft : false
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

#### Map

인덱스로 int가 아닌 다른 자료형을 사용할 수 있는 트리

cpp의 map은 검색,삽입,삭제가 ```n log(n)```인 [red black tree](https://ko.wikipedia.org/wiki/%EB%A0%88%EB%93%9C-%EB%B8%94%EB%9E%99_%ED%8A%B8%EB%A6%AC)로 구현되었다.

``` cpp
#include <map>

map<char,int> m;
map<char,int>::iterator it;

m['B'] = 2;                        //m : (B,2)
m.insert(make_pair('A',1));        //m : (A,1) (B,2)
m['C'] = 3;                        //m : (A,1) (B,2) (C,3)

m.erase('A');                      //m : (B,2) (C,3)

//m전체를 순회하며 key와 value 출력
for(it = m.begin(); it != m.end(); it++)
    cout << it->first << ' ' << it->second << '\n';

if(m.find('B') != m.end())
    cout << "노드가 존재합니다." << '\n';
else
    cout << "노드가 존재하지 않습니다." << '\n';

```

**중복을 허용하지않기 때문에,** 중복되는 key값을 입력받으면 무시한다.

> 코드 출저 : [공대사람의 '주니어 개발자의 대나무숲' 블로그](https://sarah950716.tistory.com/6)

### Set

key만 있는 map 혹은 정렬된 집합

특징은 **set은 key와 value가 같다**는 것이다.

``` cpp
#include <set>

set<int> s;
set<int>::iterator it;

s.insert(4);               //s : 4
s.insert(1):               //s : 1 4
s.insert(2);               //s : 1 2 4

vector<int> v;
v.push_back(3);            //v : 3
v.push_back(5);            //v : 3 5
v.push_back(6);            //v : 3 5 6

s.insert(v.begin(), v.end());        //s : 1 2 3 4 5 6

s.erase(4);                //s : 1 2 3 5 6
s.erase(s.begin());        //s : 2 3 5 6

//지울 원소를 입력받음
int toErase;
scanf("%d", &toErase);

it = s.find(toErase);

//지울 원소가 존재하는 원소일 때만 지움
if(it != s.end())
    s.erase(it);
```

> 코드 출저 : [공대사람의 '주니어 개발자의 대나무숲' 블로그](https://sarah950716.tistory.com/6)