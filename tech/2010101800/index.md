---
layout: post
type: tech
date: 2020-10-10 18:00
category: Algorithm
title: 백준 1436번
subtitle: 영화감독 숌
writer: charlieppark
post-header: true
image: /img/title.png
header-img: /tech/2010101800/img/bg.png
hash-tag: [Algorithm, BOJ, PS, BruteForce]
draft: false
---

> [BOJ 1436: 영화감독 숌](https://www.acmicpc.net/problem/1436)

어제와 마찬가지로 성취는 부족했으나, 최소한 백준 하나는 성의 있게 푼 것 같다

어려운 문제는 아니었으나, 연속된 6이 등장해야함을 고려해 six_counter를 알맞게 사용해야하고

num_of_six_nums 를 N과 비교해 N번째 종말의 수를 찾으면 되는

풀만하고 재미있는 브루트 포스 문제였다.

```c

#include <stdio.h>


int main()
{
    int N;
    while (scanf("%d", &N) != 1) continue;

    int num = 666;
    int num_of_six_nums = 0;
    while (N != 0)
    {
        int six_counter = 0;
        int temp = num;
        while (temp != 0)
        {
            if (temp % 10 == 6)
                six_counter++;
            else
                six_counter = 0;
            temp /= 10;
            if (six_counter == 3)
            {
                num_of_six_nums++;
                if (num_of_six_nums == N)
                {
                    N = 0;
                    printf("%d", num);
                }
                break;
            }
        }
        num++;
    }
}



```
