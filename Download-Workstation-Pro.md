---
title: 优雅的从 VMWare 官网上下载完整版 Workstation Pro
author: Gaein nidb
categories:
  - 电脑软件
tags:
  - VMWare
  - 软件教程
date: 2019-01-23 14:46:35
---

VMWare是世界闻名的虚拟机软件，那么如何在官网下载正版呢？

## 打开VMWare官网

[VMware中国](https://www.vmware.com/cn.html)

![首页](https://img.cdn.gaein.cn/website_used/blog/Download-Workstation-Pro/01.webp)
## 转到下载页面

点击左侧下载>产品下载>Workstation Pro

**！注意，是“产品下载”中的Workstation Pro而不是“免费产品试用版和演示”中的**

![下载](https://img.cdn.gaein.cn/website_used/blog/Download-Workstation-Pro/02.webp)

## 跳转到软件下载

跳转后在页面中选择自己需要的，跳转至下载

![下载详细页面](https://img.cdn.gaein.cn/website_used/blog/Download-Workstation-Pro/03.webp)

## 点击“了解更多信息”

![更多信息](https://img.cdn.gaein.cn/website_used/blog/Download-Workstation-Pro/04.webp)
## 记下“内部版本号”

![版本号](https://img.cdn.gaein.cn/website_used/blog/Download-Workstation-Pro/05.webp)

## 拼接链接：

`"https://download3.vmware.com/software/wkst/file/VMware-workstation-full-"+版本（15.0.2）+"-"+内部版本号（10952284）+".exe"`

比如我这个15.0.2,10952284的下载链接就是
https://download3.vmware.com/software/wkst/file/VMware-workstation-full-15.0.2-10952284.exe

## 脚本

> 2021.5.15 补充

前阵子无聊扒了一下VMWare的API，瞎写了一个屎山脚本，用于实验室内网资源站更新VMWare方便一些：

```python
# import requests
import httpx as requests
import json

vmware_public_api_url = "https://my.vmware.com/channel/public/api/v1.0"


def get_latest_version() -> str:
    vmware_all_url = vmware_public_api_url + "/products/getProductsAtoZ?locale=en_US&isPrivate=false"
    print(f"[INFO]向{vmware_all_url}发送请求，请稍后...")
    all_file_resp = requests.get(vmware_all_url)
    latest_rev = ""
    if all_file_resp.status_code == 200:
        ret_json = json.loads(all_file_resp.text)
        products_list = ret_json["productCategoryList"][0]["productList"]

        for item in products_list:
            if item["name"] == "VMware Workstation Pro":
                latest_rev = item["actions"][0]["target"].split('/')[-1]

    return latest_rev


def get_info_by_version(version: str) -> dict:
    vmware_list_url = vmware_public_api_url + f"/products/getRelatedDLGList?category=desktop_end_user_computing&product=vmware_workstation_pro&version={version}&dlgType=PRODUCT_BINARY"

    print(f"[INFO]向{vmware_list_url}发送请求，请稍后...")

    resp = requests.get(vmware_list_url)
    if resp.status_code == 200:
        return json.loads(resp.text)["dlgEditionsLists"][0]["dlgList"][0]
    else:
        print(f"ERROR:{resp.status_code} at url {vmware_list_url}")


def get_file_by_id(product_id: str, dl_group: str) -> str:

    vmware_file_url = vmware_public_api_url + f"/dlg/details?locale=en_US&downloadGroup={dl_group}&productId={product_id}"
    print(f"[INFO]向{vmware_file_url}发送请求，请稍后...")

    resp = requests.get(vmware_file_url)
    if resp.status_code == 200:
        return json.loads(resp.text)["downloadFiles"][0]


if __name__ == "__main__":
    latest_version = get_latest_version()

    if latest_version is not None:
        print(f"[INFO]获取到最新版本{latest_version}")

        vmware_info = get_info_by_version(latest_version)

        if vmware_info is not None:
            print(f"[INFO]获取到：ID {vmware_info['productId']},名称 {vmware_info['name']},发布时间 {vmware_info['releaseDate']}")

            file_info = get_file_by_id(vmware_info['productId'], vmware_info['code'])

            print(f"[INFO]获取到：文件名 {file_info['fileName']},MD5{file_info['md5checksum']},文件大小{file_info['fileSize']}")

            download_url = f"https://download3.vmware.com/software/wkst/file/{file_info['fileName']}"

            print(f"[INFO]拼接下载链接：\n{download_url}")

```

屎山（但是能跑）