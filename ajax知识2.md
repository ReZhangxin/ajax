# ajax

BS架构：浏览器 ~~~ 服务器

php

- $num
- .拼接字符串
- array('1'.'2')
- array('key' => 'fade') arr['key']
- 执行位置：服务端

发送Ajax请求需要五步

1.创建异步对象

```js
var ajaxObj = new XMLHttpRequest();
```

2.设置请求的url等参数

```js
ajaxObj.open('get','index.php')
```

3.发送请求

```js
ajaxObj.send()
```

4.注册事件

```js
  // 如果要在数据完美请求回来的时候才调用 我们需要手动写一些判断逻辑
if (ajaxObj.readState === 4 && ajaxObj.status === 200) {
  ajaxObj.onreadstatechange = function(){
    // 如果能进入这个判断，说明数据完美回来了，并且请求的页面是存在的
    // 5.在注册的事件中获取返回的内容并修改页面显示
  console.log('调用了...')
  }
}
```

