---
layout: post
title: "ABAP checkbox refresh"
date: "2021-08-27 19:30:21"
categories: [SAP]
tags: [SAP, ABAP, ALV]
---



> 특정조건에서 체크박스 체크 해제시, 팝업창을 띄우고,   
> 결과에 따라 체크박스값을 원복해주는 ALV refresh 로직 구현에 애를 먹었다.  
> ALV refresh 해도 체크박스 값 업데이트가 안됨..ㅠ ☠︎

 <br/>

처음엔 체크박스 클릭 시 data_changed 로직을 무조건 타니까, 이 로직만 조금 건드려주면 10분 안에 구현될 줄 알았다.

data_changed 이벤트 내에서 팝업창 결과에 따라 modify_cell 메소드를 호출해서 itab 내에서 값을 업데이트해주고, refresh_table_display 메소드를 호출하면 alv가 새로고침되면서 당연히 갱신될줄 알았는데 아니었음.. 인터널테이블 값만 업데이트되고, alv 부분은 refresh_table_display 메소드를 호출하고 별 짓을 다해도 체크박스 값 갱신이 안됨;;  ☠︎   
구글링해도 안 나옴..ㅠ

 <br/>

### 해결책은 refresh_table_display 메소드가 `data_changed_finished` 이벤트 트리거 시 호출되도록 하는 것!!!

 <br/>

정확한 이유는 모르겠지만, 팝업창이 뜨기 전 체크박스를 해제했을때 data_changed 로직을 이미 한번 타서 해제가 되었는데
팝업창 결과에 따라 다시 원복하기 위해 data_changed 이벤트내에서 refresh 메소드를 호출해봤자, data_changed 이벤트는 이미 호출된 상태여서 갱신이 일어나지 않는 것같다.

대신 data_changed_finished 이벤트에 `refresh_table_display` 메소드를 호출하면,  
변경사항이 제대로 갱신됨.





- data_changed는 편집 가능한 필드의 데이터가 변경될때 트리거
- data_changed_finished는 데이터 수정이 완료되면 트리거  



<br/>



---

클래스 구현 부분 

{% highlight abap %}

"EVENT_RECEIVER 클래스 def

CLASS EVENT_RECEIVER DEFINITION.

​	PUBLIC SECTION.

​		METHODS: CONSTRUCTOR

​			IMPORTING E_OBJECT_TEXT TYPE C,

 

​		HANDLE_DATA_CHANGED

​			FOR EVENT DATA_CHANGED OF CL_GUI_ALV_GRID

​			IMPORTING ER_DATA_CHANGED e_ucomm,

 

​		HANDLE_DATA_CHANGED_FINISHED

​			FOR EVENT DATA_CHANGED_FINISHED OF CL_GUI_ALV_GRID

​			IMPORTING ER_DATA_CHANGED e_ucomm,

​		~~~ 중략. 

ENDCLASS.

 

"EVENT_RECIEVER 클래스 impl

CLASS EVENT_RECIEVER IMPLEMENTATION.

​	METHOD CONSTRUCTOR.

​		CALL METHOD SUPER->CONSTURCTOR.

​	ENDMETHOD.

 

​	METHOD HANDLE_DATA_CHANGED.

​		PERFORM handle_data_changed_grid USING er_data_changed e_ucomm.

​	ENDMETHOD.

 

​	"핵심!!!!!!!!!!!!!!!!!!!!!

​	METHOD HANDLE_DATA_CHANGED_FINISHED.

​		PERFORM HANDLE_DATA_CHANGED_FINISHED_grid USING E_MODIFIED ET_GOOD_CELLS.

​	ENDMETHOD.



​	~~~ 중략.

ENDCLASS.

{% endhighlight %}

 ---

서브루틴 구현 부분

{% highlight abap %}

"data changed finished 이벤트구현함수

FORM handle_data_changed_finished_grid USING p_modified pt_good_cells.

​	CHECK p_modified IS NOT INITIAL.

​	PERFORM refresh_grid USING grid.

ENDFORM

 

"alv refresh 메소드

FORM refresh_grid USING p_grid TYPE REF TO cl_gui_alv_grid.

​	gs_stable_row = 'X'. "refresh 하기전현재 row 위치저장

​	gs_stable_col = 'X'. "refresh 하기전현재 col 위치저장

 

​	CALL METHOD p_grid->refresh_table_display

​		EXPORTING

​			is_stable = gs_stable.

ENDFORM.

 

"data_changed 구현

FORM handle_data_changed_grid USING ps_data_changed TYPE REF TO cl_alv_changed_data_protocol p_ucomm TYPE sy-ucomm.

​	DATA: ls_good TYPE lvc_s_modi,

​	lt_itab LIKE TABLE OF (테이블),

​	ls_itab LIKE LINE OF lt_itab.

 

​	LOOP AT ps_data_changed->mt_good_cells INTO ls_good.

​		READ TABLE (alv 인터널테이블변수) INDEX ls_good-row_id. 

​		CASE ls_good-fieldname.

​			WHEN 'ALV 필드명'.

​				PERFORM change_field USING ls_good ps_data_changed.

​		ENDCASE.

​	ENDLOOP.

 

​	PERFORM refresh_grid USING grid.

ENDFORM.

 

FORM change_field USING ps_changed TYPE lvc_s_modi pt_changed TYPE REF TO cl_alv_changed_data_protocol.

 

​	DATA: l_var(40),

​	l_err TYPE c.

 

​	"현재바뀐 ALV 필드값 가져오기

​	CALL METHOD pt_changed->get_cell_value 

​		EXPORTING

​			i_row_id = ps_changed-row_id

​			i_fieldname = 'ALV 필드명'

​		IMPORTING

​			e_value = l_var.

 

​	"바꾸고자하는값적용

​	CALL METHOD pt_changed->modify_cell

​		EXPORTING

​			i_row_id = ps_changed-row_id

​			i_fieldname = 'ALV 필드명'

​			i_value = "바꾸고자 하는 값".

 {% endhighlight %}

---

PBO 

{%highlight abap %}

"ALV 생성해주는 모듈 부분에 핸들러 추가

SET HANDLER g_event_rece->handle_data_changed_finished FOR grid.

 {% endhighlight %}

---

 

data_chagned 메소드에서 원하는대로 동작이 안 된다면,  
data_changed_finsihed 메소드에 답이 있을지도..?



코드 하이라이트 부분 줄바꿈이 너무 지저분하니
폰트 크기 조절 좀 해야될 듯

