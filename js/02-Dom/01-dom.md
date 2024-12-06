# DOM

## 一、获取DOM节点

### 1、通过标签名获取

> document.getElementsByTagName('标签名')

### 2、通过class名称获取

> document.getElementByClassName('类名')

### 3、通过id名获取

> document.getElementById('id名')

### 4、HTML5新增获取方式

```js
<body>
  <div class="a">1号</div>
  <div id="b">2号</div>
  <div class="c" id="e">3号</div>
  <div class="c" id="e">4号</div>
  <div class="c" id="e">5号</div>
  <div class="c" id="e">6号</div>
</body>
```

```js
// 通过标签名
let div = document.querySelector('div')
// 通过类名
let aDiv = document.querySelector('.a')
// 通过id名
let bDiv = document.querySelector('#b')
// 以上方法只能获取到一个
// 通过标签名获取所有节点
let divAll = document.querySelectorAll('div')
// 通过类名获取所有节点
let cDivAll = document.querySelectorAll('.c')
// 通过id名获取所有节点
let eDivAll = document.querySelectorAll('#e')
console.log(div,aDiv,bDiv,divAll,cDivAll,eDivAll);
```

<img src='./images/01/01.png'>

### 5、根据层级关系获取元素

