# Suspense、lazy和ErrorBoundary

## react类组件

### **1. 基本结构**

React 类组件是 React 中用于创建组件的一种传统方式（现已被函数式组件 + Hooks 替代，但仍有必要了解）。以下是其核心特性和用法：

```jsx
import React from 'react';

class MyComponent extends React.Component {
  // 构造函数（可选）
  constructor(props) {
    super(props);
    // 初始化状态
    this.state = { count: 0 };
    // 绑定 this（传统方式）
    this.handleClick = this.handleClick.bind(this);
  }

  // 生命周期方法（例如 componentDidMount）
  componentDidMount() {
    console.log('组件已挂载');
  }

  // 自定义方法
  handleClick() {
    this.setState({ count: this.state.count + 1 });
  }

  // 必须实现的 render 方法
  render() {
    return (
      <div>
        <p>计数: {this.state.count}</p>
        <button onClick={this.handleClick}>+1</button>
      </div>
    );
  }
}
```

### **2. 核心特性**

#### **2.1 状态管理 (`state`)**

- **初始化状态**：在 `constructor` 中通过 `this.state` 定义。
- **更新状态**：使用 `this.setState()` 方法，触发重新渲染。

```jsx
this.setState({ count: newValue });
// 或基于前一个状态更新
this.setState((prevState) => ({ count: prevState.count + 1 }));
```

#### **2.2 生命周期方法**

|            生命周期方法             |                       用途                       |
| :---------------------------------: | :----------------------------------------------: |
|        `componentDidMount()`        |   组件挂载后执行（适合发起网络请求、订阅事件）   |
|       `componentDidUpdate()`        | 组件更新后执行（适合操作 DOM 或响应 props 变化） |
|      `componentWillUnmount()`       |    组件卸载前执行（适合清理定时器、取消订阅）    |
|      `shouldComponentUpdate()`      |         控制组件是否重新渲染（性能优化）         |
| `static getDerivedStateFromProps()` |     根据 props 更新 state（使用较少，慎用）      |

#### **2.3 事件处理**

- 绑定 `this`：传统方式需要在constructor中手动绑定，或使用箭头函数：

  ```javascript
  // 方式1：构造函数中绑定
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }
  
  // 方式2：箭头函数（推荐）
  handleClick = () => {
    this.setState({ count: this.state.count + 1 });
  };
  ```

### **3. 类组件 vs 函数式组件**

|    特性    |               类组件               |         函数式组件 + Hooks         |
| :--------: | :--------------------------------: | :--------------------------------: |
|  状态管理  |  `this.state` + `this.setState()`  |             `useState`             |
|  生命周期  |         完整的生命周期方法         |            `useEffect`             |
| 代码复杂度 |    较高（需要处理 `this` 绑定）    |   较低（无 `this`，逻辑更集中）    |
|  复用逻辑  | 通过高阶组件（HOC）或 Render Props |          通过自定义 Hooks          |
|  错误边界  | 支持（需定义 `componentDidCatch`） | 不支持（必须用类组件实现错误边界） |



## Suspense+lazy


Suspense 组件一般是和 React.lazy结合使用，用来加载一些异步组件。

比如这样一个组件：

```jsx
const Foo = () => {
  return <div>Foo</div>;
};
export default Foo;
```

就可以在另一个组件里用 lazy + Suspense 异步加载：

```jsx
import { lazy, Suspense } from "react";

const Foo = React.lazy(() => import("./views/Foo"));

export default function App() {
  return (
    <div>
      <Suspense fallback="loading...">
        <Foo/>
      </Suspense>
    </div>
  );
}
```

这里的 import 是 webpack 提供的用来异步加载模块的 api，它会动态下载模块所在的 chunk，然后从中解析出该模块，拿到 export 的值：

<img src='./images/04/截屏2025-05-06 09.04.27-6493483-6493485.png'>

