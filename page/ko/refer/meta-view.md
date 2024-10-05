---
lang: ko
title: "MetaView 클래스"
layout: single
permalink: /ko/docs/api-meta-view/
date: 2011-06-23T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"

# breadcrumbs: true
---

# 주요 구조

## 속성 관계

MetaView 는 MetaTable 과 동일하게 작동됩니다.  
차이점은  *_baseEntity* 가 지정될 경우 *add(name)* 컬럼을 추가하면, `columns` 에는 참조가 등록되고, 
baseEntity 의 `columns` 에  `MetaColumn` 이 등록됩니다.
*add(name, collection?)* 컬럼 추가시 `collection` 을 지정하면, `columns` 에참조가 등록되고,
지정한 `collecton` 에 `MetaColumn` 이 등록됩니다.

Class diagram
![image-center](/assets/images/view-rel-diagram-2024-08-16-002546.png){: .align-center}

## 상속 관계

Class diagram
![image-center](/assets/images/view-diagram-2024-08-16-004628.png){: .align-center}

# 주요 요소

## 속성

| 항목          | 설명                                   |
| ----------- | ------------------------------------ |
| viewName    | 메타 뷰 이름 입니다.                         |
| columns     | 뷰의 컬럼 컬렉션 입니다.                       |
| rows        | 엔티티의 데이터(로우) 컬렉션 입니다.                |
| _baseEntity | 기본 엔티티 입니다.                          |
| _metaSet    | 엔티티가 소속되어 있는 메타셋 입니다.                |
| _name       | 요소의 이름. MetaElement의 고유 식별자 역할을 합니다. |
| _guid       | 객체의 고유 식별자 (GUID). 객체를 고유하게 식별합니다.   |
| _type       | 객체의 생성자 함수. 객체가 생성될 때 사용된 함수입니다.     |
|             |                                      |


---

## 메소드

| 항목                               | 설명                                                   |
| -------------------------------- | ---------------------------------------------------- |
| clone()                          | 현재 메타 뷰의 깊은 복사본을 생성하여 반환합니다.                         |
| copy(filter, args)               | 대상 컬럼을 복사한다.                                         |
| transformSchema()                | 주어진 직렬화 객체를 스키마 객체로 변환합니다.                           |
| clear()                          | 엔티티의 모든 데이터를 초기화합니다.                                 |
| reset()                          | 엔티티의 컬럼 및 데이터를 초기화합니다.                               |
| newRow()                         | 컬럼 구조에 맞는 새로운 로우를 생성하여 반환합니다.                        |
| getValue()                       | 컬럼의 value 값을 MetaRow 타입 객체로 반환합니다.                   |
| setValue(row)                    | MetaRow 값을 컬럼의 value에 설정합니다.                         |
| merge(target, optoin, matchType) | 주어진 엔티티와 현재 엔티티를 병합합니다.                              |
| select(filter, args)             | 주어진 콜백 함수에 따라 로우를 조회합니다.                             |
| load(obj, parse)                 | 주어진 객체를 현재 엔티티로 불러옵니다. 기존 데이터를 초기화하고 새로운 데이터를 로드합니다. |
| output(vOpt, stringify, space)   | 현재 엔티티를 직렬화된 문자열로 출력합니다.                             |
| read(obj, option)                | 주어진 객체를 엔티티로 읽어옵니다. JSON 스키마 규칙을 따릅니다.               |
| readSchema(obj, createRow)       | 주어진 스키마 객체를 현재 엔티티로 읽어옵니다.                           |
| readData(obj)                    | 주어진 객체에서 존재하는 로우만 읽어옵니다.                             |
| write(vOpt)                      | 현재 엔티티를 스키마 타입의 객체로 변환하여 반환합니다.                      |
| writeSchema(vOpt)                | 현재 엔티티의 스키마를 스키마 타입의 객체로 변환하여 반환합니다.                 |
| writeData(vOpt)                  | 현재 엔티티의 데이터를 스키마 타입의 객체로 변환하여 반환합니다.                 |
| getObject(vOpt, owned)           | 객체를 특정 옵션에 따라 직렬화된 형태로 반환합니다. 순환 참조는 $ref 값으로 대체됩니다. |
| setObject(oGuid, origin)         | 주어진 직렬화 객체를 현재 객체로 설정합니다. 설정 시 기존 객체는 초기화됩니다.        |
| equal(target)                    | 현재 객체와 지정된 객체가 동일한지 비교합니다.                           |
| getTypes()                       | 현재 객체의 생성자와 프로토타입 체인의 모든 생성자를 배열로 반환합니다.             |
| instanceOf(target)               | 현재 객체가 지정된 타입의 인스턴스인지 확인합니다. (_UNION 포함)             |



