# VUE

## 一、VUE 2.x

### （一）、VUE 基础

#### 1、介绍

-   js框架
-   简化DOM操作
-   响应是数据驱动



#### 2、第一个VUE程序

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

#### 3、el:挂载点

-   可以使用id，类，元素标签等，选择与css选择器一样
-   作用范围是被选择的元素本身及其后代元素
-   可以在选中任意标签，不能是html和body

#### 4、data:数据对象

-   data可以存储json和数组
-   选取子元素使用 `.`，选取列表元素用 `[]`

```html
{{ school.name }}
{{ campus[0] }}
```

### （二）、本地应用

#### 1、vue指令

vue使用 `v-` 开头的标签表示指令

#### 2、内容绑定，事件绑定

##### 2.1、v-text

-   绑定内容使用 `v-text` 标签，可以设置标签中的内容

-   如果在标签中使用 `{{}}` 这种形式，则不会替换，会拼接起来。

-   也可以在指令中直接将字符串拼接。

`v-text="message + 'hello'"` `{{ message + 'hello' }}`

##### 2.2、v-html

-   设置innerHTML
-   如果有html结构会被解析成标签，功能可用
-   v-text不会把html渲染，只会显示文本

##### 2.3、v-on

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

##### 2.4、计数器（实例）

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

#### 3、显示切换

##### 3.1、v-show

-   v-show可以控制元素的显示与隐藏

-   使用 `v-show="true"`，值可以设置true、false，也可以是一个布尔值，也可以是一个判断表达式
-   隐藏使用的方式是 `display: none`

##### 3.2、v-if

-   v-if的==功能和用法==和v-show一样
-   区别是v-if直接将元素标签从dom中移除

>   对于频繁切换的元素使用 v-show，因为加载dom浪费性能资源。

##### 3.3、v-bind

-   操纵标签的属性
-   使用方法 `v-bind:属性="值"`，可以省略 `v-bind`
-   值可以是拼接的字符串，也可以是三元表达式 `v-bind:class="isActive?'active':''"`
-   也可以使用对象的形式（推荐） `v-bind:class="{active:isActive}"`

##### 3.4、图片切换实例

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

#### 4、列表循环，表单元素绑定

##### 4.1、v-for

-   不仅可以遍历数组中的元素（包括对象），而且可以循环生成html元素中的内容。
-   基础语法 `v-for="(item, index) in arr"`，item和index分别代表数组中的元素和索引，只要格式正确，名字可以自定义，arr是定义在data中的数组名。
-   如果item是对象，需要使用`.`获取其中的数据。

```html
<div>
    <ul id="app">
        <li v-for="(item, index) in arr" :title="item">{{ index }} -> {{ item }}	</li>
    </ul>
</div>
<script>
    let app = new Vue({
        el: "#app",
        data: {
            arr: [ "A", "B", "C", "D" ]
        }
    });
</script>
```

如果需要把两个li标签中的内容都作为循环内容，可以使用 template标签将其包裹，在template中使用 v-for。

```html
<template v-for="item in items">
    <li>{{ item.name }}</li>
    <li>{{ item.age }}</li>
</template>
```

##### 4.2、v-on补充

-   v-on绑定的函数可以传入参数
-   v-on可以指定事件触发动作

```html
    <div id="app">
        <input type="button" value="click" v-on:click="sayHi(name)">
        <input type="text" v-on:keyup.enter="sayHi(name)">
    </div>
    <script>
        let app = new Vue({
            el: "#app",
            data: {
                name: "张三"
            },
            methods: {
                sayHi: function(yourName) {
                    alert("hello, " + yourName + "!");
                }
            }
        });
    </script>
```

>   更多指令参考：https://cn.vuejs.org/v2/api/#v-on

##### 4.3、v-model

-   设置表单内容
-   双向绑定：表单中的内容改变会引起data中数据的改变；data中数据的改变也会引起表单内容的改变。

```html
    <!-- 表单内容 双向绑定 -->
    <div id="app">
        <input type="button" value="click" @click="setMessage">
        <input type="text" v-model="message" @keyup.enter="showMessage">
        <h3>{{ message }}</h3>
    </div>

    <script>
        let app = new Vue({
            el: "#app",
            data: {
                message: "woshabi"
            },
            methods: {
                showMessage: function() {
                    alert(this.message);
                },
                setMessage: function() {
                    this.message = "woshadiao";
                }
            }
        });
    </script>
```

#### 5、计算属性和侦听器

Vue实例简称vm。

data中的属性是直接数据vm的，而计算属性不直接属于vm，而是需要通过调用函数返回一个值，如果不调用就不会显示。

通常data中的属性不是直接使用的，需要处理一下才能达到要求，在插值表达式中使用会让代码变得复杂，频繁复用也会很麻烦。因此，需要用到vue的计算属性。