也可以结合路由一起使用：

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
import { lazy, Suspense } from "react";
const Foo = lazy(() => import("./views/Foo"));
const Bar = lazy(() => import("./views/Bar"));
const App = () => {
  return (
    <BrowserRouter>
      <Routes>
        <Route
          path="/foo"
          element={
            <Suspense fallback="loading1">
              <Foo />
            </Suspense>
          }
        />
        <Route
          path="/bar"
          element={
            <Suspense fallback="loading2">
              <Bar />
            </Suspense>
          }
        />
      </Routes>
    </BrowserRouter>
  );
};

export default App;
```



后台管理系统使用的比较多，因为不可能一下子把所有路由的组件都下载下来，所以会用 lazy + Suspense 的方式异步加载一些组件。

简单实现Suspense：

```jsx
class SimpleSuspense extends React.Component {
  state = {
    isPending: false  // 是否处于加载等待状态
  };

  // 捕获子组件抛出的异常
  componentDidCatch(error) {
    // 如果子组件抛出的是 Promise
    if (error instanceof Promise) {
      this.setState({ isPending: true });
      
      // Promise 完成后重置状态
      error.finally(() => {
        this.setState({ isPending: false });
      });
    }
  }

  render() {
    // 显示加载状态或子组件
    return this.state.isPending 
      ? this.props.fallback 
      : this.props.children;
  }
}
```

大多数场景下，Suspense 就专门和 lazy 搭配使用的。

但有的时候，你会发现 Suspense 不搭配 lazy 也可以。



## ErrorBoundary

现在并不是用 lazy 异步加载组件，怎么触发的 Suspense 呢？

回答这个问题会涉及到 ErrorBoundary。

React组件的两种定义形式：类组件和函数组件（主流+官方推荐）。<img src='./images/04/截屏2025-05-06 09.09.57.png'>

绝大多数情况我们用 function 组件就好了，没必要用 class 组件。

但是有一个特性是只有 class 组件才有的，就是 ErrorBoundary。

这样写：

```js
import { Component } from "react";

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = {
      hasError: false,
    };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, message: error.message };
  }

  componentDidCatch(error, errorInfo) {
    console.log(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <div>出错了： {this.state.message}</div>;
    }
    return this.props.children;
  }
}
```

当子组件报错的时候，会把错误传递给它的 getDerivedStateFromError 和 componentDidCatch 方法。

getDerivedStateFromError 接收 error，返回一个新的 state，会触发重新渲染来显示错误对应的 UI。

componentDidCatch 接收 error 和堆栈 info，可以用来打印错误日志。

我们试一下：

```jsx
function Err() {
  const b = window.a.b;
  return <div>{b}</div>;
}

export default function App() {
  return (
    <ErrorBoundary>
      <Err/>
    </ErrorBoundary>
  );
}
```

window.a.b 不存在，所以正常情况下会报错，页面白屏。

但现在加上 ErrorBoundary 是这样的：<img src='./images/04/截屏2025-05-06 09.12.52.png'>

getDerivedStateFromError 修改 state 触发重新渲染，渲染出错误对应的 UI。

componentDidCatch 拿到错误信息，打印日志。

这样，就对组件抛错的情况做了兜底。

这个特性只有 class 组件有，function 组件没有：<img src='./images/04/截屏2025-05-06 09.13.25.png'>

不过一般也不用自己写这种 ErrorBoundary 组件，直接用 react-error-boundary 这个包就行：

```css
pnpm install react-error-boundary
```

试一下：

```jsx
import { ErrorBoundary } from "react-error-boundary";

function Bbb() {
  const b = window.a.b;

  return <div>{b}</div>;
}

function fallbackRender({ error }) {
  return (
    <div>
      <p>出错了：</p>
      <div>{error.message}</div>
    </div>
  );
}

export default function App() {
  return (
    <ErrorBoundary fallback={fallbackRender}>
      <Bbb></Bbb>
    </ErrorBoundary>
  );
}
```

而且并不一定是 ErrorBoundary 的 children，任意层级的子组件都可以：<img src='./images/04/截屏2025-05-06 09.15.21.png'>

也就是说组件抛错的时候，会向上寻找最近的 ErrorBoundary 组件，这也是 boundary 的含义。

 Suspense和 ErrorBoundary这俩有啥关系？

其实 Suspense 也是用 throw error 的方式实现的。

比如这样：

```jsx
import { Suspense } from "react";