# 세부 설명

## 주요 속성

### viewName

> 메타 뷰 이름 입니다.

```ts
type viewName = string;
```

### columns

> 뷰의 컬럼 컬렉션 입니다.

```ts
type columns = MetaTableColumnCollection;
```

### rows

> 테이블의  데이터(로우) 컬렉션 입니다.

```ts
type rows = MetaRowCollection;
```

### \_baseEntity

> 기본 엔티티 입니다.

```ts
type _baseEntity = BaseEntity;
```

### \_metaSet

> 테이블이 소속되어 있는 메타셋 입니다.

```ts
type _metaSet = MetaSet;
```

### \_guid

> 객체의 고유 식별자 (GUID). 객체를 고유하게 식별합니다.

```ts
type _guid: string;
```

### \_type

> 객체의 생성자 함수입니다. 객체가 생성될 때 사용된 함수입니다.

```ts
type _type: Function;
```

---
## 주요 메소드

### clone()

> 현재 객체의 깊은 복사본을 생성하여 반환합니다.

```ts
type clone() => MetaTable;
```
- return : 현재 객체의 복제본입니다.

### copy()

> 대상 컬럼을 복사한다.

```ts
type copy = (
	filter: (row, idx, entity) => boolean | string[], arguments<string>, 
	cols?: string[] | arguments<string>
) => MetaView;
```
- filter :   
	- Function  타입이면 컬럼을 선택하는 콜백함수를 입니다.
	-  string[]   타입이면 복사할 컬럼명입니다.
-   cols : 복사할 컬럼명입니다. filter 가 Function 타입일 때만 유효합니다.
#### 예제 : filter, cols 을 사용하는 경우
```js
var table = new MetaTable('t1');

// ... 컬럼 추가, aa, bb, cc, ee 및 rows 추가 

var temp = table.copy(
	(row, idx, entity) => { return (idx % 2) > 0; },
	['aa', 'bb']
);
```
- temp 에는 'aa', 'bb' 컬럼의 로우 인덱스가 홀수인 경우만 복사됩니다.
#### 예제 : cols 만 사용하는 경우
```js
var table = new MetaTable('t1');

// ... 컬럼 추가, aa, bb, cc, ee 및 rows 추가 

var temp = table.copy(
	['aa', 'bb']
);
```
- temp 에는 'aa', 'bb' 컬럼의 전체 로우가 복사됩니다.

### transformSchema()

> 주어진 직렬화 객체를 스키마 객체로 변환합니다.

```ts
type transformSchema = (oGuid: object) => object; // static
```
- oGuid : getObject()로 얻은 객체입니다.

### clear()

> 엔티티의 모든 데이터를 초기화합니다.

```ts
type clear = () => void;
```

### reset()

> 엔티티의 컬럼 및 데이터를 초기화합니다.

```ts
type reset = () => void;
```

### newRow()

> 컬럼 구조에 맞는 새로운 로우를 생성하여 반환합니다.

```ts
type newRow = () => MetaRow;
```
- return : 생성된 MetaRow 객체입니다.
### getValue()

> 컬럼의 value 값을 MetaRow 타입 객체로 반환합니다.

```ts
type getValue = () => MetaRow;
```
- return : 컬럼의 값이 설정된 MetaRow 객체입니다.

### setValue()

> MetaRow 값을 컬럼의 value에 설정합니다.

```ts
type setValue = (row: MetaRow) => void;
```
- row : 설정할 MetaRow 객체입니다.

### merge()

