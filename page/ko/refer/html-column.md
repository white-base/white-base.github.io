---
lang: ko
title: "HTMLColumn 클래스"
layout: single
permalink: /ko/docs/api-html-column/
date: 2011-06-23T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"

breadcrumbs: true
---
HTMLColumn 은 바인딩의 핵심입니다
특별한 지시자를에 하용하지 않고 내부구현 방식을 직접 제어하면, 익숙해지면 다양한 조건에 응용할 수 있습니다.
개발시 디버깅과 응용에 유리하며, 개발자도구 만으로 충분합니다.

클래식한 코딩 방식을 유지하면서 코드의 중복을 최소화 하고 재활용에 중점을 두었습니다.

# 주요 구조

## 상속 관계

Class diagram
![image-center](/assets/images/col-diagram-2024-08-16-002347.png){: .align-center}

# 주요 요소

## 속성

| 항목          | 설명                                                                  |
| ----------- | ------------------------------------------------------------------- |
| domType     | 아이템 DOM 타입을 정의합니다.                                                  |
| isReadOnly  | 읽기 전용 여부를 나타냅니다.                                                    |
| isHide      | 숨김 여부를 나타냅니다.                                                       |
| element     | DOM 요소를 나타냅니다.                                                      |
| selector    | 셀렉터를 정의합니다.                                                         |
| getFilter   | value 값을 필터링하는 함수입니다.                                               |
| setFilter   | value 값을 필터링하는 함수입니다.                                               |
| value       | 아이템의 값을 설정하거나 가져옵니다.                                                |
| required    | 컬럼 값의 필수 여부를 설정합니다. 값이 반드시 존재해야 하는 경우 `true`, 그렇지 않은 경우 `false`입니다. |
| constraints | 컬럼의 제약 조건을 설정합니다. 제약 조건은 객체 또는 함수 형태로 설정할 수 있습니다.                   |
| getter      | 컬럼 값의 getter 함수입니다.                                                 |
| setter      | 컬럼 값의 setter 함수입니다.                                                 |
| columnName  | 컬럼의 이름을 나타냅니다. `_name`과 동일합니다.                                      |
| alias       | 컬럼의 별칭을 설정하거나 가져옵니다. 별칭은 데이터 전송 및 로우값 설정 시 사용됩니다.                   |
| default     | 컬럼의 기본값을 설정합니다.                                                     |
| caption     | 컬럼에 대한 설명을 제공합니다.                                                   |
| _valueTypes | 컬럼의 값 타입을 정의합니다. 이 속성은 컬럼 값의 타입을 설정하는 데 사용됩니다.                      |
| _entity     | 이 컬럼이 속한 엔티티를 나타냅니다. `BaseEntity` 타입의 객체입니다                         |
| _guid       | 객체의 고유 식별자 (GUID). 객체를 고유하게 식별합니다.                                  |
| _type       | 객체의 생성자 함수. 객체가 생성될 때 사용된 함수입니다.                                    |
|             |                                                                     |


---

## 메소드

| 항목                                         | 설명                                                              |
| ------------------------------------------ | --------------------------------------------------------------- |
| clone(entity)                              | 현재 아이템의 DOM을 복제합니다.                                             |
| addConstraint(regex, msg, code, condition) | 제약 조건을 추가합니다.                                                   |
| valid(value)                               | 속성의 값이 유효한지 검사합니다. `required` 및 `constraints`를 기준으로 유효성을 검사합니다. |
| getObject(vOpt, owned)                     | 현재 객체를 직렬화된 객체로 얻습니다. 순환 참조는 `$ref` 값으로 대체됩니다.                  |
| setObject(oGuid, origin)                   | 직렬화된 객체를 현재 객체에 설정합니다.  <br>객체는 초기화됩니다.                         |
| equal(target)                              | 현재 객체와 지정된 객체가 동일한지 비교합니다.                                      |
| getTypes()                                 | 현재 객체의 생성자와 프로토타입 체인의 모든 생성자를 배열로 반환합니다.                        |
| instanceOf(target)                         | 현재 객체가 지정된 타입의 인스턴스인지 확인합니다. (_UNION 포함)                        |
|                                            |                                                                 |


