---
layout: post
title: "ABAP에서 자주 사용하는 String 관련 함수"
date: "2021-12-07 16:45:02"
categories: [SAP]
tags: [SAP, ABAP]
---

#### 문자열 합치기

`CONCATENATE lv_str1 lv_str2 INTO lv_string [condition].`

lv_str1, lv_str2를 합쳐서 lv_string에 넣어준다.

타겟 lv_string이

- 정해진 length를 가지며
  - 결과보다 더 길다면 남은 공간은 공백 또는 16진수 0 으로 채워진다.
  - 결과보다 더 짧다면 초과된 길이는 잘려서 저장된다.
- string 타입이라면, 길이에 맞게 최적화된다.

condition

- [SEPERATED BY lv_str] : lv_str 넣어서 합치기

- [RESPECTING BLANKS] :  문자열 원형 그대로 합치기. target이 string인 경우에만 사용되며 공백을 삭제하지 않고 그대로 합친다.

---

#### 문자열 길이 구하기

`lv_len = STRLEN( lv_string ).`

소괄호(그룹 연산자) 내에 변수명을 소괄호와 한 칸씩 띄워줘야 한다.

---

#### 문자열 SPLIT

`SPLIT lv_string AT lv_std INTO lv_str1 lv_str2ㆍㆍㆍ.`

lv_string 을 lv_std 문자열을 기준으로 lv_str1, lv_str2에 찢어준다.

---

#### 문자열 압축

`CONDENSE lv_string [NO-GAPS].`

lv_string 의 문자열 시작 전 공백, 끝 공백을 삭제하고 압축시킨다.

예를 들어, '     Hello World!      ' →  'Hello World!'

구문 마지막에 NO_GAPS를 선언한다면, 모든 공백을 삭제하고 압축시킨다.

만약 length가 정해져 있다면, 압축하고 남은 공간을 오른쪽에 채워넣는다.

데이터 타입이 string이라면, 길이는 압축된 문자열에 최적화된다.

---

#### 문자열 변경

`REPLACE lv_str1 IN lv_string WITH lv_str2.`

문자열에서 첫번째로 검색된 lv_str1을 lv_str2로 변경한다.

`REPLACE ALL OCCURRENCES OF lv_str1 IN lv_string WITH lv_str2 .`

문자열에 존재하는 모든 lv_str1을 lv_str2로 변경한다.

---

#### 대소문자 변경

`TRANSLATE lv_string TO UPPER CASE.`

`TRANSLATE lv_string TO LOWER CASE.`
