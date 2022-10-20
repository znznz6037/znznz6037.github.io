---
layout: post
title: "ALV Cell Color 지정"
date: "2022-10-20 20:05:00"
categories: [SAP]
tags: [SAP, ABAP]
---

### ALV Color 지정
Color를 특정 Row 전체에 적용하거나, 특정 Row의 특정 칼럼만 설정해줄 수 있다.

### 인터널 테이블

```abap
*----------------------------------------------------------------------*
* ALV
*----------------------------------------------------------------------*
DATA: go_container TYPE REF TO cl_gui_custom_container,
      go_grid      TYPE REF TO cl_gui_alv_grid,
      gs_layout    TYPE lvc_s_layo,
      gt_fieldcat  TYPE lvc_t_fcat WITH HEADER LINE,
      gt_exclude   TYPE ui_functions,
      gs_stable    TYPE lvc_s_stbl.

*----------------------------------------------------------------------*
* Internal Tables
*----------------------------------------------------------------------*
DATA: BEGIN OF gt_outtab OCCURS 0.
        INCLUDE TYPE ~~~
DATA:   info_fname(10), "특정 행 전체 적용 시
        cell_col TYPE lvc_t_scol, "특정 행 특정 칼럼 적용 시
      END OF gt_outtab.
```

인터널 테이블에 info_fname, cell_col 선언
변수명은 마음대로 ~ 타입만 신경써서 선언해주자.

### PBO

```abap
*&---------------------------------------------------------------------*
*&      Module  CREATE_ALV  OUTPUT
*&---------------------------------------------------------------------*
MODULE create_alv OUTPUT.

  IF go_container IS INITIAL.

     PERFORM create_grid USING 'CONTAINER'
                               go_container
                               go_grid.
     PERFORM alv_cl_fieldcatalog TABLES gt_fieldcat[].
     PERFORM alv_cl_layout CHANGING gs_layout.
     PERFORM alv_cl_exclude_fcode TABLES gt_exclude USING ' '.
     PERFORM alv_cl_set_color.
     PERFORM alv_cl_display USING go_grid.

  ELSE.
    PERFORM alv_cl_refresh USING go_grid.

  ENDIF.

ENDMODULE.                 " CREATE_ALV  OUTPUT

*&---------------------------------------------------------------------*
*&      Form  ALV_CL_LAYOUT
*&---------------------------------------------------------------------*
FORM alv_cl_layout  CHANGING ps_layout TYPE lvc_s_layo.
  CLEAR ps_layout.

  ps_layout-cwidth_opt = 'X'.
  ps_layout-sel_mode = 'D'.
  ps_layout-totals_bef  = 'X'.
  ps_layout-info_fname = 'INFO_FNAME'. "특정 행 전체 적용 시
  ps_layout-ctab_fname = 'CELL_COL'. "특정 행 특정 칼럼 적용 시
  ps_layout-zebra = ' '.

ENDFORM.                    " ALV_CL_LAYOUT
```
  

## 1. 특정 행 전체 적용 시

---

```abap
*&---------------------------------------------------------------------*
*&      Form  ALV_CL_SET_COLOR
*&---------------------------------------------------------------------*
FORM alv_cl_set_color .

  LOOP AT gt_outtab.
    IF <condition>. 
      gt_outtab-info_fname = 'C600' "빨간색
      MODIFY gt_outtab.
    ENDIF.

  ENDLOOP.

ENDFORM.                    " ALV_CL_SET_COLOR
```

해당 코드 적용 시 Row 전체에 색깔이 지정된다.

예시)
![1](https://i.imgur.com/yIohcrD.png)
  

## 2. 특정 행의 특정 칼럼만 적용 시

---

```abap
*&---------------------------------------------------------------------*
*&      Form  ALV_CL_SET_COLOR
*&---------------------------------------------------------------------*
FORM alv_cl_set_color .

  LOOP AT gt_outtab.
    IF <condition>.
      PERFORM fill_cell_col USING '필드명' lv_idx.
      MODIFY gt_outtab TRANSPORTING cell_col.
    ENDIF.

  ENDLOOP.

ENDFORM.                    " ALV_CL_SET_COLOR

*&---------------------------------------------------------------------*
*&      Form  FILL_CELL_COL
*&---------------------------------------------------------------------*
FORM fill_cell_col.
  DATA: ls_col TYPE lvc_s_scol,
        lv_fname(30).

  ls_col-fname = <색상 적용할 필드명>.
  ls_col-color-col = 6. "빨간색 
  APPEND ls_col TO gt_outtab-cell_col.

ENDFORM.                    " FILL_CELL_COL
```

예시)
![2](https://i.imgur.com/kvXrkL9.png)
cell_col에 여러개의 필드를 append해주면 하나의 Row에 여러 개 개별 지정 가능