---
## 이벤트

| 항목        | 위치         | 설명                       |
| --------- | ---------- | ------------------------ |
| onChanged | MetaColumn | 컬럼 값이 변경될 때 발생하는 이벤트입니다. |
|           |            |                          |





# 세부 설명

## 주요 속성

### domType

> 아이템 DOM 타입을 정의합니다.

```ts
type domType = object;
```

### isReadOnly

> 읽기 전용 여부를 나타냅니다.

```ts
type isReadOnly = boolean;
```

### isHide

> 숨김 여부를 나타냅니다.

```ts
type isHide = boolean;
```

### element

> DOM 요소를 나타냅니다.

```ts
type element = HTMLElement;
```

### selector

> 셀렉터를 정의합니다.
> type
> - `val` 또는 `value`: 요소의 value 속성값
> - `text`: 요소의 텍스트값
> - `html`: 요소의 HTML 값
> - `css.속성명`: CSS의 속성값 (객체)
> - `prop.속성명`: 요소의 속성명값 (초기 상태 기준)
> - `attr.속성명`: 요소의 속성명값 (현재 상태)
> - `none`: 아무 작업도 수행하지 않음, 표현의 목적
> 예시: 'value', 'text', 'css.color', 'prop.disabled'

```ts
type selector = { key: string, type: string };
```

### getFilter

> value 값을 필터링하는 함수입니다.

```ts
type getFilter = (sVal: any) => any;
```
- sVal : selector 가 존재시 selector 에서 얻는 값입니다.
- return : 필터링된 value 값입니다.

### setFilter

> value 값을 필터링하는 함수입니다.

```ts
type setFilter = (val: any) => any | undefined;
```
- val : 필터로 적용할 값입니다.
- return : 필터링 결과값이 있으면, selector 의 값을 설정합니다.
	- undefined 리턴시   selector 값을 설정하지 않습니다. 
### value

> 아이템의 값을 설정하거나 가져옵니다.

```ts
type value = string | number | boolean;
```

### required

> 컬럼 값의 필수 여부를 설정합니다.
> 값이 반드시 존재해야 하는 경우 `true`, 그렇지 않은 경우 `false`입니다.

```ts
type required = boolean;
```

### constraints

> 컬럼의 제약 조건을 설정합니다.
> 제약 조건은 객체 또는 함수 형태로 설정할 수 있습니다.

```ts
type constraints = (object | Function)[];
```

### getter

> 컬럼 값의 getter 함수입니다.

```ts
type getter = () => string | number | boolean;
```
- return : 컬럼의 현재 값입니다.

### setter

> 컬럼 값의 setter 함수입니다.

```ts
type setter = (value: string | number | boolean) => void;
```
- value : 설정할 값입니다.

### columnName

> 컬럼의 이름을 나타냅니다. `_name`과 동일합니다.

```ts
type columnName = string;
```

### alias

> 컬럼의 별칭을 설정하거나 가져옵니다. 별칭은 데이터 전송 및 로우값 설정 시 사용됩니다.
> 사용처 (기본값 = columnName )\
> - Bind-command-ajax.\_execBind() : 데이터 전송시
> - BaseBind.setValue(row) : 로우값 을 엔티티에 설정시
> - getValue() : row 에 활용함


```ts
type alias = string;
```

### default

> 컬럼의 기본값을 설정합니다.

```ts
type default = string | number | boolean;
```

### caption

> 컬럼에 대한 설명을 제공합니다.

```ts
type caption = string;
```

### \_valueTypes

> 컬럼의 값 타입을 정의합니다. 이 속성은 컬럼 값의 타입을 설정하는 데 사용됩니다.

```ts
type _valueTypes = any;
```

### \_entity

> 이 컬럼이 속한 엔티티를 나타냅니다. `BaseEntity` 타입의 객체입니다.

```ts
type _entity = BaseEntity;
```

### \_guid

> 객체의 고유 식별자 (GUID). 객체를 고유하게 식별합니다.

```ts
type _guid: string;
```

### \_type

> 객체의 생성자 함수입니다. 객체가 생성될 때 사용된 함수입니다.

```ts
type _type = Function;
```

---
## 주요 메소드

