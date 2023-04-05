---
layout: post
title: "SE11 Initial Value"
date: "2023-04-05 22:23:00"
categories: [SAP]
tags: [SAP]
---

# Initial Value

SE11에서 테이블을 조회하면 필드별로 Initial Value를 설정할 것인지 체크하는 부분이 있다.

![1](http://3.bp.blogspot.com/-Drg8qufHyFM/U_p7-nXe0wI/AAAAAAAAAA4/D2W-7cNNFJc/s1600/1.png)

해당 기능은 기존에 존재하던 테이블에, 어떠한 요구사항으로 인해 새로운 필드를 추가하게 될 경우
기존에 존재하던 row(데이터)들에 추가될 새로운 필드 값을

- **초기화하지 않고 null 값으로 내버려 둘 것인지**
- **초기값(Initial Value)을 넣어줄 것인지**

**`또한, 테이블을 처음 생성할 때부터 존재하던 필드들은 initial value 설정과 무관하게
해당 필드에 값을 할당하지 않은 경우 모두 initail value로 초기화된다..!!`**

Initial Value란 숫자형 타입은 0, 문자형 타입은 ‘ ‘ 공백을 의미

![2](https://solutionsapproblems.com/wp-content/uploads/2021/04/image-104.png)

위 테이블에서 CITYFROM 값이 없는 데이터를 조회하고 싶을 때
아래와 같이 쿼리를 짜면 무조건 값이 안 나올 것이고,

```abap
"무조건 sy-subrc = 4
SELECT * INTO CORRESPONDING FIELDS OF TABLE lt_spfli
	FROM spfli
	WHERE cityfrom IS NULL.
```

아래와 같이 쿼리를 짜야 원하는 값이 나올 것임..!

```abap
SELECT * INTO CORRESPONDING FIELDS OF TABLE lt_spfli
	FROM spfli
	WHERE cityfrom IS INITIAL.

```

### 참고한 게시글
[ABAP Null Value vs Initial Value](http://abapjoy.blogspot.com/2014/08/abap-null-value-vs-initial-value.html)