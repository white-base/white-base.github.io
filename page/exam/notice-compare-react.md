---
title: "notice React Mix 비교"
layout: fullwidth
permalink: /exam/notice-compare-react/
date: 2011-06-23T1
# toc: true
# toc_sticky: true
sidebar:
  nav: "exam"
---

## 설명


## React 폴더 구조
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
공지사항에서 비즈니스로직과 핵심이 구현된 코드입니다.
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


### NoticeAdminPage.js : Mix React and BindModel
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

### notice-admin-svc.js : Mix Vue and BindModel
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


> Mix된 코드에서 클래스 컴포넌트와 BindModel을 활용하는 방식은 React의 상태 관리와 비즈니스 로직을 더욱 명확하게 분리하여 관리할 수 있는 장점을 제공합니다. 이 방식의 주요 장점은 다음과 같습니다.

1.	비즈니스 로직의 재사용성과 유지보수성 향상: BindModel을 통해 비즈니스 로직을 컴포넌트에서 분리하여 관리할 수 있습니다. 데이터 바인딩과 API 호출 등의 로직을 BindModel 내부에 정의하면, 다른 컴포넌트에서 동일한 로직을 재사용할 수 있으며, 추후 로직 변경 시 유지보수도 용이합니다. 첫 번째 코드처럼 직접적으로 비즈니스 로직을 구현하는 것보다 구조적으로 더 깔끔한 관리가 가능합니다.
2.	데이터 바인딩의 일관성: BindModel을 통해 명시적으로 데이터와 화면 요소를 연결함으로써 데이터 바인딩의 일관성을 보장할 수 있습니다. this.bm.cols[name].value와 같은 방식으로 컬럼 값을 관리하면, 사용자 입력과 데이터 변경 사항을 효율적으로 동기화할 수 있습니다.
3.	명확한 상태 관리: 클래스 컴포넌트는 상태를 명시적으로 관리하고, BindModel과의 상호작용을 통해 각 데이터의 상태 변화를 쉽게 추적할 수 있습니다. 이를 통해 컴포넌트 간의 상태 관리를 더욱 체계적으로 할 수 있습니다.
4.	대규모 프로젝트에서의 유연성: 이 방식은 규모가 크고 복잡한 프로젝트에서 특히 유리합니다. BindModel은 여러 컴포넌트에서 비즈니스 로직을 일관되게 처리할 수 있도록 도와주며, 확장성과 유연성이 높습니다.

결론적으로, Mix된 코드는 React의 상태 관리와 비즈니스 로직을 명확하게 분리하여 코드의 가독성, 유지보수성, 재사용성을 크게 개선할 수 있는 방식을 제시하며, 특히 대규모 프로젝트나 복잡한 데이터 처리에 적합합니다.