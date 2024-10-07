---
lang: ko
title: "BindModel 과 React Mix 비교"
layout: fullwidth
permalink: /ko/exam/notice-compare-react/
date: 2011-06-23T1
# toc: true
# toc_sticky: true
sidebar:
  nav: "exam"

last_modified_at: 2024-10-01
---
## 설명


## 리액트 폴더 구조
```js
react-mix1/
├── componets/
│   ├── NoticeAdminPage.js
│   ├── NoticeForm.js
│   └── NoticeList.js
├── app.js
└── admin.html
```

### NoticeAdminPage.js : React
---
이 코드는 비즈니스 로직과 발표의 핵심을 구현하는 코드입니다.

```js
import React from 'https://esm.sh/react';

import NoticeList from './NoticeList.js';
import NoticeForm from './NoticeForm.js';

const { useState, useEffect } = React;

export default function NoticeAdminPage() {
  const [notices, setNotices] = useState([]);
  const [selectedNotice, setSelectedNotice] = useState(null);
  const [formData, setFormData] = useState({
    title: '',
    contents: '',
    active_cd: 'D',
    top_yn: false,
  });

  useEffect(() => {
    fetchNotices();
  }, []);

  const fetchNotices = async () => {
    try {
      const response = await axios.get('/notice/data/list.json');
      setNotices(response.data.rows);
    } catch (error) {
      console.error('Failed to fetch notices:', error);
    }
  };

  const handleRead = (notice) => {
    setSelectedNotice(notice);
    setFormData({
      title: notice.title,
      contents: notice.contents || '',
      active_cd: notice.active_cd || 'D',
      top_yn: notice.top_yn === 'Y',
    });
  };

  const handleList = () => {
    setSelectedNotice(null);
  };

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    setFormData((prevFormData) => ({
      ...prevFormData,
      [name]: type === 'checkbox' ? checked : value,
    }));
  };

  const handleUpdate = async () => {
    if (!formData.title.trim()) {
      alert('Title is required.');
      return;
    }

    try {
      const response = await axios.put(`data/list/${selectedNotice.ntc_idx}`, formData);
      console.log('Notice updated successfully:', response.data);
      fetchNotices();
      setSelectedNotice(null);
    } catch (error) {
      console.error('Failed to update notice:', error);
    }
  };

  const handleDelete = async () => {
    try {
      const response = await axios.delete(`data/list/${selectedNotice.ntc_idx}`);
      console.log('Notice deleted successfully:', response.data);
      fetchNotices();
      setSelectedNotice(null);
    } catch (error) {
      console.error('Failed to delete notice:', error);
    }
  };

  return (
    React.createElement('div', { className: 'container mt-3' },
      React.createElement('h2', null, 'Notice Admin Page'),
      React.createElement('h5', null, 'Key Features: List inquiry/modification/deletion'),
      React.createElement('p', null, 'Data is transmitted when modified or deleted from the test page, but it is not actually processed.'),
      
      !selectedNotice ? (
        React.createElement(NoticeList, { notices, handleRead })
      ) : (
        React.createElement(NoticeForm, {
          selectedNotice,
          formData,
          handleChange,
          handleUpdate,
          handleDelete,
          handleList
        })
      )
    )
  );
}
```


### NoticeAdminPage.js : React 와 BindModel 의 Mix
---

```js
import React, { Component } from 'https://esm.sh/react';
import NoticeList from './NoticeList.js';
import NoticeForm from './NoticeForm.js';
import NoticeAdminService from '../service/notice-admin-svc.js'

export default class NoticeAdminPage extends Component {
  constructor(props) {
    super(props);
    
    this.bm = new _L.BindModel(new NoticeAdminService(this));  
    this.bm.url = '/notice/data/list.json';
      
    this.state = { selectedNotice: null };
  }

  componentDidMount() {
    this.bm.cmd['list'].execute();
  }

  handleList = () => {
    this.setState({ selectedNotice: null });
  };

  handleChange = (e) => {
    let { name, value, type, checked } = e.target;
    if (type === 'checkbox') value = checked ? 'Y' : 'N';
    this.bm.cols[name].value = value;  //  column value setting
    this.forceUpdate();           //  Forced screen rendering
  };

  render() {
    const { selectedNotice } = this.state;

    return (
      React.createElement('div', { className: 'container mt-3' },
        React.createElement('h2', null, 'Notice Admin Page'),
        React.createElement('h5', null, 'Key Features: List inquiry/modification/deletion'),
        React.createElement('p', null, 'Data is transmitted when modified or deleted from the test page, but it is not actually processed.'),
        
        React.createElement(NoticeList, { bindModel: this.bm }),
        !selectedNotice || (
          React.createElement(NoticeForm, {
            handleChange: this.handleChange,
            bindModel: this.bm
          })
        )
      )
    );
  }
}
```

