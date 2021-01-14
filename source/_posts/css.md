---
title: css
date: 2017-07-26 09:38:21
tags:
  - css
---

###1. CSS text-decoration 属性
none	默认。定义标准的文本。
underline	定义文本下的一条线。
overline	定义文本上的一条线。
line-through	定义穿过文本下的一条线。
blink	定义闪烁的文本。
inherit	规定应该从父元素继承 text-decoration 属性的值。

<!-- more -->

###2. CSS 中的百分比
####2.1 width height
参照物是元素包含块，width参照包含块的宽度，height参照包含物的高度。

####2.2 margin padding
参照物是元素包含块，任意方向参照都是包含块的**宽度**。这个比较特殊！

####2.3 border-radius
参照物是元素自身， 宽参照包含块的宽度，高参照包含物的高度

####2.4 background-position
参照物是（元素包含块尺寸-背景图尺寸）, left 50% 水平居中， top 50% 垂直居中。

####2.5 fontSize
参照是直接父元素的font-size。例如，一个元素的直接父元素的font-size是14px，无论这个是直接定义的，还是继承得到的，当该元素定义font-size:100%;，获得的效果就是font-size:14px;。

####2.6 line-height
参照是元素自身的font-size。例如，一个元素的font-size是12px，那么line-height:150%;的效果是line-height:18px;。

####2.7 vertical-aligh
参照是元素自身的line-height。例如，一个元素的line-height是30px，则vertical-align:10%;的效果是vertical-align:3px;。

####2.8 bottom、left、right、top
参照是元素的包含块（第一个非static）。left和right是参照包含块的宽度，bottom和top是参照包含块的高度。

####2.9 transform: translate
参照是变换的边界框的尺寸（等于这个元素自己的border-box尺寸）。例如，一个宽度为150px，高度为100px的元素，定义transform:translate(50%, 50%)的效果是transform:translate(75px, 50px);translate3d对应是还有第三个维度的，但是，经过测试，最后的第3个值不可以使用百分比（否则样式定义无效）。就是没有三维变换， 还是使用translate3d，这个GPU优化过，比translate高效。

####2.10 百分比值的继承
当百分比值用于可继承属性时，只有结合参照值计算后的绝对值会被继承，而不是百分比值本身。例如，一个元素的font-size是14px，并定义了line-height:150%;，那么该元素的下一级子元素继承到的line-height就是21px，而不会再和子元素自己的font-size有关。

### 3. transition animation transform 
1.transition, animation 都是动画范畴。
transform是静态概念(含有translate,translate3d)，和top,left, width,height等这些一样。

2.transition是缓动动画，animation是幁动画。

### 4. marginTop top
marginTop计算盒模型时，看边框的位置。relative下top计算盒模型时，先计算不含top的盒模型，再做偏移，原位置空间还是被占用。

### 5. CSS的覆盖顺序
如果同一个标签的style属性被不同样式赋值，如width:
- 不同级别的优先级: 标签属性样式 > 内联样式 > 外联样式
- 同级别，按出现的先后顺序
- 更精确的查找覆盖其它查找，如id覆盖类，如联合查找覆盖单独的查找
- 加上!important，这个覆盖所有其它的，以这个为准