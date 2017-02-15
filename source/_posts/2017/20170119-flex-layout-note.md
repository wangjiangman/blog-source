title: "Flex 布局学习笔记"
date: 2017-01-19
tags: [CSS, flexbox]
categories: 技术
---

Flex 布局，也称为弹性布局，是为了适应不同的屏幕尺寸和不同的设备类型二提出的一种布局方式。<!--more-->

## 基本概念

![弹性布局](/images/flexbox.png)

上图是一个横向的，文本流从左到右的弹性布局图示。这里有几个概念

- 弹性容器(Flex container), 指一个声明为 flex 或者 inline-flex 的元素，是所有 Flex items 的父元素。
- 弹性项目(Flex item), 指 flex 容器内的直接孩子元素；弹性容器中的直接文本项默认为匿名弹性项目。
- 主轴(main axis)和侧轴(cross axis), 类似于二维中的 X 轴和 Y 轴，主轴由属性 flex-direction 来定义，与主轴垂直的轴则是侧轴。


## 定义弹性布局


要使用弹性布局，需要定义一个弹性容器（Flex container），通过声明 display 属性为 flex 或者 inline-flex 来将此元素定义为弹性容器。

```css
.flex {
	display: flex 
}
```

## 弹性容器的属性

- `flex-direction` 定义排列的方向
  - `row` *默认值*，主轴为水平，从左向右排列
  - `row-reverse` 主轴为水平，从右向左排列
  - `column` 主轴为垂直方向，从上向下排列
  - `column-reverse` 主轴为垂直方向，从下向上排列
- `flex-wrap` 定义是否新行显示，以及新行的排列方向
  - `nowrap` *默认值*，显示在同一行，不换行
  - `wrap` 一行显示不了时使用新行显示
  - `wrap-reverse` 同 `wrap`，但是从下向上显示
- `flex-flow` 是 `flex-direction` 和 `flex-wrap` 的简写方式
  - 使用方法 `flex-flow: <‘flex-direction’> || <‘flex-wrap’>`
  - 默认值为： `row nowrap`
- `justify-content` 定义弹性项目在主轴上的对齐方式
  - `flex-start` *默认值*，从起始位置对齐，通常为左对齐
  - `flex-end` 从结束位置对齐，通常为右对齐
  - `center` 居中对齐
  - `space-between` 两端对齐，平均间隔
  - `space-around` 每个子元素都有相等的外边距，相邻元素外边距不会叠加
- `align-items` 定义弹性项目在侧轴上的对齐方式
  - `stretch` *默认值*，弹性项目在没指定高度或者高度为 `auto` 的情况下，则高度会占满空间
  - `flex-start` 侧轴起点开始对齐
  - `flex-end` 从侧轴结束位置对齐
  - `center` 相对侧轴居中对齐
  - `baseline` 与基线对齐
- `align-content` 定义多行在侧轴的对齐方式，当只有一行时，不起作用
  - `stretch` *默认值*，各行会伸展以占满整个纵轴空间
  - `flex-start` 对齐到纵轴起点
  - `flex-end` 对齐到纵轴终点
  - `center` 相对纵轴中间对齐
  - `space-between` 各行相对纵轴两端对齐，各行间隔相等
  - `space-around` 各行都有相等的外边距，各行的外边距不会叠加

## 弹性项目的属性

- `flex-grow` 数字，定义弹性项目的放大比例，默认 0
- `flex-shrink` 数字，定义弹性项目的缩小比例，默认 1，值 0 表示不缩小
- `flex-basis` 数字，定义弹性项目的默认尺寸，
- `flex` 是 `flex-grow` `flex-shrink` `flex-basis` 的缩写，默认值为 `0 1 auto`
- `align-self` 定义此弹性项目本身的对齐方式，会覆盖弹性容器 `align-items` 定义的对齐方式
  - `auto` 从弹性容器继承
  - `stretch` 弹性项目在没指定高度或者高度为 `auto` 的情况下，则高度会占满空间
  - `flex-start` 侧轴起点开始对齐
  - `flex-end` 从侧轴结束位置对齐
  - `center` 相对侧轴居中对齐
  - `baseline` 与基线对齐
- `order` 数字，定义弹性项目的显示顺序，数字越小越靠前

**参考**

- https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_CSS_flexible_boxes
- https://www.w3cplus.com/css3/a-guide-to-flexbox-new.html