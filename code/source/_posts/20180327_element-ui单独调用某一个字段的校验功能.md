---
title: 20180327_element-ui单独调用某一个字段的校验功能
date: 2018-03-27 15:44:11  
description: 20180327_element-ui单独调用某一个字段的校验功能
---
# 20180327_element-ui单独调用某一个字段的校验功能

```js
 this.$refs[ruleFormName].validateField('checkPass');
```
 直接调用表单需要校验的某个prop即可(问了两个同事都不知道有这个方法)，[官方示例](http://element.eleme.io/#/zh-CN/component/form#zi-ding-yi-xiao-yan-gui-ze)