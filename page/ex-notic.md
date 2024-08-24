
## 공지 입니다

요구사항은 관리자에서만 입력하고, 관리목은 구분, 제목, 내용

단일 파이롤 만들어도 되지만, 공통파일을 관리할 수 있다.


## 파일 구조


파일구조
- base-svc.js
- base-notice-svc.js : 공지 기본 서비스 객체 입니다.
- notice-front-svc.js : 사용자 공지 서비스 객체 입니다.
- notice-admin-svc.js : 관리자 공지 서비스 객체 입니다.
- /admin/notice.asp : 관리자 페이지 입니다.
- /front/notice.html : 사용자 페이지 입니다.


### 최상위 서비스

서비스에 대한 최상위 입니다.

실패와 오류 시 메세지를 정의합니다.
모든 서비스 객체의 공통의 적용됩니다.

```js
class BaseService {

    this.cbFail = function(p_result, p_item) {          // 전역 실패 콜백
        console.warn("실패 :: Value=\"%s\", Code=\"%s\", Message=\"%s\" ", p_result.value, p_result.code, p_result.msg);
        Msg("ALERT", "실패", p_result.msg);
    };
    this.cbError = function(p_msg, p_status) {          // 전역 오류 콜백
        console.warn("오류 :: Status=\"%s\" , Message=\"%s\" ", p_status, p_msg);
        Msg("ALERT", "오류", p_msg);
    };
}

```

### 공지 공통 서비스

공지사항에 대한 items 와 fn 에 대한 고통 속성을 설정합니다.

```js
class BaseNoticeService extends BaseService {

    this.items = {
        // inner
        __isGetLoad:    true,
        __mode:         '',
        // view
        _temp_list:     { selector: { key: '#s-temp-list'+ _SUFF,       type: 'html' } },
        _area_list:     { selector: { key: '#s-area-list'+ _SUFF,       type: 'html' } },
        _area_page:     { selector: { key: '#s-area-page'+ _SUFF,       type: 'html' } },
        _txt_sumCnt:    { selector: { key: '#s-txt-sumCnt'+ _SUFF,      type: 'text' } },
        // bind
        cmd:            '',
        keyword:        { selector: { key: '#m-keyword'+ _SUFF,         type: 'value' } },
        page_size:      {
            selector: { key: 'select[name=m-page_size]'+ _SUFF,         type: 'value' },
            getter: () => { return page.page_size; },
            setter: (val) => { page.page_size = val; },
        },
        page_count:     {   /** 값을 외부에서 관리함! */
            getter: () => { return page.page_count; },
            setter: (val) => { return page.page_count = val; }
        },              
        sort_cd:        '',
        evt_idx:        '',
        title:          { 
            selector: { key: '#m-title'+ _SUFF,        type: 'value' },
            isNotNull: true,
        },
        writer:         { selector: { key: '#m-writer'+ _SUFF,       type: 'value' } },
        begin_dt:       { selector: { key: '#m-begin_dt'+ _SUFF,     type: 'value' } },
        close_dt:       { selector: { key: '#m-close_dt'+ _SUFF,     type: 'value' } },
        contents:       { selector: { key: '#m-contents'+ _SUFF,     type: 'value' } },
        active_yn:      { 
            selector: { key: 'input[name=m-active_yn'+_SUFF+'][type=radio]',  type: 'none' },
            setFilter(val) { 
                $('input[name=m-active_yn'+_SUFF+'][value='+ val + ']').prop('checked', true);
            },
            getFilter(val) {
                return $('input[name=m-active_yn'+_SUFF+']:checked').val();
            },
            isNotNull: true,
        },
    };
    
    this.fn = {
        searchList() {
            _this.bindModel.items['page_count'].value = 1;
            _this.bindModel.list.execute();
        },
        changePagesize(e) {
            _this.bindModel.items['page_size'].value = this.value;
            _this.bindModel.items['page_count'].value = 1;
            _this.bindModel.list.execute();
        },
        resetForm() { 
            $('form').each( () => {
                this.reset();
            });
        },
        // moveList() {
        //     var url = _this.bindModel.prop['__listUrl'];
        //     location.href = url;
        // },
        // moveEdit(p_evt_idx) {
        //     var url = _this.bindModel.prop['__formUrl'];
        //     location.href = url +'?mode=EDIT&evt_idx='+ p_evt_idx;
        // },
        // moveForm() {
        //     var url = _this.bindModel.prop['__formUrl'];
        //     location.href = url;
        // },
        procRead(p_evt_idx) { 
            _this.bindModel.items['evt_idx'].value = ParamGet2JSON(location.href).evt_idx;
            _this.bindModel.read.execute(); 
        }
    };

    // 전처리
    this.preRegister = function(bimdmodel) {
        // 초기값 설정 : 서버측 > 파라메터 > 내부(기본값)
        p_bindModel.items['keyword'].value = decodeURI(getArgs('', getParamsToJSON(location.href).keyword ));
        p_bindModel.items['page_count'].value  = Number( getArgs('', getParamsToJSON(location.href).page_count, page.page_count) );
        
        // page 콜백 함수 설정 (방식)
        if (p_bindModel.prop['__isGetLoad'] === true) {
            page.callback = page.goPage.bind(p_bindModel.list.bind);            // 2-1) GET 방식
        } else {
            page.callback = p_bindModel.list.execute.bind(p_bindModel.list);    // 1) 콜백 방식
        }
    }
}
```

