---
layout: post
title: "ABAP 파일 업로드, 다운로드"
date: "2022-09-07 19:30:00"
categories: [SAP]
tags: [SAP, ABAP]
---

## 파일을 저장할 테이블 및 도메인 생성

`RAWSTRING` 기반 도메인 정의

파일을 16진수 값으로 변환 후 테이블에 저장할 것이기 때문에 
rawstring 데이터 타입을 갖는 도메인을 생성한다.

![1](https://i.imgur.com/vK9AQPN.png)

![2](https://i.imgur.com/TsbSlb4.png)

도메인 정의 후 파일을 저장할 테이블에 해당 도메인을 추가한 후
만약 Enhancement Category 관련 에러가 날 경우

![3](https://i.imgur.com/qg3pecf.png)

Enhancement Category 관련 설정을 해준다.

![4](https://i.imgur.com/DojfZua.png)

나는 간단하게 위와 같은 구조로 테이블을 생성하였음

### 파일이 실제 저장되는 형식

![5](https://i.imgur.com/8j0Mkm4.png)

- 업로드 시에는 파일 raw data가 rawstring으로 변환되어 테이블에 위와 같은 형태로 저장되고,
- 저장된 파일을 사용자가 로컬에 내려받을 때는 테이블에 저장되어 있는 16진수 형식의 문자열을 SCMS_XSTRING_TO_BINARY 함수를 통해 다시 raw 데이터로 변환하여 원래 파일 형태로 복원한다.

# 코드

---

### 전역 변수(TOP)

```abap
DATA: gt_file_tab LIKE "위에서 생성한 테이블명
 OCCURS 0 WITH HEADER LINE.
```

### 업로드할 파일 로컬 경로 가져오기

```abap
FORM get_file_path  CHANGING pv_file_path TYPE string
                             pv_filename TYPE string
                             pv_is_success TYPE c.

  DATA: lv_file_path TYPE localfile,
        lt_path TYPE TABLE OF string WITH HEADER LINE,
        lv_size TYPE i,
        lv_filename(255),
        lv_file_extension(255).

  CALL FUNCTION 'F4_FILENAME' "파일 업로드 창 띄워주는 함수
    IMPORTING
      file_name = lv_file_path.

	"엑셀 파일만 업로드되도록 제한(확장자 .xls, .xlsx 인지 확인)
  pv_file_path = lv_file_path.
  SPLIT pv_file_path AT '\' INTO TABLE lt_path.
  DESCRIBE TABLE lt_path LINES lv_size.

  READ TABLE lt_path INDEX lv_size.
  IF sy-subrc EQ 0.
    SPLIT lt_path AT '.' INTO pv_filename lv_file_extension.
    TRANSLATE lv_file_extension TO UPPER CASE.

    IF lv_file_extension NE 'XLS' AND
       lv_file_extension NE 'XLSX'.
      MESSAGE i000 WITH '엑셀 파일만 업로드 가능합니다.'.
      pv_is_success = 'E'.
    ENDIF.
  ENDIF.

ENDFORM.                    " GET_FILE_PATH
```

### 파일 업로드

```abap
FORM upload_file .
  DATA: lv_file_path TYPE string,
        lv_file_path_string TYPE string,
        lv_filename TYPE string,
        lv_file_size TYPE i,
        lv_upload_success TYPE c,
        lt_data TYPE STANDARD TABLE OF tdline,
        xstr_content TYPE xstring.
				

  PERFORM get_file_path CHANGING lv_file_path
                                 lv_filename
                                 lv_upload_success.
  CHECK lv_file_path IS NOT INITIAL AND
        lv_upload_success NE 'E'.

  CALL FUNCTION 'GUI_UPLOAD'
    EXPORTING
      filename                = lv_file_path
      filetype                = 'BIN' "BIN 타입 설정하여 raw data로 변환
    IMPORTING
      filelength              = lv_file_size
    TABLES
      data_tab                = lt_data
    EXCEPTIONS
      file_open_error         = 1
      file_read_error         = 2
      no_batch                = 3
      gui_refuse_filetransfer = 4
      invalid_type            = 5
      no_authority            = 6
      unknown_error           = 7
      bad_data_format         = 8
      header_not_allowed      = 9
      separator_not_allowed   = 10
      header_too_long         = 11
      unknown_dp_error        = 12
      access_denied           = 13
      dp_out_of_memory        = 14
      disk_full               = 15
      dp_timeout              = 16
      OTHERS                  = 17.

  IF sy-subrc GT 1.
    MESSAGE '파일 업로드에 실패하였습니다.' TYPE 'I'.
    RETURN.
  ENDIF.

  "raw data를 SAP Table에 저장하기 위해 xstring으로 변환
  CALL FUNCTION 'SCMS_BINARY_TO_XSTRING'
    EXPORTING
      input_length       = lv_file_size
   IMPORTING
     buffer             = xstr_content
    TABLES
      binary_tab         = lt_data
   EXCEPTIONS
     failed             = 1
     OTHERS             = 2
            .
  IF sy-subrc <> 0.
    MESSAGE '파일 데이터 변환에 실패하였습니다.' TYPE 'I'.
    RETURN.
  ENDIF.

  CLEAR gt_file_tab.
  gt_file_tab-filename = lv_filename.
  gt_file_tab-filedata = xstr_content.
	
  "저장
  MODIFY table_name FROM gt_file_tab.

ENDFORM.                    " UPLOAD_FILE
```

### 파일 다운로드

```abap
FORM get_file .

  DATA: lv_filename TYPE string,
        lv_file_path TYPE string,
        lv_file_size TYPE i,
        xstr_content TYPE xstring,
        lt_data TYPE STANDARD TABLE OF tdline.

  READ TABLE gt_file_tab WITH KEY "키 값
  IF sy-subrc NE 0.
    MESSAGE I000 WITH '파일이 존재하지 않습니다.'.
    RETURN.
  ENDIF.

  xstr_content = gt_file_tab-filedata.
	
	"파일을 저장할 로컬 경로 지정
  CALL FUNCTION 'WS_FILENAME_GET'
    EXPORTING
      def_filename     = gt_file_tab-filename
      def_path         = 'C:\'
      mask             = ',*.XLS,*.XLS.'
      mode             = 'S'
      title            = '파일 저장 경로 지정'
    IMPORTING
      filename         = lv_file_path
    EXCEPTIONS
      selection_cancel = 3
      OTHERS           = 5.

  CHECK sy-subrc EQ 0.

  "테이블에 저장되어 있던 xstring 값을 raw data로 변환
  CALL FUNCTION 'SCMS_XSTRING_TO_BINARY'
    EXPORTING
      buffer        = xstr_content
    IMPORTING
      output_length = lv_file_size
    TABLES
      binary_tab    = lt_data.

  IF sy-subrc <> 0.
    MESSAGE '파일 데이터 변환이 불가능합니다.'
      TYPE 'E'.
  ENDIF.

  CALL FUNCTION 'GUI_DOWNLOAD'
    EXPORTING
      bin_filesize            = lv_file_size
      filename                = lv_file_path
      filetype                = 'BIN'
    TABLES
      data_tab                = lt_data
    EXCEPTIONS
      file_write_error        = 1
      no_batch                = 2
      gui_refuse_filetransfer = 3
      invalid_type            = 4
      no_authority            = 5
      unknown_error           = 6
      header_not_allowed      = 7
      separator_not_allowed   = 8
      filesize_not_allowed    = 9
      header_too_long         = 10
      dp_error_create         = 11
      dp_error_send           = 12
      dp_error_write          = 13
      unknown_dp_error        = 14
      access_denied           = 15
      dp_out_of_memory        = 16
      disk_full               = 17
      dp_timeout              = 18
      file_not_found          = 19
      dataprovider_exception  = 20
      control_flush_error     = 21
      OTHERS                  = 22.
  IF sy-subrc <> 0.
    MESSAGE '파일 다운로드에 실패하였습니다.'
      TYPE 'I'.
  ENDIF.

ENDFORM.                    " GET_FILE
```

테스트 해보면 파일이 깨지지 않고 업로드/다운로드 시 정상적으로 변환되는 것을 확인할 수 있을 것이다.

~~거의 1년만의 포스팅~~