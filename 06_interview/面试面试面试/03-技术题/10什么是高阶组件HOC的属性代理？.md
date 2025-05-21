# 10什么是高阶组件HOC的属性代理？

## 回答重点

属性代理是高阶组件（HOC）的一种实现方式，它主要有以下特点：

### 1）基本原理

属性代理通过包装组件，在新组件中对传入的 props 进行操作，然后传递给被包装组件。这种方式让我们能够控制传递给组件的 props。

### 2）实现方式

通过函数返回一个新组件，在新组件中对 props 进行处理后传递给原始组件：

```jsx
function withPropsProxy(WrappedComponent) {
  return function (props) {
    const newProps = {
      ...props,
      extraProp: "额外的属性",
    };
    return <WrappedComponent {...newProps} />;
  };
}
```

## 扩展知识

### 1）属性代理的常见用途

1）添加或修改 props：

```jsx
function withLogger(WrappedComponent) {
  return function (props) {
    // 添加日志记录功能
    console.log("Props:", props);
    // 可以添加新的 props
    const newProps = {
      ...props,
      logger: (msg) => console.log(msg),
    };
    return <WrappedComponent {...newProps} />;
  };
}
```

2）条件渲染：

```jsx
function withAuth(WrappedComponent) {
  return function (props) {
    if (!props.isAuthenticated) {
      return <div>请先登录</div>;
    }
    return <WrappedComponent {...props} />;
  };
}
```

### 2）实现功能

1）props 的操作：

- 添加新的 props
- 修改现有 props
- 过滤不需要的 props
- 重命名 props

2）抽象状态与行为：

- 提取公共逻辑
- 状态管理
- 事件处理
- 生命周期封装

### 3）使用建议

1）保持纯函数特性：

- 不修改传入的 props
- 返回新的 props 对象
- 避免产生副作用

2）性能优化：

- 使用 React.memo 优化函数组件
- 避免在渲染时创建新的函数或对象
- 合理使用 props 的浅比较

相关文档链接：

- React 组件文档：[https://zh-hans.react.dev/reference/react/Component](https://zh-hans.react.dev/reference/react/Component)
- React 高阶组件：[https://zh-hans.react.dev/reference/react/memo](https://zh-hans.react.dev/reference/react/memo)

