---
layout: post
title: "프로그래머스 위클리 챌린지 1주차_부족한 금액 계산하기"
date: "2021-09-11 19:10:10"
categories: [알고리즘]
tags: [알고리즘, 프로그래머스, 위클리 챌린지]
---



### 문제 설명

>새로 생긴 놀이기구는 인기가 매우 많아 줄이 끊이질 않습니다. 
>
>이 놀이기구의 원래 이용료는 price원 인데, 놀이기구를 N 번 째 이용한다면 원래 이용료의 N배를 받기로 하였습니다. 
>
>즉, 처음 이용료가 100이었다면 2번째에는 200, 3번째에는 300으로 요금이 인상됩니다.
>
>놀이기구를 count번 타게 되면 현재 자신이 가지고 있는 금액에서 얼마가 모자라는지를 return 하도록 solution 함수를 완성하세요.
>
>단, 금액이 부족하지 않으면 0을 return 하세요.



### 제한 사항

> - 놀이기구의 이용료 price : 1 ≤ price ≤ 2,500, price는 자연수
> - 처음 가지고 있던 금액 money : 1 ≤ money ≤ 1,000,000,000,
>   money는 자연수
> - 놀이기구의 이용 횟수 count : 1 ≤ count ≤ 2,500, count는 자연수



### 입출력 예

| price | money | count | result |
| ----- | ----- | ----- | ------ |
| 3     | 20    | 4     | 10     |



### 풀이

```java
class Solution {
    public long solution(int price, int money, int count) {
        long answer = -1;
        long sum = 0;

        for(int i = 1; i <= count; i++) {
            sum += price * i;
        }

        if(money < sum) {
            answer = sum - money;
        } else {
            answer = 0;
        }

        return answer;
    }
}
```

처음에 제출 시 위와 같이 되게 원초적인 방법으로 해결하였는데,

`총 이용료 = price * (1 + 2 + 3 + ... + (count - 1) + count )`  이므로,

10여 줄로 짠 코드를 한 줄로 간단하게 압축할 수 있다.

`price * count * (count + 1) / 2 `

주의할 점은 price = 2500, count = 2500 일 경우

int 최대 범위를 초과하므로 long 타입으로 형변환을 해줘야 한다.



```java
class Solution {
    public long solution(int price, int money, int count) {
        long answer = -1;        
        answer = (long)price * (count * (count + 1) / 2) - money;        
        return answer < 0 ? 0 : answer;
    }
}
```

오랜만에 알고리즘 문제를 풀다보니 머리가 굳은 게 느껴진다.

일주일에 한 문제씩 풀기

