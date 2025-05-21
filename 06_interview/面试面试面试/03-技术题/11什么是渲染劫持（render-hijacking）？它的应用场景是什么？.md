# 11什么是渲染劫持（render-hijacking）？它的应用场景是什么？

## 回答重点

渲染劫持（Render Hijacking）是指在React组件渲染过程中，通过高阶组件或者其他技术手段，改变或控制该组件的渲染输出，通常是为了添加额外的功能或者进行某些操作。这种技术可以让我们在不修改原组件的前提下进行自定义的渲染逻辑。

在应用场景方面，渲染劫持常用于以下几种情况：

1）权限控制：根据用户权限动态展示不同的界面或组件。 

2）数据注入：为组件插入额外的数据或者属性。 

3）性能优化：如懒加载特定部分的组件，减少不必要的渲染。 

4）日志记录：记录渲染过程中的数据变化，便于调试和分析。

## 扩展知识

### 实现方式

1）**高阶组件（HOC）**：这是实现场景中最常用的方式。高阶组件是接受一个组件作为输入并返回一个增强版组件的函数。

```js
function withExtraProps(WrappedComponent) {
  return class extends React.Component {
    render() {
      return <WrappedComponent extraProp="Extra Value" {...this.props} />;
    }
  };
}
```

在这个例子中，我们创建了一个高阶组件 `withExtraProps`，它给原组件赋予了一个额外的属性 `extraProp`。

2）**Render Props**：这也是另一个常见的方法，允许你在渲染过程中动态决定渲染的内容。

```js
class RenderPropComponent extends React.Component {
  render() {
    return this.props.render("Injected Data");
  }
}
```

使用时可以这样：

```js
<RenderPropComponent
  render={(data) => <OriginalComponent injectedData={data} />}
/>
```

### 注意事项

1）性能问题：频繁的渲染劫持可能会导致性能问题，因此要谨慎使用，避免滥用。 

2）可维护性：劫持逻辑复杂之后，代码的可读性和维护性可能下降，需要注意代码结构和注释。 

3）兼容性：某些劫持方法可能对React的更新产生影响，需要注意兼容新版本的变化。

### 示例应用

假如有以下需求：在用户未登录时，重定向到登录页面。我们可以使用渲染劫持来实现这样的逻辑：

```js
function withAuth(WrappedComponent) {
  return class extends React.Component {
    render() {
      if (!this.props.isAuthenticated) {
        return <Redirect to="/login" />;
      }
      return <WrappedComponent {...this.props} />;
    }
  };
}
```

在这个例子中，当用户未登录时，将用户重定向到登录页面，而不直接渲染被包裹的组件。