##### 5.1、示例

```vue
<div id="demo">
    <p>{{ message }}</p>

    <h3>{{ upperMessage }}</h3>
</div>
<script>
    Vue.config.productionTip = false;
    let vm = new Vue({
        el: "#demo",
        data: {
            message: "hello, world!"
        },
        computed: {
            upperMessage: function() {
                return this.message.toUpperCase();
            }
        }
    });
</script>
```

在创建Vue实例时，添加一个computed属性，在里面定义一个函数名，得到一个返回值。在模板中使用函数名可以直接获取返回值。

##### 5.2、计算属性与方法

如果不使用计算属性，而是使用方法，在methods中定义一个作用相同的方法也可以达到相同的效果。

例如：

```
methods: {
	upperMessage: function() {
		return this.message.toUpperCase();
	}
}
```

使用方法需要在模板中使用括号，表明这是调用一个方法。`{{ upperMessage() }}`

**不同之处：**

计算属性使用缓存，当第一次调用计算属性时，他会执行里面的代码，然后他会把这个值存储到缓存，当下次调用时直接去找缓存。当计算属性的依赖（使用的变量）发生改变时，他会再次执行计算属性的代码，放到缓存，这样就不用担心数据不一致的问题。

##### 5.3、侦听属性

当侦听到某属性发生变化时，会执行其中的代码。这种方式也可以同步数据，但是很多时候会造成代码重复。更好的方式是使用计算属性。

```vue
watch: {
    firstName: function (val) {
        this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
        this.fullName = this.firstName + ' ' + val
    }
}
```

将上面的侦听属性改为计算属性：

```vue
computed: {
	fullName: function() {
		return this.firstName + ' ' + this.lastName;
	}
}
```

##### 5.4、getter/setter

计算属性默认只有getter属性，但是也可以设置setter属性。

```vue
computed: {
    fullName: {
        get: function() {
            return this.firstName + ' ' + this.lastName;
        },
        set: function(fullName) {
            let names = fullName.split(" ");
            this.firstName = names[0];
            this.lastName = names[1];
        }
    }
}
```

##### 5.5、侦听器

虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的侦听器。这就是为什么 Vue 通过 `watch` 选项提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。

侦听器使用 watch定义。里面包含监听的属性和执行响应的代码。

```vue
watch: {
    // 如果 `question` 发生改变，这个函数就会运行
    question: function (newQuestion, oldQuestion) {
        this.answer = 'Waiting for you to stop typing...';
        this.debouncedGetAnswer();
    }
},
```

在这个示例中，使用 `watch` 选项允许我们执行异步操作 (访问一个 API)，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。

#### 6、class与style绑定

##### 6.1、对象语法

需要动态渲染class样式时，可以使用一个对象来表示样式是否渲染。

对象使用 `{}` 包裹，key是class样式的名字，value为一个布尔值，为true时渲染class。

```vue
<div id="demo">
    <!-- 对象渲染 -->
    <p class="static" v-bind:class="{active: isActive}">hello, world!</p>
    <!-- 三元表达式渲染，不推荐 -->
    <p class="static" v-bind:class="isActive == true ? 'active' : ''">hello, world!</p>
</div>

<script>
    Vue.config.productionTip = false;
    let vm = new Vue({
        el: "#demo",
        data: {
            isActive: true
        }
    });
</script>
```

也可以将对象直接写在vue实例中。使用时，直接将classObject写在对应模板位置。

```vue
data: {
    classObject: {
        isActive: true
    }
}
```

##### 6.2、数组语法

1、简单数组

```vue
<div v-bind:class="[activeClass, errorClass]"></div>
```

这样会渲染两个值，分别时activeClass和errorClass在vue实例中定义的值。

2、数组中的三元表达式

数组中逗号间隔的表达式也可以是一个三元表达式。

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

3、数组中的对象

```
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

##### 6.3、用在组件上

使用组件时，也可以给组件定义class属性，这个属性会合并到组件模板的最外层元素上。

##### 6.4、style内联样式

使用内联样式和上面的对象语法和数组语法相同，不过是把css中的样式“对象化”。

```vue
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

##### 6.5、自动添加前缀

当 `v-bind:style` 使用需要添加浏览器引擎前缀的 CSS property 时，如 `transform`，Vue.js 会自动侦测并添加相应的前缀。

##### 6.6、多重值