### notice-admin-svc.js : React 와 BindModel 의 Mix
---

```js
export default class NoticeAdminService {
    constructor(reactThis) {
        const _this = this;

        this.items = {
            title: { required: true }
        },
        this.command = {
            create:     {
            },
            read:       {
                outputOption: 3,
            },
            update:     {
                cbBind(bind, cmd, setup) {
                    console.warn('Caution: Send to the test server, but the data is not reflected.', setup.data);
                },
                cbEnd(status, cmd, res)  {
                    if (res) {
                      alert('The post has been modified.');
                      reactThis.handleList();
                    }
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
                      reactThis.handleList();
                    }
                  }
            },
            list:       {
                outputOption: 1,
                cbEnd(status, cmd, res) {
                    reactThis.setState({ selectedNotice: null });
                }
            }
        };
        this.mapping = {
            ntc_idx:    { read:     ['bind', 'output'],     update:  'bind',               delete:     ['valid', 'bind'] },
            title:      { read:     'output',               update:  ['valid', 'bind'], },
            contents:   { read:     'output',               update:  'bind' },
            top_yn:     { read:     'output',               update:  ['valid', 'bind'], },
            active_cd:  { read:     'output',               update:  ['valid', 'bind'], },
            create_dt:  { read:     'output' },
        };
        this.fn = {
            handleRead: async (idx) => {
                _this.bindModel.cmd['read'].outputOption.index = Number(idx);
                await _this.bindModel.cmd['read'].execute();
                reactThis.setState({ selectedNotice: true });
            },
        };
    }    
}
```
## 비교


> 혼합 코드에서 클래스 구성 요소와 BindModel을 사용하면 다음을 포함하여 React의 건강 관리와 비즈니스 로직을 더 명확하게 분리할 수 있는 이점을 얻을 수 있습니다.

1. 비즈니스 로직의 재사용성 및 유지보수 개선: BindModel을 사용하면 비즈니스 로직과 구성 요소를 분리하고 관리할 수 있습니다. BindModel 내부에 데이터 바인딩 및 API 호출과 같은 로직을 정의함으로써 다른 구성 요소에서도 동일한 로직을 재사용할 수 있으며, 향후 로직 변경 시 유지보수가 용이합니다. 첫 번째 코드처럼 비즈니스 로직을 직접 구현하는 것보다 구조적으로 더 깔끔한 관리가 가능합니다.
2. 데이터 바인딩 일관성: BindModel.this.bm.cols[name].value 와 동일한 방식으로 열 값을 관리하면 사용자 입력과 데이터 변경 사항을 효율적으로 동기화할 수 있어 데이터 바인딩 일관성을 보장할 수 있습니다.
3. 명확한 건강 관리: 클래스 구성 요소는 건강 상태를 명시적으로 관리하며, BindModel과의 상호 작용을 통해 각 데이터의 상태 변화를 더 쉽게 추적할 수 있습니다. 이를 통해 구성 요소 간에 보다 체계적인 건강 관리가 가능합니다.
4. 대규모 프로젝트의 유연성: 이 접근 방식은 크고 복잡한 프로젝트에 특히 유리합니다. BindModel은 여러 구성 요소에서 비즈니스 로직을 일관되게 처리할 수 있도록 지원하며 확장성과 유연성이 뛰어납니다.

결론적으로, 혼합 코드는 특히 대규모 프로젝트나 복잡한 데이터 처리의 경우 코드의 가독성, 유지보수 및 재사용성을 크게 개선하기 위해 React의 상태 관리와 비즈니스 로직을 명확하게 구분합니다.