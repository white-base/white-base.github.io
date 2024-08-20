---
title: "BindModel 클래스"
layout: single
permalink: /docs/api-bind-model-ajax/
date: 2024-08-14T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"
---

# 주요 구조
## 속성 관계

BindModel 객체를 생성하고 *addCommmand()* 메소드로 BindCommand 객체를 생성합니다.
생성된 `command['별칭']` 에는 *valid, bind, output* 속성값의  MetaView 가 있습니다.
ajax 통신은 내부적으로 `axios` 모듈을 이용하고 있습니다.

클래스 다이어그램

![image-center](/assets/images/bind-rel-diagram-2024-08-16-002203.png){: .align-center}

## 상속 관계

BindModel 을 상속하여 확장하거나 BindModel 을 상속하여 재정의하여 사용자화 할 수 있습니다.

클래스 다이어그램

![image-center](/assets/images/bind-diagram-2024-08-16-002302.png){: .align-center}

# 구성요소

## 속성

| 항목                                                    | 설명                                                       |
| ----------------------------------------------------- | -------------------------------------------------------- |
| baseConfig                                            | 바인딩 기본 config을 설정합니다.                                    |
| url                                                   | 바인딩 기본 config.url을 설정합니다.                                |
| \_tables                                              | 메타 테이블 컬렉션입니다. 여러 메타 테이블을 관리합니다.                         |
| [[52. BindModel 클래스-B#_columnType\| _columnType]] | 아이템 타입을 설정합니다.                                           |
| items                                                 | 아이템 컬렉션입니다.                                              |
| fn                                                    | 바인드모델 함수 컬렉션입니다. (내부함수 + 노출함수)                           |
| command                                               | 바인딩 명령 컬렉션입니다.                                           |
| columns                                               | 컬럼 컬렉션입니다. _baseTable의 컬럼을 나타냅니다.                        |
| first                                                 | 동적으로 생성된 첫 번째 메타 테이블입니다.                                 |
| cbFail                                                | 검사(valid)에서 실패 시 호출되는 콜백 함수입니다.                          |
| cbError                                               | 오류 발생 시 호출되는 콜백 함수입니다.                                   |
| cbBaseBegin                                           | 시작 전 기본 콜백 함수입니다. <br>(cbBegin 콜백 함수가 없을 경우 사용됨)         |
| cbBaseValid                                           | 검사(valid) 시 기본 콜백 함수입니다. <br>(cbValid 콜백 함수가 없을 경우 사용됨)  |
| cbBaseBind                                            | 바인드 시 기본 콜백 함수입니다. <br>(cbBind 콜백 함수가 없을 경우 사용됨)         |
| cbBaseResult                                          | 바인드 결과 수신 시 기본 콜백 함수입니다. <br>(cbResult 콜백 함수가 없을 경우 사용됨) |
| cbBaseOutput                                          | 출력 기본 콜백 함수입니다.<br>(cbOutput 콜백 함수가 없을 경우 사용됨)           |
| cbBaseEnd                                             | 실행 완료 시 기본 콜백 함수입니다. <br>(cbEnd 콜백 함수가 없을 경우 사용됨)        |
| preRegister                                           | init() 호출시 처음에 호출되는 콜백 함수입니다.                            |
| preCheck                                              | init() 호출시 boolean 을 리턴하는 콜백 함수입니다.                      |
| preReady                                              | init() 호출시 preCheck 콜백 함수 결과가 true 일때 호출되는 콜백 함수입니다.     |
| _baseTable                                            | 기본 엔티티를 정의합니다.                                           |
| _guid                                                 | 객체의 고유 식별자 (GUID). 객체를 고유하게 식별합니다.                       |
| _type                                                 | 객체의 생성자 함수. 객체가 생성될 때 사용된 함수입니다.                         |



## 메소드

| 항목                                                  | 설명                                                                                   |
| --------------------------------------------------- | ------------------------------------------------------------------------------------ |
| checkSelector(collection?)                          | 셀렉터를 검사합니다.                                                                          |
| getSelector()                                       | 대상 셀렐터 목록을 얻습니다.                                                                     |
| addCommand(name, opt?, baseTable?)                  | 명령을 추가합니다.                                                                           |
| setService(svc, chkType)                            | 서비스를 설정합니다.                                                                          |
| init()                                              | 초기화 작업을 수행합니다. 내부적으로 `preRegister()` -> `preCheck()` -> `preReady()` 순서로 호출합니다.      |
| addTable(name)                                      | 테이블을 등록합니다.                                                                          |
| addColumn(column, cmds?, views?, bTable?)           | 컬럼을 추가하고 명령과 매핑합니다.                                                                  |
| addColumnValue(name, value, cmds?, views?, bTable?) | 컬럼과 값을 추가하고 지정된 테이블에 추가하며, 컬럼의 참조를 BindCommand의 valid, bind, output MetaView에 등록합니다. |
| setMapping(mapping: collection \| object, bTable?)  | 컬럼을 매핑합니다.                                                                           |
| getObject(): object                                 | 현재 객체를 직렬화(guid 타입) 객체로 얻습니다. (순환참조는 $ref 값으로 대체됩니다.)                                |
| setObject(obj, origin)                              | 직렬화(guid 타입) 객체를 현재 객체에 설정합니다. (객체는 초기화 됩니다.)                                        |
| equal(target)                                       | 현재 객체와 지정된 객체가 동일한지 비교합니다.                                                           |
| getTypes()                                          | 현재 객체의 생성자와 프로토타입 체인의 모든 생성자를 배열로 반환합니다.                                             |
| instanceOf(target)                                  | 현재 객체가 지정된 타입의 인스턴스인지 확인합니다. (_UNION 포함)                                             |





## 이벤트
| 항목         | 설명                     |
| ---------- | ---------------------- |
| onExecute  | execte() 실행 전 이벤트 입니다. |
| onExecuted | execte() 실행 후 이벤트 입니다. |


# 세부 설명

## 주요 속성

### baseConfig

> 바인딩 기본 config을 설정합니다.

```ts
type baseConfig = object;
```


### url

> 바인딩 기본 config.url을 설정합니다.

```ts
type url = string;
```

### \_tables

> 메타 테이블 컬렉션입니다.
> 여러 메타 테이블을 관리합니다.

```ts
type _tables = MetaTableCollection;
```

### \_columnType

> 컬럼 타입을 설정합니다.

```ts
type _columnType = MetaColumn;
```

### items

> 아이템 컬렉션입니다.

```ts
type items = PropertyCollection;
```

### fn

> 바인드모델 함수 컬렉션입니다. (내부함수 + 노출함수)

```ts
type fn = PropertyCollection;
```

### command

> 바인딩 명령 컬렉션입니다.

```ts
type command = PropertyCollection;
```

### cmd

> command 의 별칭입니다.

```ts
type cmd = PropertyCollection;
```

### columns

> 컬럼 컬렉션입니다.
> \_baseTable의 컬럼을 나타냅니다.

```ts
type columns = MetaTableColumnCollection;
```

### first

> 동적으로 생성된 첫 번째 메타 테이블입니다.
> \_tables[0] 의 참조값입니다.

```ts
type first = MetaTable;
```

### cbFail

> 검사(valid)에서 실패 시 호출되는 콜백 함수입니다.

```ts
type cbFail = (result: object, column: MetaColumn) => void;
```
- result : 검사 결과를 담은 객체입니다.
- column : 검사에 사용된 `MetaColumn` 객체입니다.
### cbError

> 오류 발생 시 호출되는 콜백 함수입니다.

```ts
type cbError = (msg: string, status: object, response: object) => void;
```
- msg : 오류 메시지입니다.
- status : 상태 정보를 담은 객체입니다.
- response : 응답 객체입니다.
### cbBaseBegin

> 시작 전 기본 콜백 함수입니다. (cbBegin 콜백 함수가 없을 경우 사용됨)

```ts
type cbBaseBegin = (command: BindCommand) => void;
```
- command : 현재 바인드 명령 객체입니다.
### cbBaseValid

> 검사(valid) 시 기본 콜백 함수입니다. (cbValid 콜백 함수가 없을 경우 사용됨)

```ts
type cbBaseValid = (valid: MetaView, command: BindCommand) => boolean;
```
- valid : 검사할 `MetaView` 객체입니다.
- command : 현재 바인드 명령 객체입니다.
- return : 검사 결과를 나타내는 boolean 값입니다.

### cbBaseBind

> 바인드 시 기본 콜백 함수입니다. (cbBind 콜백 함수가 없을 경우 사용됨)

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

### cbBaseResult

> 바인드 결과 수신 시 기본 콜백 함수입니다. (cbResult 콜백 함수가 없을 경우 사용됨)

```ts
type cbBaseResult = (
	data: object, 
	command: BindCommand, 
	response: object
) => object;
```
- data : 바인드 결과 데이터 객체입니다.
- command : 현재 바인드 명령 객체입니다.
- response : 응답 객체입니다.
- return : 처리된 결과 객체를 반환합니다.

### cbBaseOutput

> 출력 기본 콜백 함수입니다. (cbOutput 콜백 함수가 없을 경우 사용됨)

```ts
type cbBaseOutput = (
	outputs: MetaViewCollection, 
	command: BindCommand, 
	response: object
) => object;
```
- outputs : 메타 뷰 컬렉션입니다.
- command : 현재 바인드 명령 객체입니다.
- response : 응답 객체입니다.
- return : 처리된 결과 객체를 반환합니다.

### cbBaseEnd

> 실행 완료 시 기본 콜백 함수입니다. (cbEnd 콜백 함수가 없을 경우 사용됨)

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

### preRegister

> init() 호출시 처음에 호출되는 콜백 함수입니다.

```ts
type preRegister = (model: BindModel) => void;
```
- model : 현재 바인드 모델 객체입니다.

### preCheck

> init() 호출시 boolean 을 리턴하는 콜백 함수입니다.

```ts
type (model: BindModel)=>boolean;
```
- model : 현재 바인드 모델 객체입니다.
- return : 검사 결과를 나타내는 boolean 값입니다.

### preReady

> init() 호출시 preCheck 콜백 함수 결과가 true 일때 호출되는 콜백 함수입니다.

```ts
type preReady = (model: BindModel) => void;
```
- model : 현재 바인드 모델 객체입니다.

### \_baseTable

> 기본 엔티티를 정의합니다.

```ts
type _baseTable = MetaTable;
```

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



## 주요 메소드


### checkSelector()

> 셀렉터를 검사합니다.

```ts
type checkSelector = (collection: BaseColumnCollection) => boolean;
```
- collection : 검사할 컬럼 컬렉션입니다.
- return : 검사 결과를 나타내는 boolean 값입니다.

### getSelector()

> 대상 셀렐터 목록을 얻습니다.

```ts
type getSelector = (collection: PropertyCollection) => object[];
```
- collection : 검사할 속성 컬렉션입니다. 기본값은 items 입니다.
- return : 셀렉터 목록을 나타내는 객체 배열입니다.

### addCommand()

> 명령을 추가합니다.

```ts
type addCommand = (
	name: string, 
	option: number, 
	baseTable: MetaTable
) => BindCommand;
```
- name : 명령 이름입니다.
- option : 출력옵션입니다.
- baseTable : (선택적) 기본 테이블 객체입니다.
- return : 추가된 바인드 명령 객체입니다.

### setService()

> 서비스를 설정합니다.

```ts
type setService = (service: IServiceAjax, passTypeChk: boolean) => void;
```
- service : 서비스 객체입니다.
- passTypeChk : 서비스객체 type 검사 통과 유무입니다. (기본값: false)

### init()

> 초기화 작업을 수행합니다.
> 내부적으로 `preRegister()` -> `preCheck()` -> `preReady()` 순서로 호출합니다.

```ts
type init = () => void;
```

### addTable()

> 테이블을 등록합니다.

```ts
type addTable = (name: string) => MetaTable;
```
- name : 등록할 테이블의 이름입니다.
- return : 등록된 메타 테이블 객체를 반환합니다.

### addColumn()

> 컬럼을 추가하고 명령과 매핑합니다.

```ts
type addColumn = (
	column: MetaColumn, 
	cmds?: string | string[], 
	views?: string | string[], 
	bTable?: string | MetaTable
) => void;
```
- column: 등록할 컬럼 객체입니다. 문자열 또는 `MetaColumn` 객체일 수 있습니다.
- cmds : (선택적) 뷰의 위치를 지정하는 명령입니다. 문자열 또는 문자열 배열일 수 있습니다.
- views : (선택적) 추가할 뷰 엔티티 이름입니다. 문자열 또는 문자열 배열일 수 있습니다.
- bTable : (선택적) 매핑할 기본 테이블 객체 또는 테이블 이름입니다.

### addColumnValue()

> 컬럼과 값을 추가하고 지정된 테이블에 추가하며, 컬럼의 참조를 BindCommand의 valid, bind, output MetaView에 등록합니다.

```ts
type addColumnValue = (
	name: string, 
	value: any, 
	cmds?: string | string[], 
	views?: string | string[], 
	bTable?: string | MetaTable
) => void;
```
- name : 컬럼 이름입니다.
- value : 컬럼 값입니다.
- cmds : 뷰의 위치를 지정하는 명령입니다. 문자열 또는 문자열 배열일 수 있습니다.
- views : 추가할 뷰 엔티티 이름입니다. 문자열 또는 문자열 배열일 수 있습니다.
- bTable : (선택적) 매핑할 기본 테이블 객체 또는 테이블 이름입니다.

### setMapping()

> 컬럼을 매핑합니다.

```ts
type setMapping = (
	mapping: PropertyCollection | object, 
	baseTable?: string | MetaTable
) => void;
```
- mapping : MetaColumn에 매핑할 객체 또는 컬렉션
- baseTable : (선택적) 매핑할 기본 테이블 객체 또는 테이블 이름입니다.

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
type equal = (target: object) => boolean;
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

