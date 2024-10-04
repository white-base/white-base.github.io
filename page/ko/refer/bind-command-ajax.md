---
lang: ko
title: "BindCommand 클래스"
layout: single
permalink: /ko/docs/api-bind-command-ajax/
date: 2011-06-23T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"

breadcrumbs: true
---


# 주요 구조

## 속성 관계

BindCommand 객체는 *valid, bind, output* 속성의 MetaView을 포함하고 있습니다.
`output` 속성은 `_outputs`(MetaViewCollection)의 객체의 속성 `output1` 을 참조합니다.
newOutput(name?) 메소드를 통해서 view 추가할 수 있습니다. `_outputs` 에  "output + 순번"
이름으로 컬렉션이 추가됩니다.

Class diagram
![image-center](/assets/images/cmd-rel-diagram-2024-08-16-002011.png){: .align-center}

## 상속 관계

BindCommandAjax 을 상속하여 확장하거나 BindCommand 을 상속하여 재정의하여 사용자화 할 수 있습니다.

Class diagram
![image-center](/assets/images/cmd-diagram-2024-08-16-004352.png){: .align-center}



# 구성 요소

## 속성

| 항목           | 설명                                                                                                                                                         |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| config       | axios config 와 동일                                                                                                                                          |
| url          | url 경로                                                                                                                                                     |
| \_outputs    | `_outputs` MetaView 컬켁션 속성입니다.                                                                                                                             |
| \_model      | `_model` BindModel 객체 입니다.                                                                                                                                 |
| valid        | 검사대상 MetaView 입니다.                                                                                                                                         |
| bind         | 바인드 MetaView 입니다.                                                                                                                                          |
| output       | 기본 출력 MetaView 입니다.<br>\_ouputs[0] 객체를 참조합니다.                                                                                                              |
| outputOption | 출력 옵션입니다. (기본값 = 0)<br>- 0 : view 안함<br>- 1 : 모든 column 및 rows 가져오기<br>- 2 : 존재하는 column 의 rows 가져오기 <br>- 3 : 존재하는 column 의 rows 가져오기,  n번째 자로 value 에 설정 |
| cbBegin      | execute() 시작 콜백 입니다.<br>`callback(bindComamnd)`                                                                                                            |
| cbValid      | execute() valid 검사 전 콜백 입니다.<br>`callback(validView, bindComamnd)`                                                                                         |
| cbBind       | execute() bind  전 콜백 입니다.<br>`callback(validView, bindComamnd, config)`                                                                                    |
| cbResult     | execute() 회신  콜백 입니다.<br>`callback(data, bindComamnd, response)`                                                                                           |
| cbOutput     | execute() output View 매칭 후  콜백 입니다.<br>`callback(views, bindComamnd, response)`                                                                           |
| cbEnd        | execute() 종료 콜백 입니다.<br>`callback(status, bindComamnd, response)`                                                                                          |
| _guid        | 객체의 고유 식별자 (GUID). 객체를 고유하게 식별합니다.                                                                                                                         |
| _type        | 객체의 생성자 함수. 객체가 생성될 때 사용된 함수입니다.                                                                                                                           |

---
## 메소드

| 항목                                          | 설명                                                                                                            |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| execute(): Promise                          | 순서 :  `_execBegin()` >> `_execValid()` >> `_execBind()` >> `_execResult()` >> `_execOutput()` >> `_execEnd()` |
| addColumn(column, views, bTable?)           | 컬럼을 추가하고 지정 테이블에 추가하고, 컬럼의 참조를 BindCommand 의 valid, bind, output MetaView 에 등록합니다.                            |
| addColumnValue(name, value, views, bTable?) | 지정한 이름으로 컬럼과 값을 추가하고, 컬럼의 참조를 BindCommand 의 valid, bind, output MetaView 에 등록합니다.                             |
| setColumn(names, views, bTable?)            | 메타테이블의 컬럼을 지정한 MetaView 에 설정합니다.                                                                              |
| release(names)                              | 지정한 컬럼을 대상 MeteView 에서 제거합니다.                                                                                 |
| newOutput(name?)                            | `_output` MetaViewCollection 에 MetaView 을 추가합니다.<br>* - 기본 이름 = 'output' + _outout.count                      |
| removeOutput(name)                          | `_output` MetaViewCollection 에 MetaView 을 제거합니다.                                                              |
| getObject(): object                         | 현재 객체의 guid 타입의 객체를 가져옵니다.                                                                                    |
| setObject(obj, origin)                      | 현재 객체를 초기화 후, 지정한 guid 타입의 객체를 사용하여 설정합니다.                                                                    |
| equal(target)                               | 현재 객체와 지정된 객체가 동일한지 비교합니다.                                                                                    |
| getTypes()                                  | 현재 객체의 생성자와 프로토타입 체인의 모든 생성자를 배열로 반환합니다.                                                                      |
| instanceOf(target)                          | 현재 객체가 지정된 타입의 인스턴스인지 확인합니다. (_UNION 포함)                                                                      |

