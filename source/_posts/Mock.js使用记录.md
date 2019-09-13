---
title: Mock.js使用记录
date: 2019-08-21 18:30:30
tags: '前端, JavaScript'
---

导入方式

`const Mock = require('mockjs')`

按需导入

`import { mock, setup } from 'mockjs'`

使用`Mock.mock(url,method,template)`拦截请求

#### 1.返回的对象复杂，内部需要条件语句来判断，一定概率地返回对应结果

使用`mock(template)`得到随机的结果，然后使用`function`返回

```javascript
mock('/serverInfo', {
  'items|1-10': [
    {
      'serverId': /Server\w{5,10}/,
      'errorCount': function () {
        return mock('@boolean(2, 3, true)') ? mock('@integer(1, 10)') : 0
      },
      'count|1000-10000': 10000
    }
  ]
})
```

#### 2.get请求url中带有请求参数

rest风格api需要动态匹配

url使用正则匹配

```javascript
mock(RegExp('/server/info/' + '.*'), {
  'data': function () {
    let users = mock({ 'array|3-10': [function () { return mock('@name') }] })['array']
    let apis = mock({ 'array|4-10': [/API-\w{2,4}/] })['array']
    return mockdata(users, apis)
  } }
)
```
