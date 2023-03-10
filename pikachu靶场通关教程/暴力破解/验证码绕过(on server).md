# 验证码绕过(on server)

# 一、代码审计

该关卡的源码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */

$PIKA_ROOT_DIR =  "../../";
include_once $PIKA_ROOT_DIR.'inc/config.inc.php';
include_once $PIKA_ROOT_DIR.'inc/mysql.inc.php';


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);
if ($SELF_PAGE = "bf_server.php"){
    $ACTIVE = array('','active open','','','active',"","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","");

}

include_once $PIKA_ROOT_DIR.'header.php';


$link=connect();


$html="";
if(isset($_POST['submit'])) {
    if (empty($_POST['username'])) {
        $html .= "<p class='notice'>用户名不能为空</p>";
    } else {
        if (empty($_POST['password'])) {
            $html .= "<p class='notice'>密码不能为空</p>";
        } else {
            if (empty($_POST['vcode'])) {
                $html .= "<p class='notice'>验证码不能为空哦！</p>";
            } else {
//              验证验证码是否正确
                if (strtolower($_POST['vcode']) != strtolower($_SESSION['vcode'])) {
                    $html .= "<p class='notice'>验证码输入错误哦！</p>";
                    //应该在验证完成后,销毁该$_SESSION['vcode']
                }else{

                    $username = $_POST['username'];
                    $password = $_POST['password'];
                    $vcode = $_POST['vcode'];

                    $sql = "select * from users where username=? and password=md5(?)";
                    $line_pre = $link->prepare($sql);

                    $line_pre->bind_param('ss',$username,$password);

                    if($line_pre->execute()){
                        $line_pre->store_result();
                        //虽然前面做了为空判断,但最后,却没有验证验证码!!!
                        if($line_pre->num_rows()==1){
                            $html.='<p> login success</p>';
                        }else{
                            $html.= '<p> username or password is not exists～</p>';
                        }
                    }else{
                        $html.= '<p>执行错误:'.$line_pre->errno.'错误信息:'.$line_pre->error.'</p>';
                    }
                }
            }
        }
    }
}



?>


<div class="main-content" xmlns="http://www.w3.org/1999/html">
    <div class="main-content-inner">
        <div class="breadcrumbs ace-save-state" id="breadcrumbs">
            <ul class="breadcrumb">
                <li>
                    <i class="ace-icon fa fa-home home-icon"></i>
                    <a href="burteforce.php">暴力破解</a>
                </li>
                <li class="active">验证码绕过(on server)</li>

            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="这个验证码好像一直有效哎!用户还是那三个默认用户.">
                点一下提示~
            </a>

        </div>
<div class="page-content">


<div class="bf_form">
    <div class="bf_form_main">
        <h4 class="header blue lighter bigger">
            <i class="ace-icon fa fa-coffee green"></i>
            Please Enter Your Information
        </h4>

        <form method="post" action="bf_server.php">
<!--            <fieldset>-->
                <label>
                    <span>
                        <input type="text" name="username" placeholder="Username" />
                        <i class="ace-icon fa fa-user"></i>
                    </span>
                </label>
                </br>

                <label>
                    <span>
                        <input type="password" name="password" placeholder="Password" />
                        <i class="ace-icon fa fa-lock"></i>
                    </span>
                </label>
                </br>
                <label>
                        <span>
                            <input type="text" name="vcode" placeholder="验证码" />
                            <i class="ace-icon fa fa-lock"></i>
                        </span>
                </label>
                </br>
                <label>
                    <img src="../../inc/showvcode.php" onclick="this.src='../../inc/showvcode.php?'+new Date().getTime();" />
                </label>


                <div class="space"></div>

                <div class="clearfix">
                    <label><input class="submit" name="submit" type="submit" value="Login" /></label>
                </div>

        </form>
        <?php echo $html;?>


    </div><!-- /.widget-main -->

</div><!-- /.widget-body -->



</div><!-- /.page-content -->
</div>
</div><!-- /.main-content -->



<?php
include_once $PIKA_ROOT_DIR.'footer.php';
?>
```

可以看到网站只验证用户输入的验证码是否正确，正确后再判断用户密码是否正确，但在最后却没有再次验证验证码是否正确

***

# 二、使用burp suite爆破

使用burp suite抓取登录时的数据包，然后右键选择send to repeater，如下

![image-20230303134039381](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\暴力破解\images\image-20230303134039381.png)

通过抓包将数据发送到Repeater模块后点击Run放行数据，可以看到返回的页面提示用户名或密码错误

将Repeater模块中password字段修改为其他字符，再次提交可以发现提示的信息还是用户名或密码错误，而不是验证码错误，可以判断该验证码一直有效

![image-20230303134134515](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\暴力破解\images\image-20230303134134515.png)

![image-20230303134156269](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\暴力破解\images\image-20230303134156269.png)

同上一关同样的方法进行爆破即可

![image-20230303134321460](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\暴力破解\images\image-20230303134321460.png)

