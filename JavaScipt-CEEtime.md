---
title: 'javascript制作高考倒计时网页'
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - 网站建设
  - 编程
  - 天天向上
cover: 'https://s2.ax1x.com/2019/12/01/QZ78S0.md.png'
date: 2019-12-1 09:54:14
---
暑假的时候做了一个高考倒计时兼目标的页面，由于高三实在是忙，一直没有时间写成博客
<!-- more -->
> 网上有很多javascript实现倒计时的代码，但是我找到的都不是很好用，或者有bug，所以自己绞尽没有脑子的汁写了一个，算法智障还请多多包涵。

## [一]原理
其实原理非常简单，就是用高考开始的时间（河北省是6月7日9：00语文第一场开考）减去当前的时间（javascript函数获取的用户电脑时间），然后换算成天、时、分、秒，再舍去小数。每一秒钟调用一次自己，就实现了动态更新。
## [二]代码

```
<script language="javascript" type="text/javascript">
	function timerun(){
		var now=new Date();
		var end=new Date('2020/06/07 09:00:00');
		var day=Math.floor((end-now)/(24000*3600));
		if(day>=0){
			document.getElementById('timeShowDay').innerHTML=day;
			var hour=Math.floor(((end-now)-(day*24000*3600))/(1000*3600));
			document.getElementById('timeShowHour').innerHTML=hour;
			var min=Math.floor(((end-now)-(day*24000*3600)-(hour*1000*3600))/(1000*60));
			document.getElementById('timeShowMin').innerHTML=min;var sec=Math.floor(((end-now)-(day*24000*3600)-(hour*1000*3600)-(min*1000*60))/1000);
			document.getElementById('timeShowSec').innerHTML=sec;
		}
		else{
			document.getElementById('timeShow').innerHTML="高考已经开始";
		}
		setTimeout('timerun()',1000)
	};
</script>
```
## [三]效果
见 https://www.gaein.cn/live-for-dream/
或标题图
