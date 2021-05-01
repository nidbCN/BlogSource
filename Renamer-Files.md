---
title: 'C#批量重命名图片为指定格式'
author: Gaein nidb
categories:
- 代码如诗
tags:
- CSharp
- dotNET
- 编程
- 工具
date: 2018-11-04 14:11:09
---
最近社里面交图片，要求格式为 姓名-编号
作为一个懒癌晚期患者，我默默打开了visual studio
下面是代码，原理是获取文件名，保存到数组中，再循环重命名到最后一个
<!-- More -->
最近社里面交图片，要求格式为 姓名-编号 
作为一个懒癌晚期患者，我默默打开了visual studio
下面是代码，原理是获取文件名，保存到数组中，再循环重命名到最后一个

> 2021年2月补充
> （我2年前写的什么玩意是）重命名采用MoveTo方法就行了，因为我联想到Linux下重命名也是mv...

重命名采用的方法是引用VB.NET里面的重命名
``` cs

using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Forms;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Navigation;
using System.Windows.Shapes;
using Microsoft.VisualBasic.Devices;

namespace Rename
{
    /// <summary>
    /// MainWindow.xaml 的交互逻辑
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
            Output.Text = "窗口初始化完成";
        }

        private void P_Click(object sender, RoutedEventArgs e)
        {
            Output.Text = "初始化..";
            string path = Bin.Text;
     
            string[] files=new string[10000];
            string name = Name.Text;
            int i = 0;
            Output.Text = "初始化变量成功";

           
            DirectoryInfo folder = new DirectoryInfo(@path);
                while (i < folder.GetFiles("*.jpg").Count())
                 {
                files[i] = folder.GetFiles("*.jpg")[i].Name;
                i++;
                 }
            Output.Text = "文件获取成功，共"+i+"个";
            int s = 0;
            while (s <i)
            {

                Computer MyComputer = new Computer();
                MyComputer.FileSystem.RenameFile(path+@"\"+files[s],name+"-"+ Convert.ToString(s+1)+ ".jpg");
                Output.Text = "正在转换，" + s+"/"+i + "个";
                s++;
            }
            Output.Text = "转换完成！";
        }

        private void Where_Click(object sender, RoutedEventArgs e)
        {
            Output.Text = "正在浏览文件夹";
                try
                {
                    FolderBrowserDialog dialog = new FolderBrowserDialog();
                    dialog.Description = "请选择文件路径";
                    System.Windows.Forms.DialogResult result = dialog.ShowDialog();
                    if (result == System.Windows.Forms.DialogResult.Cancel)
                    {
                        return;
                    }
                    string folderPath = dialog.SelectedPath.Trim();
                    DirectoryInfo theFolder = new DirectoryInfo(folderPath);
                    if (theFolder.Exists)
                    {
                        Bin.Text = folderPath;
                        return;
                    }
                }
            finally { }
            Output.Text = "完成";
        }
    }
}


```