### 프론트 서비스

프론트 페이지에 대한 command, mapping 을 설정합니다.

```js
class NoticeFrontService extends BaseNoticeService {

    this.command = {
        read:       {
            outputOption: 3,
            onExecute(p_bindCommand) { _this.bindModel.items['cmd'].value = 'READ'; },
            cbEnd(p_entity) {
                if (p_entity['return'] < 0) return alert('조회 처리가 실패 하였습니다. Code : ' + p_entity['return']);
            }
        },
        list:       {
            outputOption: 1,
            onExecute(p_bindCommand) { _this.bindModel.items['cmd'].value = 'LIST'; },
            cbOutput(p_result) {
                if (global.isLog) console.log("[Service] list.cbOutput() : 목록출력");

                var entity = p_result['table'];
                var row_total   = entity['row_total'];

                if (_template === null) {
                    _template = Handlebars.compile( _this.bindModel.items['_temp_list'].value ); 
                }
                _this.bindModel.items['_txt_sumCnt'].value  = row_total;
                _this.bindModel.items['_area_list'].value   = _template(entity);
                _this.bindModel.items['_area_page'].value   = page.parser(row_total);
            },
            cbEnd(p_result) {
                if (p_result['return'] < 0) return alert('목록조회 처리가 실패 하였습니다. Code : ' + p_result['return']);
            }
        },
    };

    this.mapping = {
        _temp_list:     { list:     'etc' },    // 묶음의 용도
        _area_list:     { list:     'etc' },    // 묶음의 용도
        _area_page:     { list:     'etc' },    // 묶음의 용도
        cmd:            { $all:     'bind' },
        keyword:        { list:     'bind' },
        page_size:      { list:     'bind' },
        page_count:     { list:     'bind' },
        sort_cd:        { list:     'bind' },
        evt_idx:        { read:     'bind' },
        title:          { read:     'output' },
        writer:         { read:     'output' },
        contents:       { read:     'output' },
        create_dt:      { read:     'output' },
    };
}
```


### 관리자 서비스

관리자 페이지에 대한 command, mapping 을 설정합니다.

/admin/