> 주어진 엔티티와 현재 엔티티를 병합합니다.

```ts
type merge = (target: BaseEntity, option: number, matchType?: boolean) => void;
```
- target : 병합할 대상 엔티티입니다.
- option : 병합 옵션입니다.
- matchType : 로우 유효성 검사 유무입니다. (기본값: false)

### select()

> 주어진 콜백 함수에 따라 로우를 조회합니다.

```ts
type select = (
	filter: (row, idx, entity) => boolean | string[], | arguments<string>,
	cols?: string[] | arguments<string>
) => MetaView;
```
- filter :   
	- Function  타입이면 컬럼을 선택하는 콜백함수를 입니다.
	-  string[]   타입이면 복사할 컬럼명입니다.
-   cols : 복사할 컬럼명입니다. filter 가 Function 타입일 때만 유효합니다.
#### 예제 : filter, cols 을 사용하는 경우
```js
var table = new MetaTable('t1');

// ... 컬럼 추가, aa, bb, cc, ee 및 rows 추가 

var temp = table.copy(
	(row, idx, entity) => { return (idx % 2) > 0; },
	['aa', 'bb']
);
```
- temp 뷰 에는 'aa', 'bb' 컬럼의 로우 인덱스가 홀수인 경우만 복사됩니다.
#### 예제 : cols 만 사용하는 경우
```js
var table = new MetaTable('t1');

// ... 컬럼 추가, aa, bb, cc, ee 및 rows 추가 

var temp = table.copy(
	['aa', 'bb']
);
```
- temp 뷰 에는 'aa', 'bb' 컬럼의 전체 로우가 복사됩니다.

### load()

> 주어진 객체를 현재 엔티티로 불러옵니다. 기존 데이터를 초기화하고 새로운 데이터를 로드합니다.

```ts
type load = (obj: object | string, parse?: Function) => void;
```
- obj : 불러올 대상 객체입니다.
- parse : 파서 함수입니다. (선택)

### output()

> 현재 엔티티를 직렬화된 문자열로 출력합니다.

```ts
type output = (vOpt: number, stringify?: Function, space?: string) => string;
```
- vOpt : 옵션입니다. (0, 1, 2)
- stringify : 사용자 정의 파서 함수입니다. (옵션)
- space : 출력 시 사용할 공백 문자열입니다. (옵션)

### read()

> 주어진 객체를 엔티티로 읽어옵니다. JSON 스키마 규칙을 따릅니다.

```ts
type read = (obj: object, option: number) => void;
```
- obj : 읽어올 대상 객체입니다.
- option : 읽기 옵션입니다. (기본값: 3)

```js
var schema1 = { 
	table: { 
		columns: {}, 
		rows: {} 
	}
};

var schema1 = { 
	columns: {...}, 
	rows: {} 
};
```

### readSchema()

> 주어진 스키마 객체를 현재 엔티티로 읽어옵니다.

```ts
type readSchema = (obj: object, createRow?: boolean) => void;
```
- obj : 읽어올 스키마 객체입니다.
- createRow : true일 경우, row[0] 기준으로 컬럼을 추가합니다. (기본값: false)

### readData()

> 주어진 객체에서 존재하는 로우만 읽어옵니다.

```ts
type readData = (obj: object) => void;
```
- obj : 읽어올 객체입니다.

### write()

> 현재 엔티티를 스키마 타입의 객체로 변환하여 반환합니다.

```ts
type write = (vOpt?: number) => object;
```
- vOpt : 옵션입니다. (기본값: 0)
- return : 스키마 타입의 객체입니다.

### writeSchema()

> 현재 엔티티의 스키마를 스키마 타입의 객체로 변환하여 반환합니다.

```ts
type writeData = (vOpt?: number): object;
```
- vOpt : 옵션입니다. (기본값: 0)
- return : 스키마 타입의 객체입니다.

### writeData()

> 현재 엔티티의 데이터를 스키마 타입의 객체로 변환하여 반환합니다.

```ts
type writeData = (vOpt?: number) => object;
```
- vOpt : 옵션입니다. (기본값: 0)
- return : 스키마 타입의 객체입니다.

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

