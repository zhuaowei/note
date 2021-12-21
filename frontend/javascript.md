# javascript

添加动态消息

可以放在任何位置

## 变量

组成： 大小写字母、%、_、数字

数字不能放在最前

避免关键字



每个语句后要加分号代表结束

双引号之间代表文本

变量使用：

定义：

```html
<script>
    var number1 = 1;
    number1 = number1 + 1;
    document.write("<h1>"+number1+"</h1>");
</script>
```

```html
<script>
    var name=prompt("你的名字？", "");
    document.write("<h1>" + name + "</h1>");
</script>
```

## 条件判断

```html
<script>
    var pwd = prompt("密码？", "");
    <!-- 没有字母可以转换为数字 -->
    if (pwd == 123) {
        document.write("正确");
        } else {
            document.write("错误");
        }
</script>
```

```html
<script>
    var age = prompt("年龄？", "");
    if (age < 18) {
        document.write("太小，退出");
    } else if (age >= 18) {
        
    }
</script>
```

## while循环

```html
    <script>
        var pwd = prompt("密码？", "");
        while (pwd != 123) {
            pwd = prompt("密码错误，请再次输入", "");
        }
        if (pwd == 123) {
            document.write("正确");
        }
    </script>

```

## for循环

```html
<script>
    for (var num = 0; num < 10; num++) {
        document.write(num + "<br>");
    }
</script>
```

## 函数

```javascript
function go (num1, num2) {
    return num1 + num2;
}
```

## 数组

```javascript
myList = ["apples", "oranges", "bananas"];
myList[3] = "pears";

// 删除第一个
myList.shift();

// 删除最后一个
myList.pop();
```

## 循环

```javascript
与 C 语言类似
```

```javascript
// 在控制台显示
console.log();

// 数组长度
myList.length;

// 弹窗警告
alert();
```

