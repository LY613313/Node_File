# exec "eval"

# 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "rce_evel.php"){
    $ACTIVE = array('','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','active open','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');
}

$PIKA_ROOT_DIR =  "../../";
include_once $PIKA_ROOT_DIR . 'header.php';

$html='';
if(isset($_POST['submit']) && $_POST['txt'] != null){
    if(@!eval($_POST['txt'])){
        $html.="<p>你喜欢的字符还挺奇怪的!</p>";

    }

}


?>


<div class="main-content">
    <div class="main-content-inner">
        <div class="breadcrumbs ace-save-state" id="breadcrumbs">
            <ul class="breadcrumb">
                <li>
                    <i class="ace-icon fa fa-home home-icon"></i>
                    <a href="rce.php">rce</a>
                </li>
                <li class="active">exec "eval"</li>
            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="后台不会使用了eval()吧....">
                点一下提示~
            </a>


        </div>
        <div class="page-content">

            <div id="comm_main">
                <p class="comm_title">Here, 请提交一个你喜欢的字符串:</p>
                <form method="post">
                    <input class="ipadd" type="text" name="txt" />
                    <input class="sub" type="submit" name="submit" value="提交" />
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

通过观察源码可以得到该关卡对命令注入无任何防护

***

# 二、通关教程

通过POST传参到eval()函数中执行，可以直接使用蚁剑之类的工具去连接该页面，密码txt，需要传递submit值

![image-20230308131635677](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\RCE\images\image-20230308131635677.png)

或者直接使用shell_exec()或system()等函数执行系统命令写入shell

