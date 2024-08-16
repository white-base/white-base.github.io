---
title: "기본 사용법"
layout: single
permalink: /docs/basic/
date: 2024-08-14T1
toc: true
toc_sticky: true
sidebar:
  nav: "docs"
---

## BindModel 생성

`BindModelAjax` 객체 생성은 데이터 바인딩과 서버 간 통신을 위한 첫 번째 단계입니다.
 이 객체는 서버와의 AJAX 통신을 관리하는 핵심 역할을 합니다.
 
[[52. BindModelAjax 클래스-B| - 참조 : BindModel 구성]]
[[25. 서비스 객체 구성-C| - 참조 : 서비스객체 구성]]

```js
var bm = new BindModelAjax();

bm.url = '/user';
```
- '/user' 경로는 사용자의 정보와 관련된 API 요청을 처리하기 위한 기본 경로로 설정됩니다.
- 'command' 별로 'url' 을 설정할 수도 있습니다.


## command 추가

BindModelAjax 객체에 새로운 명령(command)을 추가하려면 addCommand() 메서드를 사용합니다. 이 메서드는 BindCommand 객체를 생성하고, 이를 BindModelAjax에 등록하여 서버와의 데이터 통신을 관리할 수 있도록 합니다.

BindCommand 객체는 서버와의 데이터 통신을 위해 세 가지 주요 역할을 하는 MetaView 객체들을 포함하고 있습니다.
- **valid** : 데이터의 유효성을 검사하는 역할을 합니다.
- **bind** : 클라이언트의 데이터가 서버에 전달되기 전에 데이터를 바인딩하는 역할을 합니다. 
- **output** : 서버로부터 수신된 데이터를 가져오는 역할을 합니다.

[[53. BindCommandAjax 클래스-B| - 참조 : BindCommand 구성]]

```js
bm.addCommand('newCmd', 3);

// bm.command['newCmd'] === bm.cmd['newCmd']
// bm.command['newCmd'] instanceof BindCommand
// bm.cmd['newCmd'].vallid instanceof MetaView
// bm.cmd['newCmd'].bind instanceof MetaView
// bm.cmd['newCmd'].output instanceof MetaView
```
- 출력옵션 에 따라 다르게 동작합니다. (범위 : 0, 1, 2, 3 )
- `'bm.command['명칭']'` 으로 `BindCommand` 객체에 접근할 수 있습니다.
- `bm.cmd['명칭']` 는 동일한 객체를 참조하며, 이 중 더 짧은 `bm.cmd` 를 별칭으로 사용할 수 있습니다.

[[24. 바인드 커멘드 구성-C#출력옵션의 종류 (output) | - 참조 : 출력옵션의 종류]]


## column 추가

addColumn() 메서드는 BindModelAjax 객체에 컬럼을 추가하고, 지정된 BindCommand 객체의 MetaView에 컬럼을 설정하는 기능을 제공합니다. 추가적으로, addColumnValue() 메서드를 사용하여 컬럼의 초기값을 설정할 수 있습니다.

예시 : 빈 컬럼 추가
```js
bm.addColumn('aa', 'newCmd', 'valid');
bm.addColumn('bb', 'newCmd', ['valid', 'bind']);
bm.addColumn('cc', 'newCmd', '$all');
```
- 'aa' 이름으로 컬럼을 추가하고 cmd['newCmd'] 의 valid(MetaView)에 설정합니다.
- 'bb' 이름으로 컬럼을 추가하고 cmd['newCmd'] 의 'valid', 'bind' 에 설정합니다.
- 'cc' 이름으로 컬럼을 추가하고 cmd['newCmd'] 의 전체('valid', 'bind', 'output')에 설정합니다.

예제 : 초기값으로 컬럼 추가
```js
bm.addColumnValue('aa', 100, 'newCmd', 'valid');
bm.addColumnValue('bb', 'B', 'newCmd', ['valid', 'bind']);
bm.addColumnValue('cc', true, 'newCmd', '$all');
```
- 'aa' 이름으로 `100` 초기값으로 컬럼을 추가하고 cmd['newCmd'] 의 'valid' 에 설정합니다.
- 'bb' 이름으로 `'B'` 초기값으로 컬럼을 추가하고 cmd['newCmd'] 의 'valid', 'bind' 에 설정합니다.
- 'cc' 이름으로 `true` 초기값으로 컬럼을 추가하고 cmd['newCmd'] 의 전체에 설정합니다.

[[23. 바인드 모델 구성-B#컬럼 추가하기 | - 참조 : 컬럼 추가하기]]
[[54. HTMLColumn 클래스-B| - 참조 : column 구성]]


## 실행

BindCommand 객체의 execute() 메서드는 유효성 검사, 데이터 요청, 데이터 수신의 세 가지 주요 단계를 처리합니다. 각 단계는 콜백 함수를 통해 제어할 수 있으며, 이를 통해 요청의 흐름을 세부적으로 관리할 수 있습니다.

```js
bm.command['newCmd'].execute();
```
- execute() 메소드 호출시 세 가지 주요 단계를 거칩니다.
	- 유효성 검사 : 'valid' 컬럼 유효성 검사를 진행하고, 실패하면 'cbFail' 콜백을 호출합니다.
	- 데이터 바인딩 : 'bind' 에 컬럼과 같을 서버경로에 요청합니다.
	- 데이터 수신 : 수신된 데이터를 'output' 으로 가져옵니다.

[[41. 콜백 라이프사이클-B| 참조 : callback 라이프사이클]]
