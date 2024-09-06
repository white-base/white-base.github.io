---
title: "notice Vue Mix 비교"
layout: fullwidth
permalink: /exam/notice-compare-vue/
date: 2011-06-23T1
# toc: true
# toc_sticky: true
sidebar:
  nav: "exam"
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
공지사항에서 비즈니스로직과 핵심이 구현된 코드입니다.
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


### app.js : Mix Vue and BindModel
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

### notice-admin-svc.js : Mix Vue and BindModel
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
1.	비즈니스 로직의 분리 및 재사용성:
두 번째 코드에서는 BindModel과 NoticeAdminService를 통해 비즈니스 로직을 별도의 클래스로 분리했습니다. 이를 통해 비즈니스 로직을 Vue 컴포넌트와 분리하고 재사용 가능하게 만듭니다. 이 구조는 동일한 비즈니스 로직을 다른 Vue 컴포넌트나 프로젝트에서 재활용할 수 있게 도와, 유지보수성과 확장성을 크게 향상시킵니다.
2.	데이터 바인딩의 간결성:
BindModel을 사용하여 데이터를 가져오는 작업과 같은 로직을 캡슐화함으로써, Vue 컴포넌트의 메소드 수를 줄일 수 있습니다. 첫 번째 코드에서는 직접 axios 호출을 사용하지만, 두 번째 코드는 BindModel에서 이 책임을 담당하므로 코드가 훨씬 간결해집니다.
3.	유지보수성 및 확장성:
비즈니스 로직이 Vue 컴포넌트에 직접적으로 의존하지 않으므로, 나중에 API 변경이나 서비스 로직이 바뀌어도 이를 NoticeAdminService에서만 수정하면 됩니다. 이는 코드의 유지보수를 단순하게 만듭니다. 반면, 첫 번째 코드에서는 비즈니스 로직이 Vue 컴포넌트 내부에 포함되어 있어, 로직 변경 시 여러 컴포넌트를 수정해야 할 수 있습니다.
4.	테스트 가능성 향상:
두 번째 방식에서는 비즈니스 로직을 별도의 클래스로 분리했기 때문에, 해당 클래스에 대한 유닛 테스트를 독립적으로 작성할 수 있습니다. Vue 컴포넌트 내부에 있는 비즈니스 로직은 테스트가 더 복잡하지만, BindModel 방식은 테스트 가능한 코드 구조를 제공합니다.

> 결론적으로, 두 번째 코드의 장점은 비즈니스 로직을 분리하여 코드의 재사용성, 유지보수성 및 테스트 가능성을 높이는 점에 있습니다.