### clone()

> 현재 컬럼을 복제합니다.

```ts
type clone = (entity: BaseEntity) => this;
```
- entity : 복제할 대상의 엔티티입니다.
- return : 현재 인스턴스의 복제본입니다.

### addConstraint()

> 제약 조건을 추가합니다.

```ts
type addConstraint = (
	regex: RegExp, 
	msg: string, 
	code?: string, 
	condition?: boolean
) => void;
```
- regex : 적용할 정규 표현식입니다.
- msg : 정규 표현식 실패 시 표시할 메시지입니다.
- code : 정규 표현식 실패 시 코드입니다. (옵션)
- condition : 제약 조건의 성공/실패 여부를 결정하는 조건입니다. 기본값은 `false`입니다.
### valid()

> 속성의 값이 유효한지 검사합니다.
> `required` 및 `constraints`를 기준으로 유효성을 검사합니다.

```ts
type valid = (value: ValueType): object | undefined;
```
- value : 검사할 값입니다.
- return : 유효하지 않은 경우 객체를 반환하며, 유효한 경우 `undefined`를 반환합니다.

### getObject()

> 현재 객체를 직렬화(guid 타입) 객체로 얻습니다.
> (순환참조는 $ref 값으로 대체됩니다.)

```ts
type getObject = (vOpt?: number, owned?: object | Array<object>) => object;
```
- vOpt :  가져오기 옵션입니다. 기본값은 0 입니다.
	- opt=0 : 참조 구조(_guid:Yes, $ref:Yes)
	* opt=1 : 중복 구조(_guid:Yes, $ref:Yes)
	* opt=2 : 비참조 구조(_guid:No, $ref:No)
- owned : 현재 객체를 소유하는 상위 객체들입니다. 기본값은 빈객체 입니다.
- return : 직렬화된 객체를 반환합니다.

```js
a.getObject(2) == b.getObject(2)
```

### setObject()

> 직렬화(guid 타입) 객체를 현재 객체에 설정합니다.
> (객체는 초기화 됩니다.)

```ts
type setObject = (oGuid: object, origin?: object) => void;
```
- oGuid : 직렬화할 guid 타입의 객체입니다.
- origin : 현재 객체를 설정하는 원본 객체입니다. 기본값은 oGuid 입니다.

### equal()

> 현재 객체와 지정된 객체가 동일한지 비교합니다.

```ts
type equal (target: object) => boolean;
```
- return : 두 객체가 동일한지 여부를 반환합니다.

### getTypes()

> 현재 객체의 생성자와 프로토타입 체인의 모든 생성자를 배열로 반환합니다.

```ts
type getTypes = () => Array<Function>;
```
- return : 생성자 함수의 배열을 반환합니다.

```js
const types = obj.getTypes();
console.log(types); // [Function: MetaObject]
```

### instanceOf()

> 현재 객체가 지정된 타입의 인스턴스인지 확인합니다. (_UNION 포함)

```ts
type instanceOf = (target: object | string) => boolean;
```
- target : 확인할 대상 타입 (객체 또는 문자열)입니다.
- return : 지정된 타입의 인스턴스인지 여부를 반환합니다.

---
## 주요 이벤트


> 컬럼 값이 변경될 때 발생하는 이벤트입니다.

```ts
type onChanged = (newVal: ValueType, oldVal: ValueType, _this: this) => void;
```
- newVal : 새 값입니다.
- oldVal : 이전 값입니다.
- \_this : 이벤트를 발생시킨 객체입니다.

#### 예제
```js
column.onChanged = function(newVal, oldVal, _this) {
	console.log('Value changed');
};
```



## column 의 value 얻기

`우선순위` : 1. getter > 2. getFilter > 3. selector > 4. inner value > 5.default
column value 을 얻을때 속성(getter, getFilter, selector) 설정의 우선순위에 따라 선택된 값을 회신합니다.

### getter 내부 구조

![image-center](/assets/images/col-get-diagram-2024-08-16-010300.png){: .align-center}
1. getter 가 있는 경우, getter 의 return 값을 회신합니다.
2. getFilter 가 있는 경우, getFilter 의 return 값을 회신합니다.
   selector 가  동시에 있으면 selector 값은 getFilter(selectorValue) 의 인자로 전달합니다.
