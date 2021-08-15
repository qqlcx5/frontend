# 必备的CSS小技巧

积累了几个有趣的CSS小技巧。

## 清除默认样式

### reset.css

```
html,body,h1,h2,h3,h4,h5,h6,div,dl,dt,dd,ul,ol,li,p,blockquote,pre,hr,figure,table,caption,th,td,form,fieldset,legend,input,button,textarea,menu{margin:0;padding:0;}
header,footer,section,article,aside,nav,hgroup,address,figure,figcaption,menu,details{display:block;}
table{border-collapse:collapse;border-spacing:0;}
caption,th{text-align:left;font-weight:normal;}
html,body,fieldset,img,iframe,abbr{border:0;}
i,cite,em,var,address,dfn{font-style:normal;}
[hidefocus],summary{outline:0;}
li{list-style:none;}
h1,h2,h3,h4,h5,h6,small{font-size:100%;}
sup,sub{font-size:83%;}
pre,code,kbd,samp{font-family:inherit;}
q:before,q:after{content:none;}
textarea{overflow:auto;resize:none;}
label,summary{cursor:default;}
a,button{cursor:pointer;}
h1,h2,h3,h4,h5,h6,em,strong,b{font-weight:bold;}
del,ins,u,s,a,a:hover{text-decoration:none;}
body,textarea,input,button,select,keygen,legend{font:12px/1.14 arial,\5b8b\4f53;color:#333;outline:0;}
body{background:#fff;}
a,a:hover{color:#333;}
```

### 清除移动端 input 样式

```
border: none;
-moz-appearance:none;
-webkit-appearance: none;
border-radius: 0;    
outline:medium; 
background-color: transparent;
```

### 去除Chrome中input自动填充背景颜色

```
input:-webkit-autofill,
input:-webkit-autofill:hover,
input:-webkit-autofill:focus {
  box-shadow: 0 0 0px 1000px white inset;
}

// 选中自动填充后字体颜色并不会改变，需要添加以下代码
input {
    -webkit-text-fill-color: #fff;  //设计稿的色
}
```

### 清除移动端a标签变色

```
-webkit-tap-highlight-color: rgba(255, 255, 255, 0);
-webkit-user-select: none;
-moz-user-focus: none;
-moz-user-select: none;
```

### 清除input[type=number]的默认样式

```
input[type=number] {
  -moz-appearance: textfield;
}

input[type=number]::-webkit-inner-spin-button,
input[type=number]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
```

### 解决ios滑动滚动条卡顿

```
html, body {
    -webkit-overflow-scrolling: touch; 
}
```

### 硬件加速
有时候动画可能会导致用户的电脑卡顿，你可以在特定元素中使用硬件加速来避免这个问题：

```
.block {
    transform: translateZ(0);
}
```
## @media 查询

可以针对不同的媒体类型定义不同的样式。

```
/* 横屏 */
@media screen and (orientation:landscape){
     
}
/* 竖屏 */
@media screen and (orientation:portrait){
     
}
/* 窗口宽度<960,设计宽度=768 */
@media screen and (max-width:959px){
     
}
/* 窗口宽度<768,设计宽度=640 */
@media screen and (max-width:767px){
     
}
/* 窗口宽度<640,设计宽度=480 */
@media screen and (max-width:639px){
     
}
/* 窗口宽度<480,设计宽度=320 */
@media screen and (max-width:479px){
     
}
/* 设备像素比为2 */
/* 常用于1px边框，还应规定 3dppx 的情况 */
@media (min-resolution: 2dppx) {

}
```

## REM 设置

### 方式一 JS
```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" id="WebViewport" content="initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no" />
        <title>Document</title>
        <style type="text/css"></style>
    </head>
    <body>
        <script type="text/javascript">
            function resize() {
                let width = screen.width > 750 ? '100px' : screen.width / 10 + 'px'
                document.getElementsByTagName('html')[0].style.fontSize = width
            }
            window.onresize = resize
            
        </script>
    </body>
</html>
```

### 方式二 CSS

```
html {
    font-size: calc(100vw / 7.5);
}
```

## CSS生成一个三角形

