# Promise

## Promise是什么？

> 对于当前发生的事情，在现在无法去拿到结果，需要时间验证(异步操作)
>
> 例子：
>
> ​      生活中：借钱(给出承诺) --- 还钱(兑现承诺) --- 联系不到(没有兑现承诺)
>
> ​      js中：     new Promise    ---       resolve        ---       reject

一个 `Promise` 必然处于以下几种状态之一：

- *待定（pending）*：初始状态，既没有被兑现，也没有被拒绝。
- *已兑现（fulfilled）*：意味着操作成功完成。
- *已拒绝（rejected）*：意味着操作失败。

## Promise的常用原型方法

### promise.then

### promise.catch

### promise.finally

## Promise常用的静态方法

### Promise.all

### Promise.race

### Promise.resolve

### Promise.reject

-------------

> 在创建Promise实例的时候，传递给Promise构造函数的参数函数中去异步调用resolve/reject，
>
> 用来触发提前注册给Promise实例的then回调函数和catch回调函数

```js
let promise = new Promise(function (resolve, reject) {
    setTimeout(function () {
      let random = Math.random()
      console.log(random);
      if (random >= 0.5) {
        resolve(1000)
      } else {
        reject('联系不上')
      }
    }, 2000)
  })
  promise
    // 提前注册回调函数（收集成功的回调）
    // 如果内部调用resolve 那么传递给then的参数就会被执行
    .then(function (result) {
      console.log('成功的原因:还了:', result);
      console.log('下次还借你');
    })
    // 提前注册回调函数（收集失败的回调）
    // 如果内部调用resolve 那么传递给then的参数就会被执行
    .catch(function (reason) {
      console.log('失败的原因:', reason);
      console.log('花钱买教训，以后谁都不借');
    })
    .finally(function () {
      console.log('无论成功失败,我总执行');
    })
```

> 在创建Promise实例的时候，传递给Promise构造函数的参数函数中去异步调用resolve/reject，
>
> 用来触发提前注册给Promise实例的then回调函数和catch回调函数

--------------

#### 案例1：通过浏览器打开本地文件夹

```js
  let button = document.querySelector('.openDir')
  button.addEventListener('click', () => {
    let p = showDirectoryPicker()  //打开文件夹方法,返回一个promise,打开成功调用resolve,打开失败调用reject
    p.then(function (result) {// 如果调用了resolve则执行then
      console.log(result);
    }).catch(function (reason) {// 如果调用了reject则执行catch
      console.log(reason);
    })
  })
```

#### 案例2：全屏/退出全屏

```html
  <div>
    <button class="openFullscreen">全屏</button>
    <button class="exitFullscreen">退出全屏</button>
  </div>
```

```css
    div {
      width: 200px;
      height: 200px;
      background-color: red;
    }
```

```js
  // 例子1：元素全屏/退出全屏
  let openButton = document.querySelector('.openFullscreen')
  let exitButton = document.querySelector('.exitFullscreen')
  openButton.addEventListener('click', function () {
    //this.parentElement.requestFullscreen()返回一个promise
    let r = this.parentElement.requestFullscreen()
    r.then(function () {
      console.log('全屏操作成功');
    }).catch(function () {
      console.log('全屏操作失败');
    })
  })
  exitButton.addEventListener('click', function () {
    //document.exitFullscreen()返回一个promise
    let r = document.exitFullscreen()
    r.then(function () {
      console.log('退出成功');
    }).catch(function () {
      console.log('退出失败');
    })
  })
```

