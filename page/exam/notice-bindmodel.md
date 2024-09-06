---
title: "notice BindModel 구현"
layout: fullwidth
permalink: /exam/notice-bindmodel/
date: 2011-06-23T1
# toc: true
# toc_sticky: true
sidebar:
  nav: "exam"
---

## 설명
- "공지 사항" 예제로 페이지로 관리자와 사용자 페이지입니다.
- 웹디자이너가 제작한 html, css, js 파일을 수정없이 바로 사용할 수 있습니다.
- 공통 서비스 객체를 분리하여 관리하여 반복되는 코드 작성이 필요 없도록 효율적인 구조를 제공하여, 유지보수와 확장성을 높였습니다.

## 폴더 구조
```js
vue-mix/
├── service/
│   ├── base-notice-svc.js
│   ├── notice-admin-svc.js 
│   └── notice-front-svc.js : ** 사용자 페이지 **
├── front.html              : ** 사용자 페이지 **
└── admin.html
```


### admin.html
---
notice 목록과 폼을 html, css, js 로 구성한 페이지입니다. 
실제 구현시 관리자 페이지에 권한 설정을 추가해야 합니다.

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
- 'bm' BindModel 객체를 생성할 때 서비스 객체를 주입합니다.
- 서비스 객체(NoticeAdminService)의 공통 요소를 분리하기 위해서 내부에서 class 문을 사용하였습니다.
- Jquery 사용해서 버튼에 execute() 메소드를 등록하였습니다.
- 화면 로딩 후 bm.cmd['list'].execute() 를 실행하여 목록을 가져오는 command 를 실행하였습니다.
- 록록을 출력하기 위해서 handlebars 템플릿을 사용하였습니다. 

### front.html
---
공지사항의 사용자 페이지 입니다.

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
- 'bm' BindModel 객체를 생성할 때 서비스 객체를 주입합니다.
- 서비스 객체(NoticeFrontService)의 공통 요소를 분리하기 위해서 내부에서 class 문을 사용하였습니다.
- Jquery 사용해서 버튼에 execute() 메소드를 등록하였습니다.
- 화면 로딩 후 bm.cmd['list'].execute() 를 실행하여 목록을 가져오는 command 를 실행하였습니다.
- 록록을 출력하기 위해서 handlebars 템플릿을 사용하였습니다. 

### service/base-notice-svc.js
---
BaseNoticeService 클래스는 공지사항의 공통 객체를 구성하였습니다.

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
- _SUFF 매개변수는 서비스객체의 id, name 의 중복을 방지에 활용하였습니다.
- var _this 는 콜백함수에서 bindModel 객체에 접근하기 위해서 정의하였습니다.
- 'items' 영역은 HTMLColumn 으로 등록할 공통속성입니다.
    - selector 속성은 DOM 을 가르키는 속성입니다.
        - key 속성은 요소를 가르키는 셀렉터 값입니다.
        - type 속성의 value, none, text, html, prop.속성명, attr.속성명 이 있습니다.
    - setter/getter 는 주로 외부에서 값을 가져옵니다.
    - setFiter/getFiter 는 여러개의 DOM 요소에서 값을 구하거나, 가공할 때 설정합니다.
    required 속성은 valid 검사시 필수값으로 의미합니다.
- 'fn' 영역은 사용자 함수 영역입니다.


### service/notice-admin-svc.js
---
NoticeAdminService 클래스는 공지사항의 관리자 서비스 객체 입니다.

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
- command 영역은 BindCommand 의 속성을 설정합니다.
    - outputOption 은 서버로 전송받은 자료를 가져오는 방식입니다.
    - 콜백함수는 execute() 실행시 단계별로 호출합니다.
        - **cbBegin**() >> **cbValid**() >> **cbBind**() >> **cbResult**() >> **cbOutput**() >> **cbEnd**()
- mapping 영역은 컬럼에 대해 BindCommand 객체 매핑 정보입니다.
    - title:          { read:     'output',   update:  ['valid', 'bind'], }
        - title 컬럼을 생성하여 **read** 커멘드의 output 에, **valid** 커멘드에는 valid, bind 에 매핑딥니다.

### service/notice-front-svc.js
---
NoticeFrontService 클래스는 공지사항의 사용자 서비스 객체 입니다.

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