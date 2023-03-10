# token防爆破

 # 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */

$PIKA_ROOT_DIR =  "../../";
include_once $PIKA_ROOT_DIR.'inc/config.inc.php';
include_once $PIKA_ROOT_DIR.'inc/mysql.inc.php';
include_once $PIKA_ROOT_DIR.'inc/function.php';

$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);
if ($SELF_PAGE = "bf_client.php"){
    $ACTIVE = array('','active open','','','','','active',"","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","","");

}


include_once $PIKA_ROOT_DIR.'header.php';

$link=connect();
$html="";

if(isset($_POST['submit']) && $_POST['username'] && $_POST['password'] && $_POST['token']){

    $username = $_POST['username'];
    $password = $_POST['password'];
    $token = $_POST['token'];

    $sql = "select * from users where username=? and password=md5(?)";
    $line_pre = $link->prepare($sql);


    $line_pre->bind_param('ss',$username,$password);

    if($token == $_SESSION['token']){

        if($line_pre->execute()){
            $line_pre->store_result();
            if($line_pre->num_rows>0){
                $html.= '<p> login success</p>';

            } else{
                $html.= '<p> username or password is not exists～</p>';
            }

        }else{
            $html.= '<p>执行错误:'.$line_pre->errno.'错误信息:'.$line_pre->error.'</p>';
        }


    }else{
        $html.= '<p> csrf token error</p>';
    }




}


//生成token
set_token();



?>


<div class="main-content" xmlns="http://www.w3.org/1999/html">
    <div class="main-content-inner">
        <div class="breadcrumbs ace-save-state" id="breadcrumbs">
            <ul class="breadcrumb">
                <li>
                    <i class="ace-icon fa fa-home home-icon"></i>
                    <a href="burteforce.php">暴力破解</a>
                </li>
                <li class="active">token防爆破?</li>

            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="token了解下,后面搞CSRF会用到....虽然这里并没有什么鸟用.">
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

        <form id="bf_client" method="post" action="bf_token.php" ">
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

                <input type="hidden" name="token" value="<?php echo $_SESSION['token'];?>" />

                <label><input class="submit"  name="submit" type="submit" value="Login" /></label>

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

该级别在每次登录中都会携带不同的token，只有token正确才能进行登录，否则就显示 csrf token error

***

# 二、使用burp suite爆破

在登录页面输入用户名和密码，用户名用admin，使用Burpsuite抓包后将该数据发送到Intruder模块中，将password与user_token字段添加为变量

将攻击类型Attack type设置为Pitchfork

![image-20230303141727370](https://github.com/LY613313/Node_File/tree/main/pikachu%E9%9D%B6%E5%9C%BA%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/%E6%9A%B4%E5%8A%9B%E7%A0%B4%E8%A7%A3/images/image-20230303141727370.png)

在Options下的Grep-Extract中点击Add添加攻击需要用到的信息(user_token)，在该页面点击Fetch response后在返回的代码中找到user_token的值，并将其选中后复制(后续需要用到)

![image-20230303142315157]
(https://github.com/LY613313/Node_File/tree/main/pikachu%E9%9D%B6%E5%9C%BA%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/%E6%9A%B4%E5%8A%9B%E7%A0%B4%E8%A7%A3/images/image-20230303142315157.png)

请求线程数设置为1

![image-20230303142425780](https://github.com/LY613313/Node_File/tree/main/pikachu%E9%9D%B6%E5%9C%BA%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/%E6%9A%B4%E5%8A%9B%E7%A0%B4%E8%A7%A3/images/image-20230303142425780.png)

选中Payload set 1 ，将Payload type 设置为Simple list

![image-20230303142506333](https://github.com/LY613313/Node_File/tree/main/pikachu%E9%9D%B6%E5%9C%BA%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/%E6%9A%B4%E5%8A%9B%E7%A0%B4%E8%A7%A3/images/image-20230303142506333.png)

选中Payload set 2 ，将Payload type设置为Recursive grep，将刚才复制的user_token值设置为第一次请求的user_token值

![image-20230303142536859](https://github.com/LY613313/Node_File/tree/main/pikachu%E9%9D%B6%E5%9C%BA%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/%E6%9A%B4%E5%8A%9B%E7%A0%B4%E8%A7%A3/images/image-20230303142536859.png)

爆破结果如下

![image-20230303142615543https://github.com/LY613313/Node_File/tree/main/pikachu%E9%9D%B6%E5%9C%BA%E9%80%9A%E5%85%B3%E6%95%99%E7%A8%8B/%E6%9A%B4%E5%8A%9B%E7%A0%B4%E8%A7%A3/images/image-20230303142615543.png)

