---
title: font总结
date: 2016-04-20 17:48:18
tags: [CSS]
categories: work
---

- letter-spacing字间距属性用于定义字间距，所谓字间距就是字符与字符之间的空白。其属性值可为不同单位的数值，允许使用负值，默认为normal。

- word-spacing单词间距属性用于定义英文单词之间的间距，对中文字符无效。和letter-spacing一样，其属性值可为不同单位的数值，允许使用负值，默认为normal。

<!-- more -->

word-spacing和letter-spacing均可对英文进行设置。不同的是letter-spacing定义的为字母之间的间距，而word-spacing定义的为英文单词之间的间距。

- text-decoration文本装饰，none：没有装饰（正常文本默认值）、underline：下划线、overline：上划线、line-through：删除线。

- 使用white-space属性可设置空白符的处理方式，normal：常规（默认值）、文本中的空格、空行无效，满行（到达区域边界）后自动换行、pre：预格式化，按文档的书写格式保留空格、空行原样显示；
nowrap：空格空行无效，强制文本不能换行，除非遇到换行标记<br />。内容超出元素的边界也不换行，若超出浏览器页面则会自动增加滚动条。（常和overflow:hidden一起用）

- word-break自动换行：normal	 使用浏览器默认的换行规则、break-all   允许在单词内换行；（常用）、keep-all	只能在半角空格或连字符处换行。

- word-wrap ：（长单词或 URL 地址换行到下一行），normal	只在允许的断字点换行（浏览器保持默认处理）、break-word	在长单词或 URL 地址内部进行换行。（常用）

- text-overflow：（一般与overflow:hidden何用;），clip :不显示省略标记（...），而是简单的裁切、ellipsis : 　当对象内文本溢出时显示省略标记（...）

- 常见可继承的属性：
color、cursor、font-size、font-family、font-style、font-weight、letter-spacing、white-space、word-spacing、line-height、list-style、text-align、text-indent...