---

## 이벤트

| 항목         | 설명                        |
| ---------- | ------------------------- |
| onExecute  | execte() 실행 전 공통 이벤트 입니다. |
| onExecuted | execte() 실행 후 공통 이벤트 입니다. |


# 세부 설명 

## 주요 속성

### config

> AJAX 요청에 대한 설정값입니다.
> axios 의 `config`와 동일한 형식입니다.

```ts
type config = object;
```

### url

> AJAX 요청의 URL을 설정합니다.

```ts
type url = string;
```

### \_outputs

> 출력 결과를 저장하는 컬렉션입니다.

```ts
type _outputs = MetaViewCollection;
```

### \_model

> 바인드 모델 객체입니다.

```ts
type _model = BindModel;
```

### valid

> 검사 대상 MetaView 객체입니다.

```ts
type valid = MetaView;
```

### bind

> 바인드 대상 MetaView 객체입니다.

```ts
type bind = MetaView;
```

bind(MetaView) 는 서버에 전송하는 컬럼 목록입니다.
bind.columns 컬렉션의 컬럼명과 컬럼값은 서버로 전송(요청)합니다.
#### 예제 : 내부 작동 구조
```js
var bm = new BindModelAjax();
bm.url = '/user'
bm.addCommand('test');
bm.cmd['test'].addColumnValue('user_name', '홍길동');
bm.cmd['test'].addColumnValue('passwd', '1234');
bm.cmd['test'].execute();

///// 내부적오로 전송
var axiosConfig = {
	url: '/user',
	data: {
		user_name: '홍길동',
		passwd: '1234'
	}
}
```

### output

> 동적으로 추가된 출력 MetaView 객체입니다.

```ts
type output = MetaView;
```

### outputOption

> 출력 특성 옵션입니다.
> - 0: 제외
> - 1: 모든 컬럼의 로우 가져옴
> - 2: 존재하는 컬럼의 로우만 가져옴
> - 3: 존재하는 커럼의 로우만 가져오고, value 설정

```ts
type outputOption = object;
```

### outOpt

> outputOption 의 별칭입니다.

```ts
type outOpt = object;
```

### cbBegin

> 실행 시작 시 호출되는 콜백 함수입니다.

```ts
type cbBegin = (cmd: BindCommand) => void;
```
- cmd - 현재 바인드 명령 객체입니다.

### cbValid

> 검사(valid) 전 호출되는 콜백 함수입니다.

```ts
type cbValid = (valid: MetaView, command: BindCommand) => boolean;
```
- valid : 검사할 `MetaView` 객체입니다.
- command : 현재 바인드 명령 객체입니다.
- return : 검사 결과를 나타내는 boolean 값입니다.

### cbBind

> 바인드(bind) 전 호출되는 콜백 함수입니다.

```ts
type cbBaseBind = (
	bind: MetaView, 
	command: BindCommand, 
	config: object
) => void;
```
- bind : 바인드할 `MetaView` 객체입니다.
- command : 현재 바인드 명령 객체입니다.
- config : 설정 객체입니다.

### cbResult

> 바인드 결과를 처리하는 콜백 함수입니다.
> 주로 결과 데이터 가공에 사용됩니다.

```ts
type cbResult = (
	data: object, 
	command: BindCommand, 
	response: object
) => object;
```
- data : 바인드 결과 데이터 객체입니다.
- cmd : 현재 바인드 명령 객체입니다.
- response : 응답 객체입니다.
- return : 처리된 결과 데이터입니다.
### cbOutput

> 바인드 결과를 출력하는 콜백 함수입니다.
> 주로 목록의 출력에 사용됩니다.

