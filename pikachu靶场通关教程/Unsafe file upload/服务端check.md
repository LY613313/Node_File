# 服务端check

# 一、代码审计

该关卡的代码如下

```php+HTML
<?php
/**
 * Created by runner.han
 * There is nothing new under the sun
 */


$SELF_PAGE = substr($_SERVER['PHP_SELF'],strrpos($_SERVER['PHP_SELF'],'/')+1);

if ($SELF_PAGE = "clientcheck.php"){
    $ACTIVE = array('','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','active open','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');
}
$PIKA_ROOT_DIR =  "../../";
include_once $PIKA_ROOT_DIR . 'header.php';
include_once $PIKA_ROOT_DIR.'inc/uploadfunction.php';

$html='';
if(isset($_POST['submit'])){
//     var_dump($_FILES);
    $mime=array('image/jpg','image/jpeg','image/png');//指定MIME类型,这里只是对MIME类型做了判断。
    $save_path='uploads';//指定在当前目录建立一个目录
    $upload=upload_sick('uploadfile',$mime,$save_path);//调用函数
    if($upload['return']){
        $html.="<p class='notice'>文件上传成功</p><p class='notice'>文件保存的路径为：{$upload['new_path']}</p>";
    }else{
        $html.="<p class=notice>{$upload['error']}</p>";
    }
}


?>





<div class="main-content">
    <div class="main-content-inner">
        <div class="breadcrumbs ace-save-state" id="breadcrumbs">
            <ul class="breadcrumb">
                <li>
                    <i class="ace-icon fa fa-home home-icon"></i>
                    <a href="upload.php">unsafe upfileupload</a>
                </li>
                <li class="active">服务端check</li>
            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="MIME type了解一下">
                点一下提示~
            </a>

        </div>
        <div class="page-content">
            <div id="usu_main">
                <p class="title">这里只允许上传图片，不要乱搞！</p>
                <form class="upload" method="post" enctype="multipart/form-data"  action="">
                    <input class="uploadfile" type="file"  name="uploadfile" /><br />
                    <input class="sub" type="submit" name="submit" value="开始上传" />
                </form>
                <?php
                echo $html;//输出了路径，暴露了
                ?>
            </div>
        </div><!-- /.page-content -->
    </div>
</div><!-- /.main-content -->






<?php
include_once $PIKA_ROOT_DIR . 'footer.php';

?>
```

通过观察源码可以看到该关卡检查MIME类型，限制只允许类型为`image/jpg` | `image/jpeg` | `image/png`的文件可以被上传

***

# 二、通关教程

使用Burpsuite抓包，修改Content-Type字段的值为image/jpg | image/jpeg | image/png 其中一个，或者将php文件改为png、jpg、jpeg后上传再将文件名改回php

![image-20230308142131885](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Unsafe file upload\images\image-20230308142131885.png)

![image-20230308142156100](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Unsafe file upload\images\image-20230308142156100.png)