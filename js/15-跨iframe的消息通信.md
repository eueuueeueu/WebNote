# 跨iframe的消息通信

```html
  <h1>我是父页面里的内容</h1>
  <iframe src="./child1.html" frameborder="0"></iframe>
  <iframe src="./child2.html" frameborder="0"></iframe>
  <!-- 面试题：跨iframe的消息通信 -->
```

```html
  <!-- child1.html -->
  <div class="child-container">
    <button>生成随机数</button>
  </div>
```

```js
  // child1.js
  let port1 = window.parent.port1
  let button = document.querySelector('.child-container button')
  button.addEventListener('click', function () {
    let random = Math.random()
    port1.postMessage(random)
  })
```

```html
  <!-- child2.html -->
  <div class="child-container">
    <ul></ul>
  </div>
```

```css
    /* child2.css */
    .child-container {
      width: 300px;
      height: 300px;
      background-color: orange;
    }
```

```js
  // child2.js
  let port2 = window.parent.port2
  let ul = document.querySelector('ul')
  port2.onmessage = function (e) {
    console.log(e.data);
    let li = document.createElement('li')
    li.innerText = e.data
    ul.appendChild(li)
  }
```







