3. selector 가 있는 경우, type 에 따라 값을 회신합니다.
   type : `value`, `text`, `html`, `prop`, `attr`, `css` (jquey사용)
   (type = 'none' 제외)
4. 내부값을 회신합니다.
5. 내부값이 empty(undefined, null)일 경우, default 값을 회신합니다.
   default 의 기본값은 ''(빈문자) 입니다.

## column 의 value 설정

`설정 순서` : 1. setter > 2. inner value > 3. setFilter > 4. selector
column value 을 설정 할 때 순차적으로 설정합니다.

### setter 내부 구조

![image-center](/assets/images/col-set-diagram-2024-08-16-010359.png){: .align-center}
1. setter 가 있는 경우, setter 함수를 호출합니다.
2. setter 의 return 값을 inner value 에 저장합니다.
   return 이 없을 경우, 입력값을 저장합니다.
3. setFilter 가 있는 경우, setFilter 함수를 호출합니다.
4. selector 이 있는 경우, type 에 따라 값을 설정합니다. (jquery 사용)
   type : `value`, `text`, `html`, `prop`, `attr`, `css` (jquey사용)
   (type = 'none'는 제외)

### 사용 빈도 

HTMLComun 설계시 사용빈도를 분석하여
- 중복코드의 최소화
- 다양한 사례에 적용이 가능하고
- 직관적이 코드 구조를
고려하여 설계 하셨습니다.

또한 `selector` 는 checkSelector() 메소드로 html 페이지에 대한 요소의 존재여부 확인에 사용됩니다.

| 빈도  | setter | setFilter | selector | getFilter | getter | 설명                                                                     |
| --- | ------ | --------- | -------- | --------- | ------ | ---------------------------------------------------------------------- |
| 89% |        |           | `사용`     |           |        | - DOM 을 직접 가르키는 경우  <br>- 단독 요소의 경우  <br>- 예시> 태그 p, text, select, css |
| 3%  | `사용`   |           |          |           | `사용`   | - 외부 객체의 가르키는 경우  <br>- 예시> page_count                                 |
| 3%  | `사용`   |           | `사용`     |           | `사용`   | - 외부 객체와 DOM 을 같이 사용 하는 경우  <br>- 예시> page_size = 페이지표시줄수(select box)  |
| 3%  |        | `사용`      | `'none'` | `사용`      |        | - 복합 DOM 객체를 가리키는 경우  <br>- selector 은 검사용으로만 사용  <br>- 예시> radio      |
| 1%  |        | `사용`      | `사용`     |           |        | - 단방향으로 사용한 경우  <br>- 예시> 금액(숫자+콤마) 표시                                 |
| 1%  |        |           | `'none'` | `사용`      |        | - 단방향으로 사용할 경우  <br>- 예시> 코드값 변환, 목록 선택 값                              |
|     |        |           |          |           |        |                                                                        |

<mark style="background: #FFF3A3A6;">(쇼핑몰 웹사이트,  DB Table 36EA 기준)</mark>

### 예제 코드
#### a. selector 을 사용하는 경우

HTML 요소의 속성값을 value 로 선택할 사용합니다.
selector 의 `type` 은 html, text, value, prop.\*, attr.\*, css.\* 이 있습니다.
또한 selector 은 value 조회/설정 이외에 유효성 검사에 사용할 수 있습니다.
BindModelAjax 객체의 checkSelector() 메소드를 통해서 DOM 에 유효성 검사 여부에 활용합니다.

```html
<div id="p-nm"><p style="color:red;">10</p></div>
<input id="p-title" type="text" value="AA" >
<input id="p-check" type="checkbox" checked />
<a href="#">google.com</a>
```

```js
var c1 = new HTMLColumn('c1');
var c2 = new HTMLColumn('c2');
var c3 = new HTMLColumn('c3');
var c4 = new HTMLColumn('c4');
var c5 = new HTMLColumn('c5');
var c6 = new HTMLColumn('c6');

c1.selector = { key: '#area-nm',  type: 'html' };
c2.selector = { key: '#area-nm',  type: 'text' };
c3.selector = { key: '#p-title',  type: 'value' };
c4.selector = { key: 'p-check',   type: 'prop.checked' };
c5.selector = { key: 'a',         type: 'attr.href' };
c6.selector = { key: 'p',         type: 'css.color' };

c1.value; // <p>10</p>
c2.value; // 10
c3.value; // "AA"
c4.value; // true
c5.value; // "google.com"
c6.value; // "red"
```

