# VUE

## 一、VUE 基础

### 1、介绍

-   js框架
-   简化DOM操作
-   响应是数据驱动



### 2、第一个VUE程序

1、引入vue

```js
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>

<!-- 生产环境版本，优化了尺寸和速度 -->
<script src="https://cdn.jsdelivr.net/npm/vue@2"></script>
```

2、编写程序

```html
<div id="app">
  {{ message }}
</div>
<script>
    var app = new Vue({
        el: '#app',
        data: {
            message: 'Hello Vue!'
        }
    })
</script>
```

### 3、el:挂载点

-   可以使用id，类，元素标签等，选择与css选择器一样
-   作用范围是被选择的元素本身及其后代元素
-   可以在选中任意标签，不能是html和body

### 4、data:数据对象

-   data可以存储json和数组
-   选取子元素使用 `.`，选取列表元素用 `[]`

```html
{{ school.name }}
{{ campus[0] }}
```

## 二、本地应用

### 1、vue指令

vue使用 `v-` 开头的标签表示指令

### 2、内容绑定，事件绑定

#### 2.1、v-text

-   绑定内容使用 `v-text` 标签，可以设置标签中的内容

-   如果在标签中使用 `{{}}` 这种形式，则不会替换，会拼接起来。

-   也可以在指令中直接将字符串拼接。

`v-text="message + 'hello'"` `{{ message + 'hello' }}`

#### 2.2、v-html

-   设置innerHTML
-   如果有html结构会被解析成标签，功能可用
-   v-text不会把html渲染，只会显示文本

#### 2.3、v-on

-    使用方法 `v-on:click="function_name"`
-   也可以使用 `@click="function_name"`
-   方法定义在vue实例的 methods 中
-   获取标签中的值可以在方法中使用 `this.var_name` this指vue对象。

```vue
var app = new Vue({
    el:"#app",
    methods: {
        doId: function() {
            // 逻辑
        }
    }
})
```

#### 2.4、计数器（实例）

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Counter</title>
</head>
<body>
    <div id="app">
        <button @click="sub">
            -
        </button>
        <span>{{ num }}</span>
        <button @click="add">
            +
        </button>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el: "#app",
            data: {
                num: 1
            },
            methods: {
                add: function() {
                    if (this.num < 10) {
                        this.num++;
                    }
                },
                sub: function() {
                    if (this.num > 0) {
                        this.num--;
                    }
                }
            }
        });
    </script>
</body>
</html>
```

### 3、显示切换

#### 3.1、v-show

-   v-show可以控制元素的显示与隐藏

-   使用 `v-show="true"`，值可以设置true、false，也可以是一个布尔值，也可以是一个判断表达式
-   隐藏使用的方式是 `display: none`

#### 3.2、v-if

-   v-if的==功能和用法==和v-show一样
-   区别是v-if直接将元素标签从dom中移除

>   对于频繁切换的元素使用 v-show，因为加载dom浪费性能资源。

#### 3.3、v-bind

-   操纵标签的属性
-   使用方法 `v-bind:属性="值"`，可以省略 `v-bind`
-   值可以是拼接的字符串，也可以是三元表达式 `v-bind:class="isActive?'active':''"`
-   也可以使用对象的形式（推荐） `v-bind:class="{active:isActive}"`

#### 3.4、图片切换实例

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>图片切换</title>
</head>
<body>
    <div id="imgToggle">
        <div>
            <img alt="图片" v-bind:src="imgArr[index]"
                style="width: 720px; height: 540px;object-fit: cover;">
            <a href="javascript:void(0);" v-show="index!=0" @click="prev">
                上一页
            </a>
            <a href="javascript:void(0);" v-show="index<imgArr.length-1" @click="next" >
                下一页
            </a>
        </div>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el: "#imgToggle",
            data: {
                imgArr: [
                    "./img/202005091702.jpg",
                    "./img/202005091703.png",
                    "./img/202005091705.png",
                    "./img/pic39.jpg",
                    "./img/pic44.jpg"
                ],
                index: 0
            },
            methods: {
                prev: function() {
                    console.log("prev");
                    this.index--;
                },
                next: function() {
                    console.log("next");
                    this.index++;
                }
            }
        });
    </script>
</body>
</html>
```

### 4、列表循环，表单元素绑定

