# 存储型xss

# 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "xss_stored.php"){
    $ACTIVE = array('','','','','','','','active open','','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');
}

$PIKA_ROOT_DIR =  "../../";
include_once $PIKA_ROOT_DIR.'header.php';

include_once $PIKA_ROOT_DIR."inc/config.inc.php";
include_once $PIKA_ROOT_DIR."inc/mysql.inc.php";


$link=connect();
$html='';
if(array_key_exists("message",$_POST) && $_POST['message']!=null){
    $message=escape($link, $_POST['message']);
    $query="insert into message(content,time) values('$message',now())";
    $result=execute($link, $query);
    if(mysqli_affected_rows($link)!=1){
        $html.="<p>数据库出现异常，提交失败！</p>";
    }
}


if(array_key_exists('id', $_GET) && is_numeric($_GET['id'])){

    //彩蛋:虽然这是个存储型xss的页面,但这里有个delete的sql注入
    $query="delete from message where id={$_GET['id']}";
    $result=execute($link, $query);
    if(mysqli_affected_rows($link)==1){
        echo "<script type='text/javascript'>document.location.href='xss_stored.php'</script>";
    }else{
        $html.="<p id='op_notice'>删除失败,请重试并检查数据库是否还好!</p>";

    }

}


?>

<div class="main-content">
    <div class="main-content-inner">
        <div class="breadcrumbs ace-save-state" id="breadcrumbs">
            <ul class="breadcrumb">
                <li>
                    <i class="ace-icon fa fa-home home-icon"></i>
                    <a href="xss.php">xss</a>
                </li>
                <li class="active">存储型xss</li>
            </ul><!-- /.breadcrumb -->

            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="无用提示:这个留言板的框框是可以拉长和拉宽的,还行不,大兄弟!">
                点一下提示~
            </a>

        </div>
        <div class="page-content">

            <div id="xsss_main">
                <p class="xsss_title">我是一个留言板：</p>
                <form method="post">
                    <textarea class="xsss_in" name="message"></textarea><br />
                    <input class="xsss_submit" type="submit" name="submit" value="submit" />
                </form>
                <div id="show_message">
                    <br />
                    <br />
                    <p class="line">留言列表：</p>
                    <?php echo $html;
                    $query="select * from message";
                    $result=execute($link, $query);
                    while($data=mysqli_fetch_assoc($result)){
                        echo "<p class='con'>{$data['content']}</p><a href='xss_stored.php?id={$data['id']}'>删除</a>";
                    }

                    echo $html;
                    ?>



                </div>
            </div>



        </div><!-- /.page-content -->
    </div>
</div><!-- /.main-content -->





<?php
include_once $PIKA_ROOT_DIR.'footer.php';
?>
```

可以看到该关卡对xss是没有进行任何过滤

***

# 二、通关教程

存储型将留言的内容写入到数据库后，当用户访问该页面就会触发该漏洞，除非数据的内容被删除，使用payload如下

```
<script>alert('xss')</script>
```

![image-20230303145439207](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\XSS（跨站脚本）\images\image-20230303145439207.png)

切换到其他页面再点击这个页面，再次出现弹窗

![image-20230303145730287](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\XSS（跨站脚本）\images\image-20230303145730287.png)