#### b. setter, getter 을 사용한 경우

외부값을 HTMLColumn 의 value 로 사용할 경우 사용합니다. 
단방향 외부값을 읽기전용으로 사용할려면 getter 함수만 지정합니다.

```js
var PAGE_SIZE = 10;
varc1 = new HTML Column ('c1'); // see external value

c1.getter = function(){ return PAGE_SIZE };
c1.setter = function(){ PAGE_SIZE = newVal };

c1.value; // 10
```
#### c. selector 와 setter/getter 를 같이 사용한 경우

외부값을 HTMLColumn 의 value 로 사용하면서, html 로 표현을 해야할 때 지정합니다.
원본데이터를 어디에 위치할지에 따라서 다양하게 구성할 수 있습니다.

```html
<select id="page-size">
    <option value="10">10EA</option>
    <option value="20">20EA</option>
    <option value="30">30EA</option>
</select>
```

```js
var PAGE_SIZE = 10;
var c1 = new HTMLColumn('c1');

c1.selector = { key: '#page-size',  type: 'value' };
c1.getter = function(){ return PAGE_SIZE };
c1.setter = function(newVal){ PAGE_SIZE = newVal };

c1.value; // 10
$("#page-size") .val("20"); // select change value
c2.value; // 20
PAGE_SIZE // 20
```

#### d. setFilter/getFilter 을 사용하는 경우

setter/getter와의 차이점은 내부에 사용되는값과 html 표현이 다른 경우에 사용합니다.
주로 여러개의 html 요소를 제어할 때 사용합니다.

```html
<input type="radio" name="gender" value="female" />
<input type="radio" name="gender" value="male" />
```

```js
var c1 = new HTMLColumn('c1');

c1.selector = { 
	key: 'input[name=gender][type=radio]',  type: 'value' 
};
c1.getFilter = function(){
	 return $('input[name=gender]:checked').val();
};
c1.setFilter = function(newVal){
	$('input[name=gender][value='+ newVal + ']').prop('checked', true);
};

c1.value; // ''
$("#gender").val("female"); // radio 값 변경
c2.value; // 'female'
```
## 서비스 객체를 설정

```js
var bm = new BindModel({
	// selector 만 사용하는 경우
	area_page:     { selector: { key: '#area-page',   type: 'html' } },
	txt_sumCnt:    { selector: { key: '#sumCnt',      type: 'text' } },
	
	// setter/getter 를 사용한 경우
	page_count: { /* See page object outside */
		getter: ()=> page.page_count,
	    setter: (val)=> page.page_count = val;
	},
	
	// selector & setter/getter 를 사용한 경우
	page_size: {
	  selector: { key: 'select[name=m-page_size]',     type: 'value' },
	  getter: ()=>{ return page.page_size; },
	  setter: (val)=>{ page.page_size = val; }
	},
	
	// selecter['none'] & setFilter/getFilter 를 사용한 경우
	active_yn: { 
		selector: { key: 'input[name=m-active_yn][type=radio]',  type: 'none' },
	    setFilter: (val)=>{ 
		    $('input[name=active_yn][value='+ val + ']').prop('checked', true);
		},
	    getFilter: (val)=>{ 
		    return $('input[name=active_yn]:checked').val();
		}
	  },
	
	// selector & setFilter 를 사용한 경우 (콤마 적용시), 단방향
	point: {
    selector: { key: '#point_view',         type: 'text' },
    setFilter: function(val) { return numberWithCommas(val); }
    default: 0, // Set initial value
	},
	
	// selector & getFilter 를 사용한 경우 (코드값 변환시), 단방향
	point: {
		selector: { key: '#codeValue',           type: 'value' },
	    getFilter: function(val) { return val == '' ? 'CODE1' : 'CODE2'; }
	},
});
```