```js
class NoticeAdminService extends BaseNoticeService {

    this.command = {
        create:     {
            onExecute(p_bindCommand) { _this.bindModel.items['cmd'].value = 'CREATE'; },
            cbEnd(p_entity) {
                if (p_entity['return'] < 0) return alert('등록 처리가 실패 하였습니다. Code : ' + p_entity['return']);
                _this.bindModel.fn.moveList();  // 개선함
            },
        },
        read:       {
            outputOption: 3,
            onExecute(p_bindCommand) { _this.bindModel.items['cmd'].value = 'READ'; },
            cbEnd(p_entity) {
                if (p_entity['return'] < 0) return alert('조회 처리가 실패 하였습니다. Code : ' + p_entity['return']);
            }
        },
        update:     {
            onExecute(p_bindCommand) { _this.bindModel.items['cmd'].value = 'UPDATE'; },
            cbEnd(p_entity) {
                if (p_entity['return'] < 0) return alert('수정 처리가 실패 하였습니다. Code : ' + p_entity['return']);
                alert('수정 처리가 되었습니다.');
                _this.bindModel.read.execute();
            }
        },
        delete:     {
            onExecute(p_bindCommand) { _this.bindModel.items['cmd'].value = 'DELETE'; },
            cbValid(p_valid) { return confirm('삭제 하시겠습니까.?'); },
            cbEnd(p_entity) {
                if (p_entity['return'] < 0) return alert('삭제 처리가 실패 하였습니다. Result Code : ' + p_entity['return']);
                _this.bindModel.fn.moveList();  // 개선함
            }
        },
        list:       {
            outputOption: 1,
            onExecute(p_bindCommand) { _this.bindModel.items['cmd'].value = 'LIST'; },
            cbOutput(p_result) {
                if (global.isLog) console.log("[Service] list.cbOutput() : 목록출력");

                var entity = p_result['table'];
                var row_total   = entity['row_total'];

                if (_template === null) {
                    _template = Handlebars.compile( _this.bindModel.items['_temp_list'].value ); 
                }
                _this.bindModel.items['_txt_sumCnt'].value  = row_total;
                _this.bindModel.items['_area_list'].value   = _template(entity);
                _this.bindModel.items['_area_page'].value   = page.parser(row_total);
            },
            cbEnd(p_result) {
                if (p_result['return'] < 0) return alert('목록조회 처리가 실패 하였습니다. Code : ' + p_result['return']);
            }
        }
    };
    
    this.mapping = {
        _temp_list:     { list:     'etc' },    // 묶음의 용도
        _area_list:     { list:     'etc' },    // 묶음의 용도
        _area_page:     { list:     'etc' },    // 묶음의 용도
        _txt_sumCnt:    { list:     'etc' },    // 묶음의 용도
        cmd:            { Array:    'bind' },
        keyword:        { list:     'bind' },
        page_size:      { list:     'bind' },
        page_count:     { list:     'bind' },
        sort_cd:        { list:     'bind' },
        evt_idx:        { read:     'bind',     delete:     'bind',            update:  'bind' },
        title:          { read:     'output',   create:     ['valid', 'bind'], update:  ['valid', 'bind'], },
        writer:         { read:     'output',   create:     'bind',            update:  'bind' },
        begin_dt:       { read:     'output',   create:     'bind',            update:  'bind' },
        close_dt:       { read:     'output',   create:     'bind',            update:  'bind' },
        contents:       { read:     'output',   create:     'bind',            update:  'bind' },
        active_yn:      { read:     'output',   create:     ['valid', 'bind'], update:  ['valid', 'bind'], },
    };


    
}

```



### 관리자 페이지

관리자 페이지는 asp 페이지로 작성하였습니다.

