---
layout: post
title: "ABAP 병렬처리(Parallel Processing)"
date: "2022-09-09 12:52:00"
categories: [SAP]
tags: [SAP, ABAP]
---

매일 새벽에 실행될 일별 집계 배치잡 추가 후,  
다음날 모니터링 해보니 메모리 부족 에러 발생

기존 배치잡 로직
```abap
LOOP AT lt_itab.
	SUBMIT 'ZPROGRAM' WITH p_batch EQ 'X'
                          WITH s_key   EQ lt_itab-key.
ENDLOOP.
```

![1](https://i.imgur.com/UWRMGqN.png)

![2](https://i.imgur.com/i6QRDQi.png)

안그래도 새벽에 배치잡들이 많이 돌아가고 있는데,  
정해진 배치 시작 시간에 서버 메모리 상황은 고려하지 않은 채 무턱대고 실행시켜버리니 서버 메모리 부족이 발생한 듯 하다.

## 병렬 처리(Parallel Processing)
---
대량의 데이터 처리 시 `병렬 처리(Parallel Processing)`를 이용하여 메모리 부족 방지가 가능하다.  

![3](https://i.imgur.com/shZpVoX.png)

Work Process에서 클라이언트 요청(프로그램 수행)을 처리한다.  
서버 메모리 부족을 방지하기 위해서 현재 서버에서 Work Process 가용 개수를 확인하고,  
가용 개수만큼 프로세스를 잡아서 배치잡을 병렬 처리한다.

### Work Process 가용 개수 체크
---
```abap
FORM CHECK_P_PARAL  USING    PV_DEST
                             PV_WP_RAT
                             PV_WP_MAX
                    CHANGING PV_PARAL PV_GRP.
  DATA L_MAX      TYPE  I.
  DATA L_FREE     TYPE  I.
  DATA LT_WPLIST  LIKE TABLE OF WPINFO WITH HEADER LINE.

  "서버그룹 추출
  SELECT  SINGLE  CLASSNAME  INTO  PV_GRP
          FROM  RZLLITAB
          WHERE  CLASSNAME  =  PV_DEST.

  "Use to determine the availability of resources for parallel processing
  CALL FUNCTION 'SPBT_INITIALIZE'
    EXPORTING
      GROUP_NAME                     = PV_GRP
    IMPORTING
      MAX_PBT_WPS                    = L_MAX
      FREE_PBT_WPS                   = L_FREE
    EXCEPTIONS
      INVALID_GROUP_NAME             = 1
      INTERNAL_ERROR                 = 2
      PBT_ENV_ALREADY_INITIALIZED    = 3
      CURRENTLY_NO_RESOURCES_AVAIL   = 4
      NO_PBT_RESOURCES_FOUND         = 5
      CANT_INIT_DIFFERENT_PBT_GROUPS = 6
      OTHERS                         = 7.

  IF SY-SUBRC NE 0.
    "SPBT_INITIALIZE 한번만 호출해야하는데, 이미 호출되어 PBT 설정된 경우
    "현재 리소스 정보를 가져온다.
    IF SY-SUBRC EQ 3.
      CALL FUNCTION 'SPBT_GET_CURR_RESOURCE_INFO'
        IMPORTING
          MAX_PBT_WPS                 = L_MAX
          FREE_PBT_WPS                = L_FREE
        EXCEPTIONS
          INTERNAL_ERROR              = 1
          PBT_ENV_NOT_INITIALIZED_YET = 2.
      IF SY-SUBRC NE 0.
        CLEAR PV_GRP.
      ENDIF.
    ELSE.
      CLEAR PV_GRP.
    ENDIF.
  ENDIF.

  IF PV_GRP IS INITIAL.    "현재 서버로 대체
    CALL FUNCTION 'TH_GET_WPINFO'
      TABLES
        WPLIST     = LT_WPLIST
      EXCEPTIONS
        SEND_ERROR = 1
        OTHERS     = 2.
    IF SY-SUBRC EQ 0.
      DELETE LT_WPLIST WHERE NOT ( WP_ITYPE = '1' AND WP_ISTATUS = '2'
).
      L_FREE = LINES( LT_WPLIST ).
    ENDIF.
  ENDIF.

  PV_PARAL = L_FREE  *  PV_WP_RAT  /  100.  "WP 점유율(%)

  IF  PV_PARAL  >  PV_WP_MAX.               "WP 개수
    PV_PARAL  =  PV_WP_MAX.
  ENDIF.

ENDFORM.                    " CHECK_P_PARAL
```

### 병렬 처리 핵심 키워드
---
```abap
CALL FUNCTION 'ZXXX_EXEC_BATCH_ZXXXXXXXX'
      STARTING NEW TASK LV_TASK
      DESTINATION IN GROUP PV_GRP
      PERFORMING RETURN_EXEC_BATCH_ZXXXXXXXX ON END OF TASK
      TABLES
        LT_DATA               = LT_TEMP
      EXCEPTIONS
        COMMUNICATION_FAILURE = 1  MESSAGE L_SMESS
        OTHERS                = 2.

FORM RETURN_EXEC_BATCH_ZXXXXXXXX  USING  PV_TASKNAME.
  GV_RETURN_CNT = GV_RETURN_CNT + 1.
ENDFORM.
```

> CALL FUNCTION "function_name"  
  STARTING NEW TASK "task_name"  
  DESTINATION IN GROUP "argument".
> 

→ 함수가 병렬 호출되도록 요청하는 키워드

> PERFORMING "subroutine_name" ON END OF TASK
> 

→ 프로세스 종료 시 호출할 서브루틴(현재 처리한 task 수 카운트)

RFC 함수에는 배치를 통해 하고자 하는 작업(DB 업데이트할 쿼리나 SUBMIT 구문 등)을 정의하고,  
`현재 가용 WP 개수만큼 RFC 함수를 병렬 호출하여 전체 프로세스가 완료될 때까지 반복하는 방식`
### WP 개수만큼 RFC 호출

---

```abap
PERFORM  CHECK_P_PARAL USING 'parallel_generators'
                             '30'
                             '6'
                       CHANGING L_PARAL
                                L_GRP.

"가용 WP 1개보다 많은 경우
IF L_PARAL > 1.
	PERFORM SUBMIT_ZX.
ENDIF.

FORM SUBMIT_ZX   TABLES PT_DATA STRUCTURE 테이블명
                 USING  PV_PARAL
                        PV_GRP
                        PV_CNT_T .

  DATA : LS_LIST LIKE "테이블명 ,
         LT_TEMP LIKE "테이블명 OCCURS 0 WITH HEADER LINE,
         LV_GAP    TYPE  I,
         LV_S      TYPE  I,
         LV_E      TYPE  I,
         LV_TASK   TYPE  NUMC08,
         L_SND_COUNT  TYPE I,
         L_SMESS      TYPE  BAPI_MSG.

  CLEAR: GV_RCV_COUNT_1.

  LV_GAP  =  PV_CNT_T  /  PV_PARAL.    "총 실행되어야 할 횟수 / WPS 갯수

  G_CHK = 'X'.

  IF  LV_GAP  <  1.
    LV_GAP  =  1.
  ENDIF.

  LV_S  =  1.
  LV_E  =  LV_GAP.

  LV_TASK = 1 .
  DO  PV_PARAL  TIMES.

    CLEAR : LT_TEMP , LT_TEMP[] .
    LOOP  AT  PT_DATA INTO LS_LIST  FROM  LV_S  TO  LV_E.
      APPEND  LS_LIST  TO LT_TEMP.
    ENDLOOP.

    IF  LT_TEMP[]  IS  INITIAL.
      EXIT.
    ENDIF.

    LV_S  =  LV_E  +  1.
    LV_E  =  LV_S  +  LV_GAP.

    LV_TASK  =  LV_TASK  +  1.

    CALL FUNCTION 'ZXXX_EXEC_BATCH_ZXXXXXXXX'
      STARTING NEW TASK LV_TASK
      DESTINATION IN GROUP PV_GRP
      PERFORMING RETURN_EXEC_BATCH_ZXXXXXXXX ON END OF TASK
      TABLES
        LT_DATA               = LT_TEMP
      EXCEPTIONS
        COMMUNICATION_FAILURE = 1  MESSAGE L_SMESS
        OTHERS                = 2.

    IF SY-SUBRC  =  0.
      L_SND_COUNT  = L_SND_COUNT + 1.
    ENDIF.

  ENDDO.

  "RFC 병렬 처리가 모두 완료될 때까지 WAIT
  WAIT UNTIL GV_RCV_COUNT >= L_SND_COUNT.

ENDFORM.
```

### 병렬 처리 적용 결과
---

![4](https://i.imgur.com/7MM1yvH.png)

에러 없이 정상적으로 수행되었다.

![5](https://i.imgur.com/XCWdYvo.png)

  

## 참고 문서
---
[Implementing Parallel Processing](http://saphelp.ucc.ovgu.de/NW750/EN/4d/909309eba36e73e10000000a15822b/content.htm)  
[병렬처리 프로세싱(Parallel Process)](https://saphana.co.kr/451)