```ts
type cbOutput = (
	outputs: MetaViewCollection, 
	command: BindCommand, 
	response: object
) => object;
```
- outputs : 메타 뷰 컬렉션입니다.
- command : 현재 바인드 명령 객체입니다.
- response : 응답 객체입니다.
- return : 처리된 결과 객체를 반환합니다.
### cbEnd

> 처리 종료 후 호출되는 콜백 함수입니다.

```ts
type cbBaseEnd = (
	status: object, 
	command: BindCommand, 
	response: object
) => void;
```
- status : 상태 정보를 담은 객체입니다.
- command : 현재 바인드 명령 객체입니다.
- response : 응답 객체입니다.

### \_guid

> 객체의 고유 식별자 (GUID). 객체를 고유하게 식별합니다.

```ts
type _guid = string;
```

### \_type

> 객체의 생성자 함수입니다. 객체가 생성될 때 사용된 함수입니다.

```ts
type _type = Function;
```

---
## 주요 메소드

### execute()

> 바인드 명령을 실행합니다.
> 유효성 검사, 바인딩, 결과 처리, 성공 및 오류 콜백을 포함한 전체 실행 프로세스를 수행합니다.

```ts
type execute = () => Promise<void>;
```
- return : 실행 결과를 나타내는 `Promise` 객체입니다.

#### 예제
```js
var bm = new BindModelAjax();

bm.addCommand('test1');
bm.addCommand('test2');
...

bm.cmd['test1'].execute();
bm.cmd['test2'].execute();
```

### addColumn()

> 컬럼을 추가하고 지정한 뷰와 매핑합니다.

```ts
type addColumn = (
	column: string | MetaColumn, 
	views: string | string[], 
	bTable: string | MetaTable
) => void;
```
- column : 등록할 컬럼 객체입니다. 문자열 또는 `MetaColumn` 객체일 수 있습니다.
- views : 추가할 뷰 엔티티 이름입니다. 문자열 또는 문자열 배열일 수 있습니다.
- bTable : (선택적) 매핑할 기본 테이블 객체 또는 테이블 이름입니다.
### addColumnValue()

> 컬럼과 값을 추가하고 지정한 뷰와 매핑합니다.

```ts
type addColumnValue = (
	name: string, 
	value: any, 
	views?: string | string[], 
	bTable?: string | MetaTable
) => void;
```
- name : 컬럼 이름입니다.
- value : 컬럼 값입니다.
- views : (선택적) 추가할 뷰 엔티티 이름입니다.
- bTable : (선택적) 매핑할 기본 테이블 객체 또는 테이블 이름입니다.

### setColumn()

> 컬럼을 설정합니다.

```ts
type setColumn = (name: string | string[], views: string | string[]) => void;
```
- name : 컬럼 이름 또는 이름 배열입니다.
- views : 설정할 뷰 이름 또는 이름 배열입니다.

### release()

> 대상 엔티티에서 컬럼을 해제합니다.

```ts
type release = (name: string | string[], views: string | string[]) => void;
```
- name : 해제할 컬럼 이름 또는 이름 배열입니다.
- views : 해제할 뷰 엔티티 이름 또는 이름 배열입니다.

### newOutput()

> 출력에 사용할 뷰 엔티티를 추가합니다.
> 기본 이름은 'output' + _outputs.count입니다.

```ts
type newOutput = (name?: string) => void;
```
- name : (선택적) 추가로 참조할 뷰 이름입니다.

### removeOutput()

> 출력 뷰를 삭제합니다.

```ts
type removeOutput = (name: string) => boolean;
```
- name - 삭제할 뷰 이름입니다.

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

#### 예제
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
type equal = (target: object) => boolean;
```
- return : 두 객체가 동일한지 여부를 반환합니다.

### getTypes()

> 현재 객체의 생성자와 프로토타입 체인의 모든 생성자를 배열로 반환합니다.

```ts
type getTypes = () => Array<Function>;
```
- return : 생성자 함수의 배열을 반환합니다.

#### 예제
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

### onExecute

> 명령 실행 전 호출되는 이벤트입니다.

```ts
type onExecute = (cmd: BindCommand) => void;
```
- cmd : 실행할 명령 객체입니다.

### onExecuted

> 명령 실행 후 호출되는 이벤트입니다.

```ts
type onExecuted = (cmd: BindCommand, result: object) => void;
```
- cmd : 실행한 명령 객체입니다.
- result : 명령 실행 결과 객체입니다.


