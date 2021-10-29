---
layout: post
title: "sy-tabix와 sy-index의 차이"
date:  "2021-08-17 18:05:01"
categories: [SAP]
tags: [SAP, ABAP]
---

#### 1. sy-tabix: 인터널 테이블의 인덱스

- 인터널 테이블 MODIFY 시

```ABAP
LOOP AT gt_list INTO gs_list.
	MODIFY gt_list FROM gs_list INDEX sy-tabix.
ENDLOOP.
```

<br/>

- 이중루프 선언 시

```ABAP
LOOP AT gt_list_1.
	LOOP AT gt_list_2 FROM sy-tabix.
		"~~~
	ENDLOOP.
ENDLOOP.
```

<br/>

- 인터널 테이블 생성 후 레코드 순서대로 인덱스 번호 넣어줘야 할 때

```ABAP
LOOP AT gt_list.
	gt_list-idx = sy-tabix.
	MODIFY gt_list.
ENDLOOP.
```

<br/>

#### 2. sy-index: 반복문의 인덱스

```ABAP
DATA: lv_index LIKE sy-index.

DO 1000 TIMES.
	lv_index = sy-index.
ENDDO.
```