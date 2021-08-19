---
layout: post
title: "GETWA_NOT_ASSIGNED 런타임 에러 원인 및 해결 방법"
date: "2021-08-19 18:07:10"
categories: [SAP, ABAP, ERROR]
tags: [SAP, ABAP, ERROR]
---

> 인터널 테이블과 ALV 필드카탈로그 사이에 매핑되지 않은 필드가 있는지 확인
>
> 1. 필드카탈로그 필드명과 인터널 테이블 필드명이 똑같은지 확인
> 2. 필드카탈로그 필드명을 대문자로 입력했는지 확인  → 소문자로 입력하면 에러

<br/>

![getwa_not_assigned_1](https://i.imgur.com/9dUxntM.jpg)



<br/>



![getwa_not_assigned_2](https://i.imgur.com/6XKpvwa.jpg)



<br/>



위와 같이 `서로 필드명이 다른 경우` 아래와 같이 에러가 발생한다.

또한 **자주 하는 실수**로 `필드카탈로그명을 소문자로 입력한 경우`도 에러가 나니 주의! :eyes:



![getwa_not_assigned](https://i.imgur.com/zoc5Q8v.jpg)
