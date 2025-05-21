# 12什么是React的插槽（Portals）？请举例说明React插槽的应用场景

## 回答重点

React 的插槽（Portals）是一种将子节点渲染到父组件 DOM 层次结构以外的 DOM 节点中的方式。它允许开发者突破常规的组件束缚，将特定内容渲染到任意位置。

Portals 提供了一种将组件内容在 DOM 树中“传送”到其他位置的能力，虽然子节点还是其父组件的子节点，但它们被渲染在父组件之外。

```jsx
import ReactDOM from "react-dom";

const portalRoot = document.getElementById("portal-root");

class Modal extends React.Component {
  render() {
    return ReactDOM.createPortal(this.props.children, portalRoot);
  }
}

function App() {
  return (
    <div>
      <h1>My App</h1>
      <Modal>
        <h2>Portal Content</h2>
      </Modal>
    </div>
  );
}
```

## 扩展知识

1）**应用场景**：

- **模态框或弹出窗口**：因为这种内容通常需要脱离当前的 DOM 层次结构，以避免 z-index 的复杂问题。
- **工具提示（Tooltip）或对话框（Dialog）**：这些 UI 元素需要在不同的层级进行渲染，以便正确地设置它们的外观和行为。
- **非遮挡元素**：某些元素可能需要全局性的样式和布局，而不局限在父组件的影响下。

2）**实现细节**： Portals 实际上通过调用 `ReactDOM.createPortal(child, container)` 来实现。在这个方法中，`child` 是任何可渲染的 React 子元素，而 `container` 遍是一个 DOM 元素。

3）**事件冒泡**： 虽然 Portals 将内容渲染到父组件之外的 DOM 节点，但从事件冒泡的角度来看，它们依然认为是父组件的子元素。这意味着，例如，如果你在 Portal 中点击一个按钮，事件仍然会冒泡到其传统的 React 父组件上。

4）**类似功能的自行实现**： 如果你使用的是原生 JavaScript，你可能会通过 `document.createElement` 和 `appendChild` 等方法手动操作 DOM。然而，React 的 Portals 简化了这个过程，并为其注入了 React 的机制和生态，使得状态的改变和渲染都更加高效和可控。

5）**性能注意事项**： Portals 的使用并不会对性能造成明显的影响，因为它们依然遵循 React 的单向数据流和视图更新机制。这就意味着你可以放心地使用它们，而不必担心引入复杂的性能问题。

6）**与 Context 的结合**： 在一些高级用例中，可以将 Portals 和 React 的 Context API 结合使用，从而实现更加灵活和复杂的组件间通信和状态管理功能。

