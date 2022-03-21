# ES6



##### 1、本地存储

存储在磁盘中，不会因为关闭浏览器而消失。

例如：本次缓存，cookie，搜索历史等

```javascript
localStorage.setItem('message', 'hello world');
localStorage.readItem('message');
localStorage.removeItem('message');
localStorage.clear();
```

>   1.   以键值对的方式存储
>   2.   值只能是字符串，否则会自动调用toString()
>   3.   重复键的值会覆盖前面的。
>   4.   可以使用 `JSON.Srtingify()` 和 `JSON.parse()` 将对象和字符串之间转换。

##### 2、会话存储

与上面的本地存储类似，也是有以上四个方法。会话存储在关闭浏览器后会删除数据。