```html
<!-- 관리자 세션 검사 -->
<!--#include virtual="/Admin/adm_cmn/include/Admin_Global_Define.I.asp"-->
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <meta name="copyright" content="Copyright(c) White Lab Inc. All Rights Reserved." />
    <title>Admin</title>
    <link rel="stylesheet" type="text/css" href="/css/suio.css" media="screen" charset="utf-8">
    <link rel="stylesheet" type="text/css" href="/css/layout.css" media="screen" charset="utf-8">
    
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.2/jquery.min.js"></script>		
    <script src="/Common/js/jquery.xml2json.js"></script>
	<script src="/Common/js/common.js?a"></script>
	<script src="/Admin/adm_cmn/js/admin_common.js?v2"></script>
    
</head>
<body>

<div style="width:1020px">   


    <form id="frm_default" name="frm_default" method="post">
                
	<!--###############  검색 조건 Block ###############-->

	<div class="section" id="QA_profile1">
  		<div>&nbsp;</div>
        <div>&nbsp;</div>                        
        <div class="optionArea">
            <div class="mOption" style="display: block;">
                <table border="1" summary="">
                <caption>회원정보 조회</caption>
                <colgroup>
                    <col style="width:154px;*width:135px;" />
                    <col style="width:auto;" />
                    <col style="width:154px;*width:135px;" />
                    <col style="width:auto;" />
                </colgroup>
                <tbody>
                    <!--
                    <tr>
                        <th scope="row">상위공지 여부 </th>
                        <td>
                            <label class="gLabel"><input type="radio" name="real_type" value="A" checked class="fChk" /> 일반</label>
                            <label class="gLabel"><input type="radio" name="real_type" value="R" class="fChk" /> 상위공지</label>
                        </td>
                        <th scope="row">팝업 여부 </th>
                        <td>
                            <label class="gLabel"><input type="radio" name="real_type2" value="A" checked class="fChk" /> 팝업</label>
                            <label class="gLabel"><input type="radio" name="real_type2" value="R" class="fChk" /> 상위공지</label>
                        </td>
                    </tr>
                    -->
                    <tr>
                        <th scope="row">제목</th>
                        <td  colspan="3">
                            <input type="text" id="m-keyword" name="name_corp_tel_hp" value="" class="fText" style="width:200px;" />
                        </td>
                    </tr>
                    </tbody>
                </table>
            </div>
        </div>
		<!--###############  검색 버튼 Block ###############-->
		<div class="mButton gCenter">
		    <a href="#none" id="btn_Search" class="btnSearch"><span>검색</span></a>
		    <a href="#none" id="btn_Reset" class="btnSearch reset"><span>초기화</span></a>
		</div>
    </div>

	<!--###############  검색 결과 Block ###############-->
	<div class="section" id="QA_level2">
	    <div class="mState">
	        <div class="gLeft">
	            <p class="total">검색결과 <strong id="s-txt-sumCnt">0</strong>건</p>
            </div>
	        <div class="gRight">
	            <select id="sel_Pagesize" name="m-page_size" class="fSelect">
	                <option value="10" selected>10개씩보기</option>
	                <option value="20">20개씩보기</option>
	                <option value="30">30개씩보기</option>
	                <option value="50">50개씩보기</option>
	                <option value="100">100개씩보기</option>
	            </select>
	        </div>
	    </div>
	    <div class="mCtrl typeHeader">
	        <div class="gLeft">
                
                <a href="#none" id="btn_Insert" class="btnNormal"><span> 등록</span></a>
                
	        </div>
	        <div class="gRight">
                <a href="#none" id="btn_Excel" title="새창 열림" class="btnNormal"><span><em class="icoXls"></em> 엑셀다운로드<em class="icoLink"></em></span></a>
            </div>
	    </div>
	    <div class="mBoard gCellNarrow">
	        <table border="1" summary="" class="eChkColor">
		        <caption>목록</caption>
		        <!--###############  제목 크기 Block ###############-->
		        <colgroup>
		            <col class="date">
                    <col style="width:auto">
                    <col style="width:auto">
                    <col style="width:auto">
                    <col style="width:auto">
                    <col style="width:auto">
		        </colgroup>
		        <!--###############  검색 제목 Block ###############-->
		        <thead>
		            <tr>
		                <th scope="col"><strong>순번</strong></th>
                        <th scope="col"><strong>작성일자</strong></th>
                        <th scope="col"><strong>제목</strong></th>
		                <th scope="col"><strong>작성자</strong></th>
                        <th scope="col"><strong>활성유무</strong></th>
                        <th scope="col"><strong>처리</strong></th>
		            </tr>
		        </thead>
				
				<!--###############  검색 본문 Block ###############-->
				<tbody class="center" id="s-area-list">
    				<tr><td colspan='6' align='center'>자료가 없습니다.</td></tr>
				</tbody>

                <script id="s-temp-list" type="text/x-handlebars-template">
                    {{#rows}}
                    <tr class="">
                        <td>{{row_count}}</td>
                        <td>{{create_dt}}</td>
                        <td>{{title}}</td>
                        <td>{{writer}}</td>
                        <td>{{active_yn}}</td>
                        <td><a href="javascript:evt.fn.moveEdit('{{evt_idx}}');" class='btnNormal'><span> 조회</span></a></td>
                    </tr>
                    {{/rows}} 
                </script>           
			</table>
	    </div>
	    
	    <!--###############  페이지 Block ###############-->
	    <div id="s-area-page" class="mPaginate"></div>
	</div>

	<!--###############  처리중 Block ###############-->
	<div class="mLoading typeStatic">
	    <p>처리중입니다. 잠시만 기다려 주세요.</p>
	    <img src="http://img.echosting.cafe24.com/suio/ico_loading.gif" alt="" />
	</div>
	
	<!--###############  페이지 Block ###############-->

    </form>
    <!-- <link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">-->

	<div id="part_Overlay" class="w3-overlay w3-animate-opacity" style="cursor:pointer;z-index:90;"></div>

</div>        


<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.7.8/handlebars.min.js"></script>
<script src="https://unpkg.com/logic-bind-model/dist/bindmodel.pack.js"></script>

<script src="/Service/base-svc.js"></script>
<script src="/Service/base-notice-svc.js"></script>
<script src="/Admin/noitce-svc.js"></script>
<script>
	// ##################################################
	var evt = new _L.BindModel(new AdminNoticeService());
	
	// 콜백 url 설정
    evt.url = '/Admin/Notice.C.asp';

	// 이벤트 바인딩
	$('#btn_Search').click(evt.fn.searchList);
	$('#sel_Pagesize').change(evt.fn.changePagesize);
	$('#btn_Reset').click(evt.fn.resetForm);
	$('#btn_Insert').click(evt.fn.moveForm);

	$(document).ready(function () {
        evt.init();
		evt.fn.procList();
    });
</script>

</body>
</html>   

```



