# 反射型xss(get)

# 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "xss_reflected_get.php"){
    $ACTIVE = array('','','','','','','','active open','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');

}

$PIKA_ROOT_DIR =  "../../";
include_once $PIKA_ROOT_DIR.'header.php';


$html='';
if(isset($_GET['submit'])){
    if(empty($_GET['message'])){
        $html.="<p class='notice'>输入'kobe'试试-_-</p>";
    }else{
        if($_GET['message']=='kobe'){
            $html.="<p class='notice'>愿你和{$_GET['message']}一样，永远年轻，永远热血沸腾！</p><img src='{$PIKA_ROOT_DIR}assets/images/nbaplayer/kobe.png' />";
        }else{
            $html.="<p class='notice'>who is {$_GET['message']},i don't care!</p>";
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
                    <a href="xss.php">xss</a>
                </li>
                <li class="active">反射型xss(get)</li>
            </ul><!-- /.breadcrumb -->

            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="管tmd什么xss,首先你应该输入kobe看一下再说">
                点一下提示~
            </a>

        </div>
        <div class="page-content">



            <div id="xssr_main">
                <p class="xssr_title">Which NBA player do you like?</p>
                <form method="get">
                    <input class="xssr_in" type="text" maxlength="20" name="message" />
                    <input class="xssr_submit" type="submit" name="submit" value="submit" />
                </form>
                <?php echo $html;?>
            </div>


        </div><!-- /.page-content -->
    </div>
</div><!-- /.main-content -->





<?php
include_once $PIKA_ROOT_DIR.'footer.php';

?>
```

可以看到这一关的代码没有进行任何xss过滤

***

# 二、通关教程

直接使用如下payload即可成功触发xss漏洞

![image-20230303143710488](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\XSS（跨站脚本）\images\image-20230303143710488.png)

检查源码可以看到成功插入js语句并成功执行

![image-20230303143817281](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\XSS（跨站脚本）\images\image-20230303143817281.png)

