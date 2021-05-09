---
title: ASCII码对照表和转换器
author: Gaein nidb
categories:
  - 学习资料
tags:
  - 笔记
  - 编程
cover: 'https://s1.ax1x.com/2020/09/22/wOiUT1.md.png'
date: 2020-09-22 14:05:19
---

ASCII 是一种计算机的编码系统，编码了数字、拉丁字母和一些控制符。

<!--MORE-->

## 简介

> ASCII 是最通用的信息交换标准，并等同于国际标准 ISO/IEC 646。ASCII 第一次以规范标准的类型发表是在 1967 年，最后一次更新则是在 1986 年，到目前为止共定义了 128 个字符（一个 char）

## 内容

下面是完整的 ASCII 表，0 ～ 31 及 127(共 33 个)是控制字符或通信专用字符，其余为可显示字符

![ASCII](https://s1.ax1x.com/2020/09/22/wLWF2Q.png)

#### 下载

[ASCII-Table.xlsx|18KB](https://static.cdn.gaein.cn/files/ASCII-Table/ASCII-Table.xlsx)

[ASCII-Table.pdf|138KB](https://static.cdn.gaein.cn/files/ASCII-Table/ASCII-Table.pdf)

---

## 转换

---

#### 将字符转换为 ASCII 十进制码

---

<link rel="stylesheet" href="https://static.cdn.gaein.cn/Layui/css/layui.css">

<div class="layui-form">
    <div class="layui-form-item">
        <label class="layui-form-label">输入</label>
        <div class="layui-input-inline">
            <input type="text" id="char" placeholder="请输入字符" class="layui-input">
        </div>
    </div>
    <div class="layui-form-item">
        <label class="layui-form-label">操作</label>
        <div class="layui-input-inline">
            <button class="layui-btn layui-btn-lg layui-btn-warm" onclick="charToNum()">转换</button>
            <button type="reset" class="layui-btn layui-btn-lg">清空</button>
        </div>
    </div>
    <div class="layui-form-item">
        <label class="layui-form-label">结果</label>
        <div class="layui-input-inline">
            <div id="numResult" class="layui-bg-blue" style="width:190px;height:35px;text-align:center"> 转换结果
            </div>
        </div>
    </div>
</div>
<br/ >

---

#### 将十进制码转换为 ASCII 字符

---

<div class="layui-form">
    <div class="layui-form-item">
        <label class="layui-form-label">输入</label>
        <div class="layui-input-inline">
            <input type="text" id="num" placeholder="请输入数字" class="layui-input">
        </div>
    </div>
    <div class="layui-form-item">
        <label class="layui-form-label">操作</label>
        <div class="layui-input-inline">
            <button class="layui-btn layui-btn-normal layui-btn-lg" onclick="numToChar()">转换</button>
            <button type="reset" class="layui-btn layui-btn-lg">清空</button>
        </div>
    </div>
    <div class="layui-form-item">
        <label class="layui-form-label">结果</label>
        <div class="layui-input-inline">
            <div id="charResult" class="layui-bg-orange" style="width:190px;height:35px;text-align:center"> 转换结果
            </div>
        </div>
    </div>
</div>
<br />

<script>
    function numToChar() {
        var input = document.getElementById("num").value;
        if (input >= 40 && input <= 126){
            var result = String.fromCharCode(input);
            document.getElementById("charResult").innerHTML = input + "对应的字符是:\"" + result + "\"";
        }
        else
        {
            document.getElementById("charResult").innerHTML =  input + "超出有效范围";
        }
    }
    
    function charToNum() {
        var input = document.getElementById("char").value;
        input = input.substring(0,1);
        var result = input.charCodeAt();
        document.getElementById("numResult").innerHTML = "\"" + input + "\"对应的数字是:" + result;
    }
</script>