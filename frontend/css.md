# css 层叠样式表

有三种形式

1. 





## css选择器

1. class

全局属性

```html
<style type="text/css">
    .classname {

	}
</style>
<!-- class 与 id 功能类似 -->
```

2. id

全局属性

```html
<style type="text/css">
    #idname {

	}
</style>
<!-- classname 可以相同，idname 不能相同 -->
<!-- id 是唯一的 -->
```

3. 属性选择器

```html
<style type="text/css">
    [href] {
        
    }
    [href="index.html"] {
        
    }
</style>
```

4. 冒号选择器

```html
<style type="text/css">
    a:hover {
        
    }
</style>
<!-- 冒号后面有很多属性 -->
```

5. 选择所有元素

```html
<style type="text/css">
    * {
        
    }
</style>
```

6. 根据类型选择

```html
<style type="text/css">
    a {
        
    }
</style>
```

## 控制边框和背景

```html
<style type="text/css">
    a {
        border-width: 8px;
        border-color: black;
        border-style: solid;
        border-top-color: white;
        
    }
    p {
        width: 500px;
        height: 600px;
        border: 5px solid red;
        border-top: 10px dashed yellow;
        background-color: blue;
        background-image: url();
    }
</style>
<!-- border-style 有很多属性 -->
```

```html
<style type="text/css">
	body {
        background-attachment: fixed;
		background-image: url();
		backgound-repeat: no-repeat;
        background-size: auto;
        
	}
    
</style>
<!-- background-size:auto/contain/cover -->
```
```html
<style type="text/css">
    p {
        width: 50px;
        height: 50px;
        border: 1px solid black;
        background-color: antiquewhite;
        border-radius: 10px / 15px;
    }
</style>
```
## 文本样式

```html
<style type="text/css">
    p {
        text-align: center;
        direction: ltr;
        letter-spacing: 10px
		word-spacing: 10px
		line-height: 10px
		text-indent: 50px
        text-decoration: underline;
        text-transform: capitalize;  
    }
</style>
<!-- 
	ltr: left to right
	letter-spacing: 字间距
	word-spacing: 单词间距（中文不能用）
	line-height: 行间距
	text-indent: 首行缩进
	text-decoration: 装饰  underline/overline/line-through
-->
```

```html
<style type="text/css">
    p {
        font-family: 微软雅黑;
        font-size: 40px;
        font-style: italic;
        font-variant: small-caps;
        font-weight: 900;
        text-shadow: 10px 10px 5px red
    }
</style>
<!-- 
	font-size: italic/oblique/inherit
	text-shadow: 水平偏移 竖直偏移 模糊程度 阴影颜色
	font-variant: small-caps 小型大写
	font-weight: 100 - 900 的整百
-->

```

## 过渡

```html
<style type="text/css">
    p {
        width: 100px;
        height: 100px;
        background-color: white;
    }
    p:hover {
        width: 200px;
        height: 200px;
        background-color: blue;
        transition-delay: 100ms;<!-- 延迟 -->
        transition-duration: 500ms;<!-- 动画时间 -->
        transition-propety: background-color;<!-- 里面的属性平滑过渡，其他的瞬发 -->
        -weblit-transition-duration: ;
        <!-- -webkit- chrome 和 Safari 浏览器内核支持，安全起见带上 -->
        
    }
</style>
```

## 动画

深入学习过渡

```html
<!DOCTYPE HTML>
<html>
	<head>
        <meta charset="utf-8">
        <title>动画</title>
        <style type="text/css">
            p {
                width: 100px;
                height: 100px;
                background-color: white;
            } 
            p:hover {
                transition-timing-function: ease;
                <!-- 平滑效果速度，其他属性看下面表格 -->
                animation-duration: 500ms;
                animation-delay: 200ms;
                animation-name: suibian;
                animation-iteration-count: infinite;
                <!-- 动画无限执行，一个来回算两次 -->
                animation-direction: alternate;
                <!-- 恢复动画 -->
    		}
            @keyframes suibian {
                from {
                    width: 150px;
                	height: 150px;
                	background-color: orange;
                }
                <!-- 恢复动画的样式 -->
                %50 {
                    
                }
                %75 {
                    
                }
                <!-- 关键帧类似 -->
                to {
                    width: 200px;
                    height: 200px;
                    background-color: blue;
                }
            }
		</style>
	</head>
    <body>
        <p></p>
    </body>
</html>
```

<table>
    <tr>
        <th>属性</th>
        <th>作用</th>
    </tr>
    <tr>
        <td>ease</td>
        <td></td>
    </tr>
    <tr>
        <td>ease-in</td>
        <td>先慢后快</td>
    </tr>
    <tr>
        <td>ease-out</td>
        <td>先快后慢</td>
    </tr>
    <tr>
        <td>ease-in-out</td>
        <td>中间快，两边慢</td>
    </tr>
    <tr>
        <td>liner(默认)</td>
        <td>速度不变</td>
    </tr>
</table>

## 变换

```html
<!DOCTYPE HTML>
<html>
	<head>
        <meta charset="utf-8">
        <title>动画</title>
        <style type="text/css">
            p {
                width: 100px;
                height: 100px;
                background-color: white;
                background-clip: content-box;
                <!-- 背景作用区域 -->
            } 
            p:hover {
                width: 100px;
                height: 100px;
                background-color: white;
                -webkit-transfrom: rotate(45deg);
                <!-- 转动45度，默认顺时针，逆时针加 - 号 -->
                transform: scale(1.5);
                <!-- 缩放  scalex/scaley 缩放x/y -->
                
                transform-origin: top right;
                <!-- 50px 70px -->
    		}
		</style>
	</head>
    <body>
        <p></p>
    </body>
</html>
```



## 盒子模型

常用属性
<table>
    <tr>
        <th>属性</th>
        <th>翻译</th>
    </tr>
    <tr>
        <td>content</td>
        <td>内容</td>
    </tr>
    <tr>
        <td>padding</td>
        <td>填充</td>
    </tr>
    <tr>
        <td>border</td>
        <td>边框</td>
    </tr>
    <tr>
        <td>margin</td>
        <td>边界</td>
    </tr>
</table>

