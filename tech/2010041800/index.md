---
layout: post
type: tech
date: 2020-10-04 18:00
category: Algorithm
title: 백준 11729번
subtitle: 하노이 탑 이동 순서
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2010041800/img/bg.png
hash-tag: [Algorithm, BOJ, PS, Recursion]
draft: false
---

> [BOJ 11729 : 하노이 탑 이동 순서](https://www.acmicpc.net/problem/11729)

예전에 헷갈려서 실패했던 문제

횟수는 **a<sub>n</sub> = 2 \* a<sub>n_1</sub> + 1, a<sub>1</sub> = 1** 꼴로 표현이 간단하게 돼서 큰 문제가 없는데

어디서 어디로 옮길 것인가가 되게 헷갈린다.

결론은, n개 쌓인 탑을 from에서 to로 옮기려면, n-1개 쌓인 탑을 from 에서 to 가 아닌 곳으로 (1, 2, 3 중 from, to가 배정되지 않은 곳) 옮겨야 한다는 것이다. 그 위치를 stopover 라고 변수로 만들었고, 1, 2, 3의 합이 6이라는 것에 착안해 6 - (from + to) 로 표현하였다.

그 이후는 간단하게 하노이 함수의 재귀로 해결하면 된다.

n개를 from에서 to로 움직이는 Hanoi(n, from, to)로 표현하자.

Hanoi(n, from, to) 는 Hanoi(n-1, from, stopover) 실행 -> 맨 밑에 남은 가장 큰 원판을 from에서 to로 이동 -> Hanoi(n-1, stopover, to) 실행

순으로 이뤄진다.

즉, n개의 탑을 from 에서 to로 옮기려면, n-1개의 탑을 from 에서 stopover 로 옮겨놓고, 맨 아래의 원판을 from에서 to 로 옮기고, n-1개의 탑을 stopover에서 to 로 옮기면 된다.

```c

#include <stdio.h>

void Hanoi(int, int, int);
int Hanoi_count(int);

int main()
{
    int N;

    while(scanf("%d", &N) != 1) continue;

    printf("%d\n", Hanoi_count(N));
    Hanoi(N, 1, 3);

    return 0;
}

void Hanoi(int n, int from, int to)
{
    if (n == 1)
        printf("%d %d\n", from, to);
    else
    {
        int stopover;
        stopover = 6 - (from + to);
        Hanoi(n - 1, from, stopover);
        printf("%d %d\n", from, to);
        Hanoi(n - 1, stopover, to);
    }
}

int Hanoi_count(int n)
{
    if (n == 1)
    {
        return 1;
    }
    else
    {
        return 2 * Hanoi_count(n - 1) + 1;
    }
}
```
