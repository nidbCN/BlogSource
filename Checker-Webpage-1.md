---
title: PHP考勤系统源码 利用PHP实现简单的打卡考勤 超详细教程+实例+代码（一）
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - 网站建设
  - 编程
  - PHP
date: 2020-02-08 22:36:25
---

利用PHP实现简单的考勤打卡、人数统计功能。

> 这什么屑玩意啊，黑历史黑历史。

## 先讲个故事...

由于疫情原因，开学延迟，班主任为了监督我们学习，所以叫我们每天打卡。那么就要到群里点人，还要统计，谁打卡了谁没打卡谁迟到...我一想太麻烦了

![截屏1](https://img.cdn.gaein.cn/website_used/blog/Checker-Webpage-1/01.webp)

![截屏2](https://img.cdn.gaein.cn/website_used/blog/Checker-Webpage-1/02.webp)

于是我在群里水了一句，要不然开发个考勤系统吧

![截屏3](https://img.cdn.gaein.cn/website_used/blog/Checker-Webpage-1/03.webp)

当然我当时的开发指的是“面向百度”编程（就是随便找个什么源码改一下title。

结果我发现行不通...没有符合我要求的源码，只好试试了。

我虽然接触了点编程，不过PHP还真没碰过，想想编程语言也都是26个字母只好硬着头皮干了。

## 要求

明确一下自己的期望吧：

1. 能记录打卡人（开始就这一个想法）

好的没有了。

现在实现的功能（很多都是我晚上在被窝的新奇想法）

1. 记录打卡人，打卡时间，自动标注准时或者迟到；
2. 打卡成功后返回页面，提示成功与否并且提示信息（非常智障的实现，我跳转了一个html并且get传参信息，当时自己不知道php echo能输出html）；（2021补:什么我为什么要用php echo来输出html？？？）
3. 后台统计打卡人数、未打卡人数、迟到人数还有未打卡人名单；

## 准备

1. 语言：思考来思考去，服务器只装着PHP，空间倒是有 `.NET fw`，不过看看还是PHP的网站居多；
2. 实现：操作数据库一想就头大了，MySQL这玩意真玩不了，上次导入旧博客数据就差点没玩死我，想想还是直接磁盘上写文件吧；
3. 服务器：本来想用我一个虚拟主机来着，不过那玩意权限有问题，写不了文件，无奈搬到网站服务器上面；
4. IDE：没有专门去找PHP的IDE，直接用的vscode+网上随便找一个在线PHP作为调试；

## 开工

### 表单页面

#### 表单创建

想来想去输入一个姓名就可以了，后来又想想需要一个时间，那么html如下:

``` html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>考勤打卡</title>
  </head>
  <body>
	  <form action="process.php" method="post">
      姓名：<input type="text" name="name"><br>
      时间：<input type="text" name="time"><br>
      日期：<input type="text" name="filename"><br>
	    <input type="submit" value="打卡 ">
    </form> 
  </body>
</html>
```

看起来是这样的：

![界面](https://img.cdn.gaein.cn/website_used/blog/Checker-Webpage-1/04.webp)

（丑死了）

` <!DOCTYPE html> ` 和 `meta` 是必须的，否则有可能乱码。

`form` 标签里面是表单，`action` 后面是提交到的地址，`method` 是方法（别问我为啥用 `post` ，因为它看起来好看）

`input` 中的 `type` 是输入的类型，`text` 自然就是文本啦，`submit` 是提交表单，`name` 是参数的名字（我日期参数是 `filename` 是因为我想把存储打卡信息的文件用日期命名），`value` 是默认的值。

#### 时间获取

那么又一个问题来了，时间和日期怎么获取？这就要用的`javascript` 了，`js` 获取时间和日期后再去改动 `html`，为了找到他们我把他们用 `div` 框起来然后给他们 `id` 为 `0` 和 `1`（懒死了）

`js` 的时间获取百度一下就有，不过获取的都是浏览网页的人电脑上的时间，我怕别人通过修改本地时间来掩盖自己迟到的事实，所以我要去获取网络时间。

我的方法是 `get` 请求自己的服务器，在返回的头里面有时间信息（服务器的）。当然，你也可以百度一下时间 `api`，调用起来也会很方便的。
`js` 代码如下：

``` javascript
<script>
function gettime(){
ajax()
  function ajax(option){
    var xhr = null;
    if(window.XMLHttpRequest) {
      xhr = new window.XMLHttpRequest();
    } else { // ie
      xhr = new ActiveObject("Microsoft")
    }
    // 通过get的方式请求当前文件
    xhr.open("get","/");
    xhr.send(null);
    // 监听请求状态变化
    xhr.onreadystatechange = function(){
      var time = null,
          curDate = null;
      if(xhr.readyState===2) {
        // 获取响应头里的时间戳
        time = xhr.getResponseHeader("Date");
        curDate = new Date(time);
        //下面修改HTML
        document.getElementById("0").innerHTML = "时间：<input type='text' name='time' value='"+curDate.getHours()+":"+curDate.getMinutes()+"' readonly><br>";//拼接时间
        document.getElementById("0").innerHTML = "日期：<input type='text' name='filename' value='"+curDate.getFullYear()+(curDate.getMonth()+1)+curDate.getDate()+"' readonly><br>";//通过readonly阻止修改时间
      }
    }
  }
}
</script>
```

#### 页面代码

因为HTML是顺序加载的，所以我希望 `HTML` 加载完全后再读取`js`（否则 `js` 会找不到 `id` 为 `0` 的 `div`），所以我们写个 `onload`，为了刷新时间我在 `gettime` 函数里面利用 `setTimeout` 调用它自己实现定时（一分钟）刷新。HTML全文如下：

``` html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>考勤打卡</title>
    <script>
      function gettime() {
      ajax()
      function ajax(option) {
      var xhr = null;
        if(window.XMLHttpRequest) {
          xhr = new window.XMLHttpRequest();
        } else { // ie
          xhr = new ActiveObject("Microsoft")
        }
        // 通过get的方式请求当前文件
        xhr.open("get","/");//可以把/改成https://www.baidu.com/
        xhr.send(null);
        // 监听请求状态变化
        xhr.onreadystatechange = function() {
          var time = null,
            curDate = null;
            if(xhr.readyState===2){
              // 获取响应头里的时间戳
              time = xhr.getResponseHeader("Date");
              curDate = new Date(time);
              //下面修改HTML
              document.getElementById("0").innerHTML = "时间：<input type='text' name='time' value='"+curDate.getHours()+":"+cur.DategetMinutes()+"' readonly><br>";//拼接时间
              document.getElementById("1").innerHTML = "日期：<input type='text' name='filename' value='"+curDate.getFullYear()+(curDate.getMonth()+1)+curDate.getDate()+"' readonly><br>";//通过readonly阻止修改时间
            }
          }
        }
	      setTimeout(gettime,60000);
      }
    </script>
  </head>
  <body onload="gettime()">
	  <form action="process.php" method="post">
      姓名：<input type="text" name="name"><br>
		  <div id="0"></div>
      <div id="1"></div>
	  <input type="submit" value="打卡 ">
  </form> 
  </body>
</html>
```

很可惜在自己家电脑上面是无法完成测试的，

![CORS](https://img.cdn.gaein.cn/website_used/blog/Checker-Webpage-1/05.webp)

把html上传到服务器之后效果是这样的:

![效果](https://img.cdn.gaein.cn/website_used/blog/Checker-Webpage-1/06.webp)

虽然很丑，但是功能实现了，手动试一试确实无法修改时间、日期，而且时间也会每分钟刷新

### PHP页面

> 由于PHP较为奇特的语法，和跟没有一样的报错，坑了我很长时间。PHP中字符串用 `.` 拼接、变量无需声明前面加上 `$` 即可...建议先过一遍菜鸟教程或者w3school

#### 参数接收

PHP中接收到的POST的参数的值在 

``` php
$_POST["参数名"]
```

GET的在

``` php
$_GET["参数名"]
```

#### 参数处理

##### 文件名

``` php
$filename=$_POST["filename"]."txt";//拼接文件名
```

##### 姓名

``` php
$name=$_POST["name"];
```

##### 时间

``` php
$time = str_replace(":","",$_POST["time"]);//把时间里的“:”去除掉
if ((int)$time>730)//7:30之后迟到
{
  $name=$name."[迟到]";
}
else
{
  $name=$name."[准时]";
}
```

##### 写入文件

我使用的是函数 

``` php
file_put_contents(文件,内容,模式可选); 
```
文件是文件的路径（字符串），比如/root/test.txt；（没有则创建一个文件）

 > 注意：当无法创建且无法写入文件时，应该检查是否有权限。当能够创建但是无法写入文件时，应该检查首次写入的字符串是否为空，再检查后续代码是否有相关的操作覆盖了该文件，最后记得check一下你的磁盘空间（真的，我的硬盘满了，我检查了一天代码...）

内容是需要写入文件的内容；
模式是写入文件的模式，它包括：

* FILE_USE_INCLUDE_PATH //检查 *filename* 副本的内置路径?
* FILE_APPEND           //添加模式更改文件，向文件的末尾加入字符串
* LOCK_EX               //锁定文件

//如果模式为空则采用覆盖原内容的方式写入

它相对于 `fopen` 再 `write` 再 `close` 
来说更简洁，功能上几乎是一样的。
我们写入的文件要求内容格式大致如下：

```
张三[准时] | 7:25
李四[迟到] | 7:33
```

这样后续我们可以通过计算“|”的数量来得出打卡人数，通过“[迟到]”的数量来得出迟到人数（当然我并不是这么办的，刚想起来这么机智的办法）
那么下面我们来写入文件：

``` php
$str=$name." | ".$_POST["time"];
file_put_contents($filename,$str,FILE_APPEND);
echo "打卡成功";
```


那么PHP的全文是：

``` php
<?php 
$filename=$_POST["filename"]."txt";//拼接文件名
$name=$_POST["name"];
$time = str_replace(":","",$_POST["time"]);//把时间里的“:”去除掉
if ((int)$time>730)//7:30之后迟到
{
	  $name=$name."[迟到]";
}
else
{
	  $name=$name."[准时]";
}
$str=$name." | ".$_POST["time"]."\n";// \n的目的是在文件里面换行
file_put_contents($filename,$str,FILE_APPEND) or die("打卡失败");
echo "打卡成功";
?> 
```

让我们把前面的HTML代码保存到 `index.html`，上面的 `php` 保存到 `process.php`，上传到服务器试试吧！

输入姓名提交表单：

![表单](https://img.cdn.gaein.cn/website_used/blog/Checker-Webpage-1/06.webp)

POST到PHP，输出成功

![结果](https://img.cdn.gaein.cn/website_used/blog/Checker-Webpage-1/07.webp)

> 如果PHP不输出任何内容，可以考虑在第一行加入一个 echo "test"; ，如果仍然不输出任何内容，那么可能是你的代码有问题

打卡前文件列表：

![列表](https://img.cdn.gaein.cn/website_used/blog/Checker-Webpage-1/08.webp)

打卡后文件列表：

![列表2](https://img.cdn.gaein.cn/website_used/blog/Checker-Webpage-1/09.webp)

TXT内容：

![文件内容](https://img.cdn.gaein.cn/website_used/blog/Checker-Webpage-1/10.webp)

（一）完结
实在是太累了，其他功能在后续的（二）中说

代码自己copy，成品会在（二）p.s.也没准是（三）中放出来

如果你觉得有用或者有需要补充之处不妨评论一下
