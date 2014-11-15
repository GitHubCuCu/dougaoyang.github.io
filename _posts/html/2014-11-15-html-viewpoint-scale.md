---
layout: post
title: html移动端自适应宽度
category: HTML/HTML5
description: 自动适应移动端网页宽度，并控制缩放比例
keywords: html, 移动端, 自适应, viewpoint
---

##添加元标签
wap2.0网页的head里加入下面这条元标签，在iPhone的浏览器中页面将以原始大小显示，并不允许缩放。

> <meta name="viewport" content="width=device-width,minimum-scale=1.0,maximum-scale=1.0;user-scalable=0;" />

##参数说明

width - viewport的宽度 

height - viewport的高度

initial-scale - 初始的缩放比例

minimum-scale - 允许用户缩放到的最小比例

maximum-scale - 允许用户缩放到的最大比例

user-scalable - 用户是否可以手动缩放