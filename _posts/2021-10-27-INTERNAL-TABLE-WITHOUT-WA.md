---
layout: post
title: "헤더가 없는 인터널 테이블 다루기"
date: "2021-10-27 16:17:20"
categories: [SAP]
tags: [SAP, ABAP, ERROR]

---

 `READ TABLE [ITAB] WITH KEY [CONDITION].`  
`LOOP AT [ITAB]`

헤더가 없는 인터널 테이블의 READ, LOOP 문을 헤더가 있는 인터널 테이블 호출할 때와 똑같이 선언 시 아래와 같은 에러 메시지가 뜨게 된다.

> ##### Specify either "INTO wa", "ASSIGNING", "REFERENCE INTO", or "TRANSPORTING NO FIELDS" addition.

헤더가 있는 경우 READ나 LOOP 구문에서 결과값을 바로 헤더에 넣어서 읽으면 되는데,  
헤더가 없는 경우에는 읽어서 저장할 곳이 없기 때문에 work area나 field symbol에 따로 할당해 줘야 한다.

---

### 헤더없는 ITAB 읽는 4가지 방법

1. INTO WA  
   헤더가 없으니 대신에 Work Area에 담아서 READ 하는 방식  
   `READ [ITAB] WITH KEY [CONDITION] INTO WA.`  
   `LOOP AT [ITAB] INTO WA.`

   

2. ASSIGNING `<fs>`  
   헤더 대신 field symbol에 assign  
   `READ TABLE [ITAB] ASSIGNING <fs>.`  
   `LOOP AT [ITAB] ASSIGNING <fs>.`

   

3. REFERENCE INTO  dref  
   찾은 데이터의 참조값 생성

   ```ABAP
   DATA: lr_mara TYPE REF TO mara,
   	    lt_mara TYPE TABLE OF mara.
   	  
   SELECT * FROM mara INTO TABLE lt_mara UP TO 10 rows.
   
   LOOP AT lt_mara REFERENCE INTO lr_mara.
   	WRITE: / lr_mara->matnr.
   ENDLOOP.
   ```

   

4. TRANSPORTING NO FIELDS  
   단순히 해당 조건의 데이터 존재 여부만 체크  
   sy-tabix에는 READ 성공 시 해당 라인 인덱스가 저장됨