```
// 普通三角形
.triangle {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 50px 0 50px 100px;
  border-color: transparent transparent transparent #00adb5;
}

// 直角三角形
.triangle {
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 100px 100px 0 0;
  border-color: #00adb5 transparent transparent transparent;
}
```

## 文字省略

当文本内容长度超过容器最大宽度时，自动省略多余的文本。

```html
<!-- 最多显示一行 -->
<div class="c-ellipsis">这是一段最多显示一行的文字，多余的内容会被省略</div>

<!-- 最多显示两行 -->
<div class="c-ellipsis-2">
  这是一段最多显示两行的文字，多余的内容会被省略
</div>

<!-- 最多显示三行 -->
<div class="c-ellipsis-3">
  这是一段最多显示三行的文字，多余的内容会被省略
</div>
```

```scss

.c-ellipsis,
.c-ellipsis-1 {
    @include ellipsis();
}

.c-ellipsis-2 {
    @include ellipsis(2);
}

.c-ellipsis-3 {
    @include ellipsis(3);
}

@mixin ellipsis($column: 1) {
	overflow: hidden;
	-o-text-overflow: ellipsis;
	text-overflow: ellipsis;
	@if $column == 1 {
		white-space: nowrap;
	} @else {
	    display: -webkit-box;
	    white-space: normal !important;
	    word-wrap: break-word;
	    -webkit-line-clamp: $column;
	    -webkit-box-orient: vertical
	}
}
```

## 1px 下划线


```
.c-underline,
.c-underline__top,
.c-modal__title {
    position: relative;
}

.c-underline::after,
.c-underline__top::after {
    content: "";
    position: absolute;
    right: 0;
    bottom: 0;
    left: 0;
    z-index: 2;
    height: 1px;
    transform: scaleY(0.5);
    background-color: #ededed;
}

.c-underline__top:after {
    top: 0;
    bottom: auto;
}
```

## 动画

### 淡入
```
@keyframes fadeIn {
  0% {
    opacity: 0
  }
  100% {
    opacity: 1
  }
}

.fadeIn {
  animation-name: fadeIn;
}
```

### 淡出

```
@keyframes fadeOut {
  0% {
    opacity: 1
  }
  100% {
    opacity: 0
  }
}
.fadeOut {
  animation-name: fadeOut;
}
```

### 旋转

```
@keyframes c-spin {
    0% {
        transform: rotate(0deg);
    }
    100% {
        transform: rotate(360deg);
    }
}

.c-spin{
    animation-name: c-spin;
}
```

## 间距简写

在0-80以内，是4的倍数或者是12的倍数。

```html
<!--内边距24像素-->
<div class="p24"></div>

<!--外边距向左12像素-->
<div class="ml12"></div>
ptb 上下内边距
plr 左右内边距
p t b l r上下左右边距
```

```scss
// 定义内外边距，历遍1-80
@for $i from 0 through 80 {
    // 只要能被4除尽的数或者12的倍数
    @if $i % 4 == 0 or $i % 12 == 0 {
        .hr#{$i} {
            height: $i + rpx;
            background-color: #f5f5f5;
            display: block;
        }
        // ptb30 plr30 p24
        .ptb#{$i} {
            padding-top: $i + rpx !important;
            padding-bottom: $i + rpx !important;
        }
        .plr#{$i} {
            padding-left: $i + rpx !important;
            padding-right: $i + rpx !important;
        }
        .p#{$i} {
            padding: $i + rpx !important;
        }

        @each $short, $long in l left, t top, r right, b bottom {
            // 缩写版，ml30
            .m#{$short}#{$i} {
                margin-#{$long}: $i + rpx !important;
            }
            .p#{$short}#{$i} {
                padding-#{$long}: $i + rpx !important;
            }
        }
    }
}
```


### 参考文献

- [CSS命名约定将节省您的调试时间](https://medium.com/free-code-camp/css-naming-conventions-that-will-save-you-hours-of-debugging-35cea737d849)
- [22-essential-css-recipes](http://ipestov.com/22-essential-css-recipes/)
- [vant](https://vant-contrib.gitee.io/vant/#/zh-CN/)