let data, promise;
function fetchData() {
  if (data) return data;
  promise = new Promise((resolve) => {
    setTimeout(() => {
      data = "取到的数据";
      resolve();
    }, 2000);
  });
  throw promise;
}

function Content() {
  const data = fetchData();
  return <p>{data}</p>;
}

export default function App() {
  return (
    <Suspense fallback={"loading data"}>
      <Content />
    </Suspense>
  );
}
```

可以看到，触发了 Suspense：<img src='./images/04/Kapture 2025-05-06 at 09.20.33.gif'>



也就是说，只要 throw 一个 promise，就会被最近的 Suspense 捕获。

promise 初始状态展示 fallback，promise 改变状态后展示子组件。

那 React.lazy 是不是也是基于这个实现的呢？

调试下源码，发现确实是这样：<img src='./images/04/截屏2025-05-06 09.22.22.png'>

React.lazy 包裹之后，也会 throw 一个 promise 来触发 Suspense。

当 promise 改变状态后，再返回拿到的值。

这样为什么 jotai 可以支持 Suspense 我们也就知道了：

也是这样实现的：<img src='./images/04/截屏2025-05-06 09.23.25.png'>

ErrorBoundary 是捕获组件 throw 的错误，而 Suspense 是捕获组件 throw 的 promise，这俩会冲突么？

试一下就知道了：<img src='./images/04/截屏2025-05-06 09.27.07.png'>

包裹一层 ErrorBoundary，你会发现没有触发：<img src='./images/04/Kapture 2025-05-06 at 09.27.44.gif'>

而 throw 一个 error 的时候：<img src='./images/04/截屏2025-05-06 09.28.39.png'>

ErrorBoundary 就触发了：<img src='./images/04/截屏2025-05-06 09.29.10.png'>



也就是说，ErrorBoundary 和 Suspense 虽然都是捕获组件 throw 出的东西，但这俩互不相干，一个捕获 error，一个捕获 promise。



如果 throw 的是 error，就是 error boundary 的处理逻辑，找最近的一个 ErrorBoundary 组件来处理。

如果 throw 的是 promise，则是 suspense boundary 的处理逻辑，找最近的 Suspense 组件来处理。



但业务代码我们不用 Suspense 来写这种 loading。

我们一般会这么写：

```js
import { useEffect, useState } from "react";

function fetchData() {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({
        name: "guang",
      });
    }, 2000);
  });
}

export default function App() {
  const [loading, setLoading] = useState(false);
  const [data, setData] = useState({});

  async function load() {
    setLoading(true);
    const data = await fetchData();
    setData(data);
    setLoading(false);
  }

  useEffect(() => {
    load();
  }, []);

  return <div>{loading ? "loding..." : data.name}</div>;
}
```

就是加一个 state 来记录 loading 状态就行了。

要是用 Suspense，需要 throw 一个 promise 才行，比较麻烦，而且代码也不好维护。



## use



本来 Suspense 就是用来做这个的，结果现在只有 lazy 加载异步组件的时候才能用。

react 团队也在想办法解决这个问题，所以出了一个 use 的 hook：<img src='./images/04/截屏2025-05-06 09.35.27.png'>

这样用：<img src='./images/04/截屏2025-05-06 09.36.21.png'>

它的参数是 promise。

当 promise 在 pending 的时候，展示 suspense 的 fallback。

当 promise 是 resolve 的时候，展示 Suspense 的子组件。

当 promise 是 reject 的时候，展示 ErrorBoundary 的 fallback。

<img src='./images/04/截屏2025-05-06 09.37.02.png'>

这样就不用自己 throw promise 了，业务代码就可以用 Suspense 来 loading 了。

就是 pending 的时候 throw promise，reject 的时候 throw error，否则 return 数据。

这就是文档里写的触发 Suspense 的 3 种方式：

<img src='./images/04/截屏2025-05-06 09.37.45.png'>

一种是用支持 Suspense 的框架，比如 next.js 或者 jotai。

一种是 lazy 异步加载组件。

再一种就是 use 了。

这些不同的方式底层都是 throw promise。



