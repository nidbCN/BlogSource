---
title: 玉田公交线路图
author: Gaein nidb
tags: 
  - 公交
  - 生活
categories: [杂记]
date: 2023-02-11 15:10:18
---

## 玉田公交线路图

### 概览

{% raw %}

<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/viewerjs/1.11.2/viewer.min.css" integrity="sha512-9EosEckNJFma9X2uo5ysGPhVf/dcZTuZUBVW2A9QcWBd0HAx6zs+FK+wsBGhl91uFfDI4ZY+/7MVhtYU4tXEig==" crossorigin="anonymous" referrerpolicy="no-referrer" />
<script src="https://cdnjs.cloudflare.com/ajax/libs/viewerjs/1.11.2/viewer.min.js" integrity="sha512-1TCjsgfYd9edJ4mO6sb8rLzhnGpnFR4GazDGVhDekHrOHU7y7vcqGiO+4yW0HIDBoIY/ocbM/BrXxg8dYO6wSQ==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>

<img style="display: none;" id="image" src="https://img.cdn.gaein.cn/website_used/yutian-map-viewer/overview.svg" alt="玉田公交线路图" />

<script>
    const viewer = new Viewer(document.getElementById('image'), {
        navbar: false,
        slideOnTouch: false,
        title: () => "玉田公交线路图",
        toolbar: {
            zoomIn: true,
            zoomOut: true,
            oneToOne: true
        },
        viewed() {
            viewer.moveTo(-512, 320);
            viewer.zoomTo(1.5);
        },
    });
</script>

{% endraw %}

> 点击图片进入查看器

> [独立页面的图片查看器](https://static.cdn.gaein.cn/yutian-bus-map-viewer/)

### 附

使用 railmapgen.github.io 绘制，转载请附上本文链接。
