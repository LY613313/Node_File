# CSRF Token

# 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "csrf_get_edit.php"){
    $ACTIVE = array('','','','','','','','','','','','','','','','','','','','','','','','','','active open','','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');

}


$PIKA_ROOT_DIR =  "../../../";
include_once $PIKA_ROOT_DIR . 'header.php';

include_once $PIKA_ROOT_DIR."inc/config.inc.php";
include_once $PIKA_ROOT_DIR."inc/function.php";
include_once $PIKA_ROOT_DIR."inc/mysql.inc.php";
$link=connect();
// 判断是否登录，没有登录不能访问
if(!check_csrf_login($link)){
//    echo "<script>alert('登录后才能进入会员中心哦')</script>";
    header("location:token_get_login.php");
}

$html1='';


if(isset($_GET['submit'])){
    if($_GET['sex']!=null && $_GET['phonenum']!=null && $_GET['add']!=null && $_GET['email']!=null && $_GET['token']==$_SESSION['token']){
        //转义
        $getdata=escape($link, $_GET);
        $query="update member set sex='{$getdata['sex']}',phonenum='{$getdata['phonenum']}',address='{$getdata['add']}',email='{$getdata['email']}' where username='{$_SESSION['csrf']['username']}'";
        $result=execute($link, $query);
        //没有修改，点击提交，也算修改成功
        if(mysqli_affected_rows($link)==1 || mysqli_affected_rows($link)==0){
            header("location:token_get.php");
        }else {
            $html1.="<p>修改失败,请重新登录</p>";

        }
    }
}
//生成token
set_token();

?>


<div class="main-content">
    <div class="main-content-inner">
        <div class="breadcrumbs ace-save-state" id="breadcrumbs">
            <ul class="breadcrumb">
                <li>
                    <i class="ace-icon fa fa-home home-icon"></i>
                    <a href="../csrf.php">CSRF</a>
                </li>
                <li class="active">CSRF Token</li>
            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="源码看一下,有个隐藏的token,干啥用的的?">
                点一下提示~
            </a>

        </div>
        <div class="page-content">

            <?php
            //通过当前session-name到数据库查询，并显示其对应信息
            $username=$_SESSION['csrf']['username'];
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
   <form method="get">
   <h1 class="per_title">hello,{$name},欢迎来到个人会员中心 | <a style="color:bule;" href="token_get.php?logout=1">退出登录</a></h1>
   <p class="per_name">姓名:{$name}</p>
   <p class="per_sex">性别:<input type="text" name="sex" value="{$sex}"/></p>
   <p class="per_phone">手机:<input class="phonenum" type="text" name="phonenum" value="{$phonenum}"/></p>    
   <p class="per_add">住址:<input class="add" type="text" name="add" value="{$add}"/></p> 
   <p class="per_email">邮箱:<input class="email" type="text" name="email" value="{$email}"/></p>
   <input type="hidden" name="token" value="{$_SESSION['token']}" />
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

CSRF的主要问题是敏感操作的链接容易被伪造。每次请求，都增加一个随机码（需要够随机，不容易伪造），后台每次对随机码进行验证

网页接受从后台发过来的token,类型不可见。将其一并提交给后台进行验证。每次刷新，后台发送过来的token都不一样，起到了防止伪造的作用。

修改用户信息并提交，在burpsuite的proxy模块可以看到报文中包含token（如下图红框中所示）

![](C:\\Users\\linyunong\\Desktop\\Note\\pikachu靶场通关教程\\CSRF\\images\\image-20230304235335469.png)

试了一下，这关删除token是无法修改用户信息的，多抓几个包之后也没有看出token有什么规律。
在一个浏览器上以allen登录，到修改信息的页面，查看网页源代码获取token，再到另一个浏览器以lili登录，构造payload包含此token也是无法攻击成功的。

看一下代码，修改用户信息时，服务器会比较url中的token字段和session中的token字段，如果相同才能修改用户信息。修改完用户信息之后，会用set_token()函数生成新的token，将其返回到html表单中并隐藏起来，以便下次用户修改信息时代入url。set_token()函数如下图所示，在生成新token之前会先销毁老token，避免token重复使用。
