# panzoom-fake

## 项目简介

这是一个基于[原panzoom项目](https://github.com/anvaka/panzoom.git)的修改版本。修改并添加了一些内容，使其更适用于我的项目。

## 主要修改(**_鼠标操作未改_**)

+ 修复问题：  
  + 边界限制：原项目边界传入{top, right, bottom, left}或{bounds: true, boundPadding: 1}时并不能正常工作（也不知道是不是我的问题，即使我按文档示例配置也这样）。
  + 事件触发时机：主要修改了zoomend的事件触发时机。
  + 双击缩放：将双击缩放限制在 1 与 允许缩放平均值之间。
+ 添加：
  + 添加了双击缩放控制变量：可以根据需要来随时启动或关闭。
  + 添加了两个事件：双击后缩放变为1，以及双击后缩放变为允许缩放平均值。
+ 其他一点修改

## 安装

在public文件夹下下载panzoom.umd.js后。
```html
<script src="panzoom.umd.js"></script>
```

## 使用

示例代码参考“/src/components/PanZoom.vue”。

## API文档

基本api与[panzoom](https://github.com/anvaka/panzoom.git)一致。  
以下是修改过后的：
+ options:
  + bounds: 格式为{top, right, bottom, left} 或 Boole。一般为{top: 0, right: 容器宽度, bottom: 容器高度, left: 0}。
  + enableDoubleClick： 开启双击缩放功能。不传则没有边界限制。
+ on: 
  + zoomMin: 双击图片缩小到1后触发。
  + zoomMean：双击图片放大到(maxZoom + minZoom) / 2后触发。
+ api:
  + openEnableDoubleClick: 参数true or false。开启或关闭双击缩放。
## 效果演示

<img src="/public/show.gif" alt=""/>

## 已知问题

1. 双击缩放：电脑浏览器鼠标双击以及ionic app应用双击没有问题。手机浏览器双击则无效果。手机浏览器好像要将zoomDoubleClickSpeed设置为不为1的数，但是效果则不是我修改后的效果。