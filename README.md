# white-base.github.io


# 시작하기

---
## BindModel 이란?

BindModel은 웹과 Node.js 환경에서 작동하는 프론트엔드 프레임워크입니다. 명령과 엔티티(Table, View)를 기반으로 하여 단순함과 생산성을 목표로 설계되었습니다. HTML, CSS, JavaScript의 기초를 숙지한 상태에서 BindModel을 사용하여 손쉽게 웹사이트를 제작할 수 있습니다.

- 모든 데이터를 엔티티(MetaTable, MetaView)로 관리합니다.
- MVC 패턴에서 Controller의 역할을 수행하며, View(화면)와 완전히 분리있습니다.
- 명령(BindCommand) 기반의 프로세서를 제공하여, 일관된 개발 패턴을 제공합니다.
- 라우팅, 폼 관리, 클라이언트-서버 통신 등 웹 개발에 필요한 라이브러리를 조화롭게 통합한 모음집입니다.
- 다른 프레임워크의 연동하여 사용할 수 있습니다.

---
## 설치

### npm 을 이용한 설치

Node.js 환경에서 BindModel 을 설치하려면 다음 명령어를 사용하십시오.

```sh
npm install logic-bind-model
```

### 브라우저 환경에서의 설치

브라우저 환경에서는 BindModel 을 CDN을 통해 사용할 수 있습니다.

```html
<script src="https://cdn.example.com/bind-model.min.js"></script>
```


---
## 사용

BindModelAjax 는 프레임워크의 핵심 객체입니다.

### 서버 환경 (node.js)

Node.js 환경에서는 require 또는 import 문을 통해 BindModelAjax 을 사용할 수 있습니다.

예제 : CommonJS 에서 사용
```js
const { BindModelAjax } = require('logic-bind-model');

const bm = new BindModelAjax();
```


예제 : ES6 에서 사용
```js
import { BindModelAjax } from 'logic-bind-model';  

const bm = new BindModelAjax();
```

### HTML 환경

브라우저 환경에서는 `_L` 전역 변수를 통해서 접근합니다.

예제 : HTML 환경에서 사용
```html    
<script src="https://cdn.example.com/bind-model.pack.min.js"></script>
<script>
	const bm = new _L.BindModelAjax();
</script>
```

---
## 패키징

BindModelAjax는 axios 와 jQuery 모듈에 의존하여 서버와의 비동기 통신 및 DOM 조작을 수행합니다. 이러한 의존성을 반영하여 다양한 배포 패키지를 제공합니다.

### bind-model.pack.js,  bind-model.pack.min.js

이 패키지는 BindModelAjax와 함께 axios와 jQuery 라이브러리를 포함하고 있습니다. 이 패키지는 외부에서 별도로 axios나 jQuery를 설치하지 않아도, bind-model.pack.js 하나만으로 모든 기능을 사용할 수 있습니다. 

### bind-model.js,  bind-model.min.js

이 패키지는 BindModelAjax 만을 포함하고 있으며, axios와 jQuery는 포함되지 않습니다. 이 패키지를 사용할 경우, 외부에서 axios와 jQuery를 이미 포함하고 있거나, 별도로 관리하고 있을 때 유용합니다.

---
%% ## 의존성

### axios

BindModelAjax는 웹 애플리케이션에서 서버와의 비동기 통신을 구현하는 데 사용됩니다. 이 클래스는 내부적으로 HTTP 요청을 처리하기 위해 axios 라이브러리를 사용합니다. 

BindModelAjax는 BindModel 클래스를 상속하여 구현되며, 이를 통해 기본적인 모델 바인딩 기능을 제공받습니다. 사용자는 BindModel 클래스를 상속하여, axios를 활용한 서버와의 통신 로직을 자신의 요구에 맞게 커스터마이징할 수 있습니다. 

### jquery 

HTMLColumn은 웹 페이지 내 특정 DOM 요소에 접근하고 이를 조작하는 데 사용됩니다. 이 클래스는 DOM 요소에 접근하기 위해 jQuery 라이브러리를 사용합니다. 

HTMLColumn은 MetaColumn 클래스를 상속하여 구현되며, 사용자는 MetaColumn 클래스를 상속하여, jQuery를 활용한 DOM 조작 로직을 자신의 요구에 맞게 커스터마이징할 수 있습니다. 

--- %%

## 학생 정보 조회 예시

이 예시는 웹 애플리케이션에서 학생 정보를 서버로부터 가져와 화면에 표시하는 기능을 구현한 코드입니다. BindModelAjax 객체를 사용하여 AJAX 요청을 통해 데이터를 받아오고, 이를 HTML 요소에 바인딩하여 사용자가 학생의 학번과 이름을 확인할 수 있게 합니다.

### 데이터 영역

이 영역에서는 서버에서 반환되는 JSON 데이터를 설명합니다.

서버 정보: /user/1
```json
{
	"rows": {
		"user_no": "2020-1234",
		"user_name": "홍길동"
	}
}
```

### 화면 영역

화면은 학생의 학번과 이름을 표시하는 두 개의 HTML 요소로 구성되어 있습니다.

```html
<div>
	학번 : <h2 id="user_no"></h2>
</div>
<div>
	이름 : <input id="user_name" type="text"/>
</div>
```

### 스크립트 영역

스크립트는 `BindModelAjax` 객체를 사용하여 서버로부터 데이터를 가져오고, 이를 HTML 요소에 바인딩하는 역할을 합니다.

```js
// step 1 
var bm = new BindModelAjax();
bm.url = '/user/1';
// step 2
bm.addCommand('read', 3);
// step 3
bm.addColumn('user_no', 'read', 'output');
bm.addColumn('user_name', 'read', 'output');
// step 4
bm.columns['user_no'].selector = { key: '#user_no', type: 'text' };
bm.columns['user_name'].selector = { key: '#user_name', type: 'value' };
// step 5
bm.command['read'].execute();
```
1. `BindModelAjax` 객체를 생성합니다.
   bm.url 속성을 /user/1로 설정하여 서버의 엔드포인트를 지정합니다.
2. 'read'라는 이름의 BindCommand 객체를 추가합니다.
   두 번째 인자는 출력 옵션을 의미하며, 3은 데이터를 받아오는 컬럼 value 값에 설정합니다.
3. user_no와 user_name 컬럼을 추가합니다.
   이 컬럼들은 'read' 명령에 대한 output 역할을 수행하여 서버로부터 데이터를 받아옵니다.
4. 각각의 컬럼에 대해 selector 객체를 설정합니다.
   selector는 HTML 요소와 데이터를 바인딩하기 위한 설정입니다.
5. 'read' 명령을 실행합니다.
   가져온 데이터는 자동으로 각 컬럼에 바인딩된 selector에 연결된 HTML 요소에 반영됩니다. user_no와 user_name 컬럼의 데이터가 각각 `#user_no`와 `#user_name` 요소에 바인딩되어 화면에 표시됩니다.

이 코드는 BindModelAjax 객체를 통해 서버로부터 학생 정보를 받아와, 이를 HTML 요소에 자동으로 바인딩하여 사용자에게 표시하는 과정을 보여줍니다. 간단한 설정만으로 서버와의 비동기 통신 및 데이터 바인딩을 구현할 수 있어, 웹 애플리케이션 개발에 유용하게 활용될 수 있습니다.

---






