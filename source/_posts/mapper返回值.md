---
title: mapper返回值
date: 2018-08-16 18:36:52
tags: [mybatis]
---
mapper.xml中的resultType可以是多种类型     
<!-- more -->     
## 返回类型为hashMap    
当resultType="hashMap"时，将会返回Map类型的结果集，key对应字段名，value对应字段对应的记录（```ps:在mapper.xml的标签语句内不要使用<!-- -->注释，这样的注释无效```）