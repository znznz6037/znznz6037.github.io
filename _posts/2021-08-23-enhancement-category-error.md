---
layout: post
title: "Enhancement Category ~ 에러 원인 및 해결 방법"
date: "2021-08-23 19:10:23"
categories: [SAP]
tags: [SAP, ABAP, ERROR]
---



테이블, structure 수정 후 활성화하려고 할 때 다음과 같은 Enhancement category ~ 에러가 뜬다.

(아래는 warning이지만, error 발생 시 캡처하지 못해서 warning 이미지로 대체)

![1](https://i.imgur.com/a91E28z.jpg)



## 원인

---



Dictionary type을 QUAN으로 설정했을 경우, 

정확하게 어떤 도메인을 참조하는지 입력하지 않아 발생하는 에러



## 해결 방법

---



![2](https://i.imgur.com/mvU2hZr.jpg)

간단하게 해당 필드의 구성요소유형(도메인) 더블 클릭한 후, 참조필드를 넣어주면 된다.

<br/>

![3](https://i.imgur.com/0Pwfon6.jpg)

참조 필드 기입!