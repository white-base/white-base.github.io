---
lang: ko
title: "Notice example"
layout: fullwidth
permalink: /ko/exam/notice/
date: 2011-06-23T1
# toc: true
# toc_sticky: true
sidebar:
  nav: "exam"

last_modified_at: 2024-10-01
---

## 예제 화면

![image-left](/assets/images/notice-800.gif){: .align-center} 

## 환경 구성

```sh
git clone https://github.com/white-base/exam-bind-model.git
cd exam-bind-model
npx serve
```

## props drilling 이슈

리액트, 뷰, 앵글을 사용한 props drilling 문제는 데이터를 중앙에서 관리하고 비즈니스 로직을 명확하게 분리하여 유지보수 및 재사용성을 크게 개선하는 BindModel을 통해 한꺼번에 해결할 수 있습니다.

BindModel의 구성 요소인 MetaTable과 MetaView는 표준 인터페이스로 정의되어 화면 구성이 더욱 유연해집니다. React와 Vue는 화면 관련 책임만 관리하고 비즈니스 로직을 BindModel에 위임하여 비즈니스 로직을 다양한 화면에서 재사용할 수 있습니다.

