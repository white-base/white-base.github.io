---
lang: ko
title: "BindModel 예제"
layout: fullwidth
permalink: /ko/exam/notice-bindmodel/
date: 2011-06-23T1
# toc: true
# toc_sticky: true
sidebar:
  nav: "exam"
---
## 설명
- "알림"의 예로 관리자 및 사용자 페이지를 들 수 있습니다.
- 웹 디자이너가 제작한 html, css, js 파일을 수정 없이 바로 사용할 수 있습니다.
- 공통 서비스 개체를 분리하여 관리함으로써 반복적인 코드 작성이 필요하지 않도록 효율적인 구조를 제공하여 유지보수 및 확장성을 높입니다.

## 폴더 구조
```js
vue-mix/
├── service/
│   ├── base-notice-svc.js
│   ├── notice-admin-svc.js 
│   └--notice-front-svc.js : ** User Page **
├-- front.html : ** User Page **
└── admin.html
```

### admin.html
---
공지 목록과 양식의 html, css, js로 구성된 페이지입니다. 
물리적 구현 중에 관리자 페이지에 권한 설정을 추가해야 합니다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <title>BindModel Example</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
</head>
<body>
<div class="container mt-3">
  <h2>Notice Admin Page</h2>
  <h5>Key Features: List inquiry/modification/deletion</h5>
  <p>Data is transmitted when modified or deleted from the test page, but it is not actually processed.</p>
  <table class="table">
    <thead>
      <tr>
        <th>Title</th>
        <th>Status</th>
        <th>Date</th>
      </tr>
    </thead>
    <tbody id="area-tbody">
      <tr>
        <td colspan="3">There is no content.</td>
      </tr>
    </tbody>
    {% raw %}
    <!-- Template -->
    <script id="area-temp" type="text/x-handlebars-template">
      {{#rows}}
      <tr>
        <td><a href="javascript:bm.fn.procRead('{{@index}}');" class='btnNormal'>{{title}}</a></td>
        <td>{{active_cd}}</td>
        <td>{{create_dt}}</td>			            	
      </tr>
      {{/rows}} 
    </script>
    {% endraw %}
  </table>

  <div id="class-form" class="d-none">
    <form>
      <div class="form-group">
        <label for="title">날짜</label>
        <p id="create_dt"></p>
    </div>
      <div class="form-group">
          <label for="title">Title</label>
          <input type="text" class="form-control" id="title">
      </div>
      <div class="form-group">
          <label for="contents">Content</label>
          <textarea class="form-control" id="contents" rows="3"></textarea>
      </div>
      <div class="row">
        <div class="col">
            <div class="form-check">
                <input type="checkbox" class="form-check-input" id="check1" name="top_yn" value="Y">
                <label class="form-check-label" for="check1">top notice</label>
            </div>
        </div>
        <div class="col">
            <div class="form-check">
                <input type="radio" class="form-check-input" id="radio1" name="active_cd" value="D" checked>
                <label class="form-check-label" for="radio1">Standby</label>
            </div>
            <div class="form-check">
                <input type="radio" class="form-check-input" id="radio2" name="active_cd" value="A">
                <label class="form-check-label" for="radio2">Activation</label>
            </div>
            <div class="form-check">
                <input type="radio" class="form-check-input" id="radio3" name="active_cd" value="H">
                <label class="form-check-label" for="radio3">Hidden</label>
            </div>        
        </div>
      </div>
    </form>
    <button type="submit" class="btn btn-primary mt-3" id="btn_Update">Update</button>
    <button type="submit" class="btn btn-primary mt-3" id="btn_Delete">Delete</button>
    <button type="submit" class="btn btn-primary mt-3" id="btn_List">List</button>
  </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.7.8/handlebars.min.js"></script>
<script src="https://unpkg.com/logic-bind-model/dist/bindmodel.pack.min.js"></script>
<script src="service/base-notice-svc.js"></script>
<script src="service/notice-admin-svc.js"></script>
<script>
	// ##################################################
	var bm = new _L.BindModel(new NoticeAdminService());

    bm.url = '/notice/data/list.json';  // base url
	// button event
    $('#btn_Update').click(()=> bm.cmd['update'].execute());
	$('#btn_Delete').click(()=> bm.cmd['delete'].execute());
	$('#btn_List').click(()=> bm.cmd['list'].execute());

	$(document).ready(function () {
        bm.init();
        bm.cmd['list'].execute();
  });
  // ##################################################
</script>
</body>
</html>
```
#### 코드 설명
- 'bm' BindModel 개체를 만들 때 서비스 개체를 주입합니다.
- 클래스 문은 내부적으로 서비스 개체(NoticeAdminService)의 공통 요소를 분리하는 데 사용되었습니다.
- Jquery를 사용하여 버튼에 execute() 메서드를 등록했습니다.
- 화면을 로드한 후 명령을 실행하여 bm.cmd['list'].execute()를 실행하여 목록을 가져옵니다.
- 핸들바 템플릿을 사용하여 잠금을 출력했습니다.

### front.html
---
공지사항의 사용자 페이지입니다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <title>BindModel Example</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
</head>
<body>
<div class="container mt-3">
  <h2>Notice Front Page</h2>
  <h5>Key functions: List Lookup</h5>
  <p>Data is transmitted when modified or deleted from the test page, but it is not actually processed.</p>
  <table class="table">
    <thead>
      <tr>
        <th>Title</th>
        <th>Date</th>
      </tr>
    </thead>
    <tbody id="area-tbody">
      <tr>
        <td colspan="2">There is no content.</td>
      </tr>
    </tbody>
    {% raw %}
    <!-- Template -->
    <script id="area-temp" type="text/x-handlebars-template">
      {{#rows}}
      <tr>
        <td><a href="javascript:bm.fn.procRead('{{@index}}');" class='btnNormal'>{{title}}</a></td>
        <td>{{create_dt}}</td>			            	
      </tr>
      {{/rows}} 
    </script>
    {% endraw %}
  </table>
  
  <div id="class-form" class="d-none">
    <form>
      <div class="form-group">
          <label for="title">Title</label>
          <input type="text" class="form-control" id="title">
      </div>
      <div class="form-group">
          <label for="contents">Content</label>
          <textarea class="form-control" id="contents" rows="3"></textarea>
      </div>
    </form>
    <button type="submit" class="btn btn-primary mt-3" id="btn_List">List</button>
  </div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.7.8/handlebars.min.js"></script>
<script src="https://unpkg.com/logic-bind-model/dist/bindmodel.pack.min.js"></script>
<script src="service/base-svc.js"></script>
<script src="service/base-notice-svc.js"></script>
<script src="service/notice-front-svc.js"></script>
<script>

    // ##################################################
	var bm = new _L.BindModel(new NoticeFrontService());

    bm.url = '/notice/data/list.json';  // base url

	$('#btn_List').click(()=> bm.cmd['list'].execute());

	$(document).ready(function () {
        bm.init();
        bm.cmd['list'].execute();
    });
  // ##################################################
</script>
</body>
</html>
```
#### 코드 설명
- 'bm' BindModel 개체를 만들 때 서비스 개체를 주입합니다.
- 클래스 문을 사용하여 서비스 개체(NoticeFrontService)의 공통 요소를 분리했습니다.
- Jquery를 사용하여 버튼에 execute() 메서드를 등록했습니다.
- 화면을 로드한 후 명령을 실행하여 bm.cmd['list'].execute()를 실행하여 목록을 가져옵니다.
- 핸들바 템플릿을 사용하여 잠금을 출력했습니다.

### service/base-notice-svc.js
---
기본 NoticeService 클래스가 알림의 공통 개체를 구성했습니다.

```js
class BaseNoticeService {
    constructor(_SUFF = '') {
        var _this = this;    

        this.items = {
            // misc
            _area_temp: { selector: { key: `#area-temp${_SUFF}`,    type: 'html' } },
            _area_tbody:{ selector: { key: `#area-tbody${_SUFF}`,   type: 'html' } },
            _area_form: { selector: { key: `#class-form${_SUFF}`,   type: 'prop.class' } },
            _index:     0,
            // valid, bind, output
            ntc_idx:        '',
            title:      { 
                selector: { key: `#title${_SUFF}`,        type: 'value' },
                required: true,
            },
            contents:   { selector: { key: `#contents${_SUFF}`,     type: 'value' } },
            top_yn:     { 
                selector: { key: `input[name=top_yn${_SUFF}]`,      type: 'none' },
                setFilter(val) { 
                    $(`input[name=top_yn${_SUFF}]`).prop('checked', val == 'Y' ? true : false);
                },
                getFilter(val) {
                    return $(`input[name=top_yn${_SUFF}]:checked`).val();
                }
            },
            active_cd:  {
                selector: { key: `input[name=active_cd${_SUFF}][type=radio]`,  type: 'none' },
                setFilter(val) { 
                    $(`input[name=active_cd${_SUFF}][value=${val}]`).prop('checked', true);
                },
                getFilter(val) {
                    return $(`input[name=active_cd${_SUFF}]:checked`).val();
                }
            },
            create_dt:  { selector: { key: `#create_dt${_SUFF}`,  type: 'text' } }
        };
        
        this.fn = {
            procRead(index) { 
                _this.bindModel.items._index = index;
                _this.bindModel.command['read'].execute();
            }
        };
    }
}
```
#### 코드 설명
- _SUFF 매개 변수는 ID와 서비스 개체 이름의 중복을 방지하는 데 사용되었습니다.
- var_this는 콜백 함수에서 bindModel 개체에 액세스하도록 정의되었습니다.
- '항목' 영역은 HTML 열로 등록할 수 있는 공통 속성입니다.
    - 선택기 속성은 DOM을 가리키는 속성입니다.
        - 키 속성은 요소를 가리키는 선택기 값입니다.
        - 형식 속성의 값, 없음, 텍스트, html, prop.synonymes 및 속성.synonymes.
    - 세터/게터는 일반적으로 외부에서 값을 가져옵니다.
    - setFiter/getFiter는 여러 DOM 요소에서 값을 얻거나 처리할 때 설정합니다.
    필요한 속성은 유효한 검사에 필요한 값입니다.
- 'fn' 영역은 사용자 기능 영역입니다.

### service/notice-admin-svc.js
---
NoticeAdminService 클래스는 알림의 관리자 서비스 개체입니다.

```js
class NoticeAdminService extends BaseNoticeService {
    constructor() {
        super();

        var _this       = this;
        var _template   = null;     // Handlebars template

        this.command = {
            create:     {
            },
            read:       {
                outputOption: 3,
                cbBegin(cmd) { 
                    cmd.outputOption.index = Number(cmd._model.items._index);
                    cmd._model.columns._area_form.value = '';  // form show
                },
            },
            update:     {
                cbBind(bind, cmd, setup) {
                    console.warn('Caution: Send to the test server, but the data is not reflected.', setup.data);
                },
                cbEnd(status, cmd, res) {
                    if (res) alert('It has been modified.');
                }
            },
            delete:     {
                cbValid(valid, cmd) { 
                    if (confirm('Are you sure you want to delete it?')) return true;
                },
                cbBind(bind, cmd, setup) {
                    console.warn('Caution: Send to the test server, but the data is not reflected.', setup.data);
                },
                cbEnd(status, cmd, res) {
                    if (res) {
                        alert('The post has been deleted.');
                        _this.bindModel.cmd['list'].execute();
                    }
                }
            },
            list:       {
                outputOption: 1,
                cbBegin(cmd) {
                    cmd._model.columns._area_form.value = 'd-none';
                },
                cbOutput(outs, cmd, res) {
                    if (_template === null) {
                        _template = Handlebars.compile( _this.bindModel.columns['_area_temp'].value ); 
                    }
                    _this.bindModel.columns['_area_tbody'].value   = _template(res.data);
                },
            }
        };

        this.mapping = {
            _area_temp:     { list:     'misc' },
            _area_tbody:    { list:     'misc' },
            _area_form:     { list:     'misc' },
            ntc_idx:        { read:     'bind',     update:  'bind',               delete:     'bind' },
            title:          { read:     'output',   update:  ['valid', 'bind'], },
            contents:       { read:     'output',   update:  'bind' },
            top_yn:         { read:     'output',   update:  ['valid', 'bind'], },
            active_cd:      { read:     'output',   update:  ['valid', 'bind'], },
            create_dt:      { read:     'output' },
        };

    }    
}
```
#### 코드 설명
- 명령 영역은 Bind명령어의 속성을 설정합니다.
    - 출력 옵션은 서버로 전송된 데이터를 가져오는 방법입니다.
    - 콜백 함수는 실행()을 실행할 때 단계별로 호출됩니다.
        - **cbBegin**() >> **cbValid**() > **cbBind**() > **cbResult**() > **cbOutput**() > **cbEnd**()
- 매핑 영역은 열에 대한 Bindcommand 개체 매핑 정보입니다.
    - 제목: { 읽기: '출력', 업데이트: ['valid', 'bind', }
        - 제목 열을 만들고 **read** 명령 및 **valid** 명령의 출력에 매핑하여 유효하게 바인딩합니다.

### service/notice-front-svc.js
---
NoticeFrontService 클래스는 알림의 사용자 서비스 개체입니다.

```js
class NoticeFrontService extends BaseNoticeService {
    constructor() {
        super();

        var _this       = this;
        var _template   = null;     // Handlebars template

        this.command = {
            read:       {
                outputOption: 3,
                cbBegin(cmd) { 
                    cmd.outputOption.index = Number(cmd._model.items._index);
                    cmd._model.columns._area_form.value = '';  // form show
                },
            },  
            list:       {
                outputOption: 1,
                cbBegin(cmd) {
                    cmd._model.columns._area_form.value = 'd-none'; // form hidden
                },
                cbOutput(outs, cmd, res) {
                    if (_template === null) {
                        _template = Handlebars.compile( _this.bindModel.columns['_area_temp'].value ); 
                    }
                    _this.bindModel.columns['_area_tbody'].value   = _template(res.data);
                },
            }
        };

        this.mapping = {
            _area_temp:     { list:     'misc' },
            _area_tbody:    { list:     'misc' },
            _area_form:     { list:     'misc' },
            ntc_idx:        { read:     'bind' },
            title:          { read:     'output' },
            contents:       { read:     'output' },
            create_dt:      { read:     'output' },
        };
    }
}
```