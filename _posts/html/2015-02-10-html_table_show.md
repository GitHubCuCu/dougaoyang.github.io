---
layout: post
title: 表格在移动端自适应的一种方法
category: HTML/HTML5
description: 解决表格在窄视口设备（移动端）上显示困难的一种方法。
keywords: html, 表格, 移动端, 自适应, viewpoint
---

`PS: 下文所说的窄距设备可以泛指移动端设备`

在窄距设备中，表格很难搞，水平方向上没有足够的空间来舒适地摆放元素。因此可以在窄距设备上将表格变成两行，将一行的行头和单元格变成列。

下面来实现这个功能

首先一个普通的表格

```HTML
<table>
  <thead>
    <tr>
      <th>Country</th>
      <th>Desktop share</th>
      <th>Tablet share</th>
      <th>Mobile share</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td data-th="Country">India</td>
      <td data-th="Desktop share">32%</td>
      <td data-th="Table share">1%</td>
      <td data-th="Mobile share">67%</td>
    </tr>
    <tr>
      <td data-th="Country">GB</td>
      <td data-th="Desktop share">69%</td>
      <td data-th="Table share">13%</td>
      <td data-th="Mobile share">18%</td>
    </tr>
    <tr>
      <td data-th="Country">US</td>
      <td data-th="Desktop share">69%</td>
      <td data-th="Table share">9%</td>
      <td data-th="Mobile share">22%</td>
    </tr>
    <tr>
      <td data-th="Country">China</td>
      <td data-th="Desktop share">86%</td>
      <td data-th="Table share">4%</td>
      <td data-th="Mobile share">10%</td>
    </tr>
  </tbody>
</table>
```

它在窄距设备是这样展示的

![普通情况下表格样式][img:1]

下面利用css3的媒体查询功能来实现表格的自适应。

这里我们设置的端点是`600px`。

```CSS
@media (max-width: 600px) {
    table thead {
        display: none;
    }
    table td {
        display: block;
        position: relative;
        padding-left: 50%;
        padding-top: 13px;
        padding-bottom: 13px;
        text-align: left;
    }

    table td:before {
        content: attr(data-th) " :";
        display: inline-block;
        position: absolute;
        top: 0;
        left: 0;
        bottom: 0;
        width: 33%;
        max-height: 100%;
    }
}
```

它在窄距设备是这样展示的

![修改后表格样式][img:2]

这样就比较完整的展示的表格的内容。

当然这只是表格自适应的其中一种办法，还有更多的办法哦O(∩_∩)O~


[img:1]: ../../images/20150210144726.png "普通情况下表格样式"
[img:2]: ../../images/20150210150306.png "修改后表格样式"
