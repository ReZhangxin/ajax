# ajax

BS架构：浏览器 ~~~ 服务器

php

- $num
- .拼接字符串
- array('1'.'2')
- array('key' => 'fade') arr['key']
- 执行位置：服务端

## 发送Ajax get请求需要五步

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
    // 数据是保存在异步对象的属性中
    console.log(ajaxObj.responseText)
  }
}
```

## Ajax向后台传值

```js
// 第一步：获取异步对象
var ajax = new XMLHttpRequest()
var username = document.querySelector('#nameText').value
// 第二步：设置请求url等参数
ajax.open('get', 'index.php?name='+username)
// 第三步：发送请求
ajax.send()
// 第四步：注册事件
ajax.onreadystatechange = function () {
 if (ajax.readyState === 4 && ajax.status === 200) {
 // 第五步：在注册事件中获取返回的内容并修改页面显示
   document.getElementById('box').innerHTML=ajax.responseText
 }
}
```

后台接收参数并返回数据

```php
echo $_GET['name'].'你好啊~';
```

## Ajax发送POST请求

```js
// 第一步：创建异步对象
var ajax = new XMLHttpRequest()
// 第二步：设置url
ajax.open('post', 'index.php')
// 第三步：设置HTTP头
ajax.setRequestHeader('Content-Type','application/x-www-form-urlencoded')
// 第四步：发送请求(携带参数)
ajax.send('name=xin&&age=22')
// 第五步：注册事件
ajax.onreadystatechange = function () {
 if (ajax.readyState === 4 && ajax.status === 200) {
// 第六步：在注册事件中获取返回的内容并修改页面显示
   document.getElementById('box').innerHTML=ajax.responseText
 }
}
```

后台接收数据

```php
  // 获取POST数据
 echo $_POST['name'].'你好';
 echo '<br>';
 echo $_POST['age'].'你这么大了';
```

## 举个栗子：切换头像

### 浏览器

1、xxx.html

2、点击事件发送ajax请求

3、请求方法： post

4、步骤：

- 获取异步对象
- 设置url
- 发送请求
- 设置HTTP头
- 注册事件
- 在注册事件中修改页面（1、找到DOM元素；2、修改头像url）

```js
//  <div class="imgBox"></div>
//  <input type="button" class='starBtn' value='xin'>
//  <input type="button" class='starBtn' value='fei'>
//  <input type="button" class='starBtn' value='suo'>
var starBtns = document.querySelectorAll('.starBtn')
for (var i = 0; i < starBtns.length; i++) {
  starBtns[i].onclick = function () {
    var ajax = new XMLHttpRequest()
    ajax.open('post','index.php')
    ajax.setRequestHeader('Content-Type','application/x-www-form-urlencoded')
    ajax.send('name' + this.value)
    ajax.onreadystatechange = function () {
      if (ajax.readyState === 4 && ajax.status === 200) {
        // 修改页面显示
        document.querySelector('.imgBox').style.background = 'url('+ajax.responseText+') no-repeat center'
      }
    }
  }
}
```

### 服务器

1、xxx.php

2、`$_POST`获取数据

- 获取到的xin
- 返回的是image/xin.jpg

3、php逻辑

- 准备关系型数组
- `$_POST`获取`key`
- 使用`key`获取对应的`value`
- 返回`value`给浏览器

```php
  // 获取POST数据
 $key = $_POST['name'];

 $starArr = array(
   'xin' => 'image/xin.jpg',
   'fei' => 'image/fei.jpg',
   'suo' => 'image/suo.jpg'
 )

```

## XML

- XML 的设计宗旨是传输数据，而不是显示数据。
- 是按照某种既定的格式写的字符串
- 通通使用双标签
- 最外层需要一个根节点
- 标签的名字可以随便编写（不要使用数字开头，不要使用中文）
- 如果写了版本号那么版本就要作为第一行否则会报错
- 如果使用php读取，那么要设置header

异步对象中有另外一个属性 用来专门获取xml

xml对象在浏览器端就是一个document对象

解析时可以直接使用querySelector获取

```js
console.log(ajax.responseXML.querySelector('name').innerHTML)
```

## 封装Ajax工具函数

```js
function ajax (option) {
  var ajax = new XMLHttpRequest()
  // 格式化传入的数据为name=fox&age=18这样的格式
  var data = "";
  for(var item in option.data){
      // 获取属性名,属性值,进行拼接
      data+=item;// 属性名
      data+="=";//等号
      data+=option.data[item];//属性值
      data+="&";//分隔符
  }
  // 去除最后一个&
  data = data.slice(0,-1);
  if (option.methods === 'get') {
    if (data) {
      ajax.open(option.methods, option.url+'?'+data)
    } else {
      ajax.open(option.methods, option.url)
    }
    ajax.send()
  } else if (option.methods === 'post') {
    ajax.open(option.methods,option.url)
    ajax.setRequestHeader("Content-type","application/x-www-form-urlencoded");
    if (data) {ajax.send(data)
    } else {
      ajax.send()
    }
  }
  ajax.onreadystatechange = function () {
    if (ajax.readyState==4&&ajax.status==200) {
      option.success(ajax.responseText);
    }
  }
}
```

举个栗子：

> js

```js
document.getElementById('send').onclick = function () {
  ajax({
    url: 'index.php',
    data:{user:'Railgun',age:22,},
    methods: 'get',
    success: function (data) {
      alert(data)
    }
  })
}
```

> php

```php
$name = $_GET['user'];
$age = $_GET['age'];
echo 'Hello~'.$name.'，你今年'.$age.'岁了~';
```
