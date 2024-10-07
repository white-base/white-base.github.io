---
lang: ko
title: "BindModel 과 Vue Mix 비교"
layout: fullwidth
permalink: /ko/exam/notice-compare-vue/
date: 2011-06-23T1
# toc: true
# toc_sticky: true
sidebar:
  nav: "exam"

last_modified_at: 2024-10-01
---

## 설명

## 폴더 구조
```js
vue-mix/
├── componets/
│   ├── NoticeForm.js
│   └── NoticeList.js
├── app.js
└── admin.html
```

### app.js : Vue
---
이 코드는 비즈니스 로직과 발표의 핵심을 구현하는 코드입니다.

```js
import NoticeList from './components/NoticeList.js';
import NoticeForm from './components/NoticeForm.js';

const { createApp, ref } = Vue

const app = createApp({
  data() {
    return {
      notices: [],
      selectedNotice: null,
      statusOptions: {
        'D': 'Standby',
        'A': 'Activation',
        'H': 'Hidden'
      }
    };
  },
  methods: {
    fetchNotices() {
      axios.get('/notice/data/list.json')
      .then(response => {
        this.notices = response.data.rows.map(row => ({
          ntc_idx: row.ntc_idx,
          title: row.title,
          contents: row.contents,
          top_yn: row.top_yn,
          active_cd: row.active_cd,
          create_dt: row.create_dt
        })) || [];
      })
      .catch(error => {
        console.error('Error fetching notices:', error);
      });
    },
    selectNotice(notice) {
      this.selectedNotice = notice;
      axios.get(`/notice/data/list.json`)  // RESTful : `/notice/data/${ntc_idx}.json`
        .then(response => {
          const notice = response.data;
          this.selectedNotice = {
            ntc_idx: notice.ntc_idx,
            title: notice.title,
            contents: notice.contents, 
            top_yn: notice.top_yn,
            active_cd: notice.active_cd,
            create_dt: notice.create_dt
          };
        })
        .catch(error => {
          console.error('Error reading notice:', error);
        });
    },
    deselectNotice() {
      this.selectedNotice = null;
    },
    updateNotice(notice) {
      const isValid = this.validateNotice(notice);

      if (isValid) {
        axios.put(`/notice/data/list.json`, notice) // RESTful : '/notice/update/${notice.ntc_idx}`
          .then(() => {
            console.warn('Caution: Send to the test server, but the data is not reflected.', notice);
            this.deselectNotice();
          })
          .catch(error => {
            console.error('Error updating notice:', error);
          });
      } else {
        alert('Validation failed');
      }
    },
    deleteNotice(idx) {
      const deleteNotice = {
        ntc_idx: idx
      };
      if (confirm('Are you sure you want to delete it?')){
        axios.delete(`/notice/data/list.json`, deleteNotice)  // RESTful : `/notice/delete/${ntc_idx}`
          .then(() => {
            console.warn('Caution: Send to the test server, but the data is not reflected.', deleteNotice);
            this.deselectNotice();
            // 삭제 후 공지사항 목록을 다시 불러옴
            this.fetchNotices();
          })
          .catch(error => {
            console.error('Error deleting notice:', error);
          });
      }
    },
    validateNotice(notice) {
      let isValid = true;
      if (!notice.title || notice.title === '') {
        isValid = false;
      }
      if (!notice.top_yn || (notice.top_yn !== 'Y' && notice.top_yn !== 'N')) {
        isValid = false;
      }
      if (!notice.active_cd || (notice.active_cd !== 'D' && notice.active_cd !== 'A' && notice.active_cd !== 'H')) {
        isValid = false;
      }
      return isValid;
    }
  },
  mounted() {
    this.fetchNotices();
  },
  components: {
    'notice-list': NoticeList,
    'notice-form': NoticeForm
  }
});

app.mount('#app');
```


### app.js : Vue 와 BindModel Mix
---

```js
import NoticeList from './components/NoticeList.js';
import NoticeForm from './components/NoticeForm.js';
import NoticeAdminService from './service/notice-admin-svc.js'

const { createApp, ref } = Vue
const bm = new _L.BindModel(new NoticeAdminService());  

bm.url =' /notice/data/list.json';

const app = createApp({
  data() {
    return {
      notices: [],
      selectedNotice: null,
      statusOptions: {
        'D': 'Standby',
        'A': 'Activation',
        'H': 'Hidden'
      },
      bindModel: bm,
    };
  },
  methods: {
    selectNotice(idx) {
      this.selectedNotice = idx;
    },
    deselectNotice() {
      this.selectedNotice = null;
    },
  },
  components: {
    'notice-list': NoticeList,
    'notice-form': NoticeForm
  }
});

app.mount('#app');
```

### notice-admin-svc.js : Vue 와 BindModel Mix
---

```js
export default class NoticeAdminService {
    constructor() {
        var _this       = this;
        var _template   = null;     // Handlebars template

        this.items = {
            ntc_idx: { required: true },
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
            },
            delete:     {
                cbValid(valid, cmd) { 
                    if (confirm('Are you sure you want to delete it?')) return true;
                },
                cbBind(bind, cmd, setup) {
                    console.warn('Caution: Send to the test server, but the data is not reflected.', setup.data);
                },
            },
            list:       {
                outputOption: 1,
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
    }    
}
```
## 비교 
1. 비즈니스 로직의 격리 및 재사용 가능성:
두 번째 코드는 비즈니스 로직을 BindModel과 NoticeAdminService를 사용하여 별도의 클래스로 분리했습니다. 따라서 비즈니스 로직은 Vue 구성 요소와 분리되어 재사용할 수 있습니다. 이 구조를 통해 다른 Vue 구성 요소나 프로젝트에서 동일한 비즈니스 로직을 재활용할 수 있어 유지보수 및 확장성이 크게 향상됩니다.
2. 데이터 바인딩의 단순성:
BindModel을 사용하여 데이터를 가져오는 것과 같은 논리를 캡슐화하면 Vue 구성 요소에 대한 메서드 수를 줄일 수 있습니다. 첫 번째 코드는 직접 축시오스 호출을 사용하지만 두 번째 코드는 BindModel에서 이를 담당하므로 코드가 훨씬 간단해집니다.
3. 유지보수 및 확장성:
비즈니스 로직은 Vue 구성 요소에 직접 의존하지 않기 때문에 향후 API 변경이나 서비스 로직 변경은 NoticeAdminService에서만 수정할 수 있습니다. 이렇게 하면 코드 유지보수가 간소화됩니다. 반면에 첫 번째 코드는 Vue 구성 요소 내부에 비즈니스 로직이 포함되어 있어 로직을 변경할 때 여러 구성 요소를 수정해야 할 수 있습니다.
4. 테스트 가능성 향상:
두 번째 방법은 비즈니스 로직을 별도의 클래스로 분리했기 때문에 해당 클래스에 대해 독립적으로 단위 테스트를 작성할 수 있습니다. Vue 구성 요소 내부의 비즈니스 로직은 테스트하기가 더 복잡하지만 BindModel 방법은 테스트 가능한 코드 구조를 제공합니다.

> 결론적으로 두 번째 코드의 장점은 비즈니스 로직을 분리하여 코드의 재사용 가능성, 유지보수 및 테스트 가능성을 높이는 것입니다.