```vue
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

这样写只会渲染数组中最后一个被浏览器支持的值。

#### 7、事件处理

##### 7.1、基本使用

使用v-on绑定一个事件，然后指定触发事件后执行的函数名既可。函数在vue实例中的methods中定义，具体参考 [2.3、v-on](#####2.3、v-on) 和 [4.2、v-on补充](#####4.2、v-on补充)。

##### 7.2、事件参数

如果需要将触发事件作为参数，可以在定义方法中使用 `event` 关键字作为形参，使用时，在html中使用 `$evnet` 作为参数传入方法。

##### 7.3、事件修饰符



### （三）、网络应用

#### 1、axios

axios 是一个强大的网络请求库。

##### 1.1、导入

-   本地导入
-   cdn

`<script src="https://unpkg.com/axios/dist/axios.min.js"></script>`

##### 1.2、get

```js
axios.get("url?key1=value1&key2=value2")
.then(function(response) {
    console.log(response);
}, function(err) {
    console.log(err);
});
```

-   get方法里面写请求链接和参数
-   then中第一个方法是请求成功后执行的方法
-   then中第二个方法是请求失败后执行的方法

##### 1.3、post

```js
axios.post("url", {
    key1: value1,
    key2:value2
})
.then(function(response) {
    console.log(response);
}, function(err) {
    console.log(err);
});
```

-   与get稍有不同
-   post一共有两个参数，第一个是请求链接，第二个是请求参数，用对象表示

#### 2、axios + vue

唯一一点要注意的是，当在回调函数中，vue的this已经改变，无法使用，需要预先将其存起来。

```js
let app = new Vue({
    el: "#app",
    data: {
        message: "zhangsan"
    },
    methods: {
        getMessage: function() {
            let that = this;
            axios.get(url)
            .then(function(response)){
                that.message = response.data;
            }, function(err) {
                console.log(err);
            });
        }
    }
});
```

### （四）、组件

我们可以把经常用的页面元素打包成一个组件，这样可以减少代码冗余，保持代码整洁。

通常，一个页面只有一个Vue实例，这个实例可以有很多个组件。

#### 1、注册组件

```html
<div id="components-demo">
    <test-component></test-component>
    <test-component></test-component>
    <test-component></test-component>
</div>

<script>
    <!-- 声明一个组件 ("组件名", {}) -->
    const Feature = Vue.component("test-component", {
        <!-- 数据 -->
        data: function () {
            return { count: 0 };
        },
        <!-- 模板 -->
        template:
            '<button v-on:click="count++;">点击{{ count }}次</button>',
    });
    <!-- 绑定元素，只有绑定元素里面才可以使用组件 -->
    let app = new Vue({
        el: "#components-demo"
    });
</script>
```

-   使用声明的组件名作为标签，使用组件。
-   组件之间相互独立，各自维护自己的变量。
-   组件可以被无限复用，它和Vue很多属性相同，不同的是没有el挂载点。

##### 1.1、组件名

组件名有两种书写方式，一就是上面写的使用 `-` 分阁各单词。二是使用大驼峰的方式书写。

使用大驼峰的组件名可以使用 `-` 使用组件，二使用 `-` 的只能使用定义的名字。

##### 1.2、全局注册

```js
Vue.component("component-demo", {});

new Vue({
    el: "#app"
});
```

这种方式注册的组件在任意Vue实例中都可以使用。

##### 1.3、局部注册

```js
// 使用对象的方式定义组件
<div id="app">
    <component-a></component-a>
    <component-a></component-a>
    <component-a></component-a>
</div>
<div id="demo">
    <component-a></component-a>
</div>

<script>
    // 或者使用 Vue.extend({ /* ... */ });
    let component1 = {
        data: function() {
            return { count : 0 };
        },
        template: '<button v-on:click="count++;">点击{{ count }}次</button>'
    };

    let app = new Vue({
        el: "#app",
        components: {
            'component-a': component1
        }
    });

    let demo = new Vue({
        el: "#demo"
    });
</script>
```

##### 1.4、模板

模板最外层必须由一个元素包裹，否则会只显示第一个被顽症包裹的内容。

#### 2、prop（外部变量）

##### 2.1、变量名大小写

因为html的属性是大小写不敏感的，所以在使用组件，给组件内变量赋值的时候，需要将变量名转换成对应的“短横线分隔形式”。







## 二、Vue2.x详细

### （一）、模板细节

#### 1、实例与模板

在js中声明的Vue对象叫作Vue实例，在html中写的标签。通过el绑定的叫作模板。实例和模板之间的对应关系为一对一，多模板对一实例，只有第一个模板会解析实例中的数据；多实例对应一模板会报错。

#### 2、表达式

##### 2.1、js表达式与js语句

表达式是能产生一个值的句子。，例：a、a + b、a == b，funtion()

语句是控制代码流程走向的句子。例：if() {}、for() {}

##### 2.2、表达式使用范围

在插值表达式中和指令中。例 `{{ name }}`、`v-band:src=“url”`

`{{}}` 叫作插值表达式。

2.3、

#### 3、模板表达式







## Vue3.x



