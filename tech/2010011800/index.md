---
layout: post
type: tech
date: 2020-10-04 18:00
category: Algorithm
title: 백준 1011번
subtitle: Fly me to the Alpha Centauri
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2010011800/img/bg.png
hash-tag: [Algorithm, BOJ, PS, Math(Algorithm)]
draft: false
---

> [BOJ 1011 : Fly me to the Alpha Centauri](https://www.acmicpc.net/problem/1011)

x, y 로 좌표가 주어지고, x,y의 차를 이용해 거리를 구한다

이 거리를 알맞게 쪼개는 문제.

이 문제에서 주어진 규칙은 다음과 같다

1. 이전에 k광년의 거리를 이동했다면, 그 다음 이동 거리는 k-1, k, k+1만 가능하다.

2. 도착 직전의 마지막 이동거리는 1이어야 한다.

이 문제에 접근하기 위해 연쇄적으로 질문을 던져봤다.

이 문제에 접근할 수 있는 알고리즘이 있나?

없다면 모든 혹은 특정한 경우의 수만큼 반복 수행을 진행해 최소 횟수가 나올 경우를 찾는다.

있다면 알고리즘으로 푼다.

알고리즘이 있는지 확신을 할 수 없었는데, 한가지 힌트를 얻은 부분은

숫자가 순차적으로 증가하고 감소하는, 예컨대 등차수열의 꼴로 표현될 수 있는 경우였다.

예를 들어 거리가 25라면 1, 2, 3, 4, 5, 4, 3, 2, 1 처럼 순차적으로 증가하고 감소하는 수로 표현할 수 있다.

(이 거리는 가장 큰 숫자 n의 제곱으로 표현된다)

이 지점들을 기준으로, 나머지 거리를 표현할 수 없을까?

예컨대, 거리가 26이라면, 25를 표현한 경우 사이에 거리가 1인 횟수를 1,2 또는 2,1 사이에 추가하면 된다.

마찬가지로, 27, 28, 29 모두 표현가능하다.

그런데, 만약 거리 - 25 가 가장 큰 숫자인 5를 넘어서면 숫자 표현이 조금 달라진다.

이 숫자를 더 명확히 표현하기 위해, 33을 예로 들어 생각해보자

33은 25 + 8로, 8은 5보다 크다.

따라서, 8을 쪼개야하는데, 8을 어떻게 쪼개느냐에 따라 횟수가 달라진다

2, 2, 2, 2라면 네번이 추가되고

4, 4 라면 두번이 추가된다.

지금은 숫자가 작아 어떤 경우가 최소 횟수를 만드는지 판단하기가 쉽지만, 숫자가 클 수록 문제가 생긴다.

따라서, 가장 큰 숫자인 5를 먼저 빼고, 남은 숫자로 처리를 하면 된다

즉, 5, 3과 같이 하면 최소 횟수임을 확실히 알 수 있는 것이다

위와 같은 내용을 counter 함수 안에 구현했다.

```c

#include <stdio.h>
#include <math.h>

int counter(int);

int main()
{
    int T, x, y, count;

    while (scanf("%d", &T) != 1);

    for (int i = 0; i < T; i++)
    {
        int distance;

        while (scanf("%d %d", &x, &y) != 2);

        distance = y - x;

        count = counter(distance);

        printf("%d\n", count);
    }


}

int counter(int d)
{
    int count = 0;
    int n;

    n = (int)sqrt((float)d);
    count += 2 * n - 1;
    d -= n * n;

    count += d / n;

    d %= n;
    if (d > 0)
        count++;

    return count;
}

```