### 프론트 페이지


```html
<!doctype html>
<html>
<head>
<!--#include virtual="/Front/PC/include/HeadTag.I.asp"-->
    <link type="text/css" rel="stylesheet" href="/Front/PC/css/board.css?ts=<%=g_iRandomID%>" />

</head>

<body class="body-main body-index pc"  >
    <div id="warp">

        <div id="container">
            <div id="contents">
                <div id="sub_content">


                    <div class="content">
                    <div class="board_zone_sec">
                        <div class="board_zone_tit">
                            <h2>이벤트</h2>
                        </div>
                        <!--게시판 주소 -->
                        <div class="esotitleline">
                        </div>

                        <!--게시판 주소 -->
                        <div class="board_zone_cont">
                            <div class="board_zone_list" align="center">
                                <table class="board_list_table" style="width:100%" "="">
                                    <colgroup>
                                        <col style="width:6%">
                                        <col style="width:37%;">
                                        <col style="width:12%">
                                        <col style="width:15%">
                                    </colgroup>
                                    <thead>
                                    <tr>
                                        <th>번호</th>
                                        <th>제목</th>
                                        <th>날짜</th>
                                        <th>작성자</th>
                                        <!--<th>조회</th>-->
                                    </tr>
                                    </thead>
                                    <tbody id="s-area-list">
                                    </tbody>
                                    <script id="s-temp-list" type="text/x-handlebars-template">
                                        {{#rows}} 
                                        <tr data-sno="3" data-auth="y" style="height:10px">
                                            <td>
                                                {{row_count}}
                                            </td>
                                            <td class="board_tit">
                                                
                                                <a href="javascript:evt.fn.moveView({{evt_idx}})">
                                                    {{title}}
                                                    <img src="/Front/PC/img/icon_board_hot.png" alt="인기글">
                                                </a>
                                            </td>
                                            <td>  {{create_dt}} </td>
                                            <td> {{writer}} </td>
                                            <!--<td> 765 </td>-->
                                        </tr>
                                        {{/rows}} 
                                    </script>                                    

                                </table>

                                <!--<div class="pagination" id="CPage"><ul><li class="on"><span>1</span></li></ul></div>-->
                                <div class="pagination" id="s-area-page"></div>
                                <!-- //pagination -->

                                <div class="board_search_box">

                                        <input type="hidden" name="bdId" value="notice">
                                        <input type="hidden" name="memNo" value="">
                                        <input type="hidden" name="noheader" value="">

                                        <select class="chosen-select" name="searchField" style="display:;">
                                            <option value="subject">제목</option>
                                            <option value="contents">내용</option>
                                            <option value="writerNm">작성자</option>
                                        </select>

                                        <input type="text" id="m-keyword" class="text" name="searchWord" value="">
                                        <button id="btn_Search" class="btn_board_search"><em>검색</em></button>

                                </div>
                                <!-- //board_search_box -->

                            </div>
                            <!-- //board_zone_list -->


                        </div>
                        <!-- //board_zone_cont -->
                    </div>
                    <!-- //board_zone_sec -->

                    <form id="frmWritePassword">
                        <div id="lyPassword" class="dn layer_wrap password_layer" style="height: 226px">
                            <div class="layer_wrap_cont">
                                <div class="ly_tit">
                                    <h4>비밀번호 인증</h4>
                                </div>
                                <div class="ly_cont">
                                    <div class="scroll_box">
                                        <p>비밀번호를 입력해 주세요.</p>
                                        <input type="password" name="writerPw" class="text">
                                    </div>
                                    <!-- // -->
                                    <div class="btn_center_box">
                                        <button type="button" class="btn_ly_password js_submit"><strong>확인</strong></button>
                                    </div>
                                </div>
                                <!-- //ly_cont -->
                                <!--<a href="#close" class="ly_close layer_close"><img src="/data/skin/front/lady2019_C/img/common/layer/btn_layer_close.png" alt="닫기"></a> -->
                            </div>
                            <!-- //layer_wrap_cont -->
                        </div>
                        <!-- //layer_wrap -->
                    </form>

                    <div id="layerDim" class="dn">&nbsp;</div>
                    <!-- <script type="text/javascript" src="/data/skin/front/lady2019_C/js/gd_board_list.js" charset="utf-8"></script> -->
                    <script>
                        $(document).ready(function () {
                            $('img.js_image_load').error(function () {
                                        $(this).css('background', 'url("/data/skin/front/lady2019_C/board/skin/default/img/etc/noimg.png") no-repeat center center');
                                        $(this).attr('src', '/data/skin/front/lady2019_C/img/etc/blank.gif');
                                    })
                                    .each(function () {
                                        $(this).attr("src", $(this).attr("src"));
                                    })
                        });
                    </script>
                    </div>

                </div>
                <!-- //sub_content -->
            </div>
        </div>
         <!-- //container -->

        <div id="footer_wrap">
<!--#include virtual="/Front/PC/include/Footer.I.asp"-->
        </div>
        <!-- //footer_wrap --> 
    </div>

<script src="/Common/js/handlebars.js"></script>
<script src="/Common/js/_w-meta-1.6.1.js?<%=g_iRandomID%>"></script>
<script src="/Front/frt_cmn/Service/base-page-svc.js?<%=g_iRandomID%>"></script>
<script src="/Front/frt_mod/BOD/Service/board-event-svc.js?<%=g_iRandomID%>"></script>
<script>
	// #######################################################################################################
	var evt = new _W.BindModelAjax(new BoardEventService());
    
    this.isLog = true;  // 디버깅 모드
	
	// 속성 설정
	evt.prop["__viewUrl"] = "event_Viw.asp";
	evt.prop['__isGetLoad'] = false;

	// 이벤트 바인딩
	$('#btn_Search').click(evt.fn.searchList);
    //--------------------------------------------------------------
	$(document).ready(function () {
        evt.init();
		evt.fn.procList();
    });
	if (this.isLog) console.log("______________ $.ready()");
</script>

</body>
</html>

```


