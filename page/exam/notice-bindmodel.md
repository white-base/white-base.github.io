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

## 폴더 구조
```js
vue-mix/
├── service/
│   ├── notice-admin-svc.js
│   └── base-notice-svc.js
└── admin.html
```

### admin.html
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


### service/base-notice-svc.js

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

### service/notice-admin-svc.js
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
