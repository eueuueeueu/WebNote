# 9如何在React项目中使用Hooks从服务端获取数据？

## 回答重点

在 React 项目中使用 Hooks 获取服务端数据主要有以下几种方式：

### 1）使用 useState + useEffect

这是最基础的方式，适合简单的数据获取场景：

```jsx
function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUsers = async () => {
      setLoading(true);
      try {
        const response = await fetch("https://api.example.com/users");
        const data = await response.json();
        setUsers(data);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  if (loading) return <div>加载中...</div>;
  if (error) return <div>出错了: {error.message}</div>;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### 2）使用自定义 Hook

将数据获取逻辑封装成可复用的 Hook：

```jsx
function useData(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch(url);
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
}
```

## 扩展知识

### 1）数据获取的最佳实践

在实际项目中，我们通常不会直接在 Effect 中请求数据，而是使用一些成熟的数据请求方案：

1.  使用 React Query、SWR 等数据请求库
2.  使用 React Router 6.4+ 的数据加载机制
3.  使用 React Suspense 配合数据获取

### 2）避免常见问题

1.  竞态条件：当有多个请求同时进行时，可能后发出的请求先返回结果
2.  内存泄漏：组件卸载时需要取消未完成的请求
3.  重复请求：应该合理设置依赖项，避免无限循环

### 3）错误处理和加载状态

良好的用户体验需要处理以下状态：

1.  加载状态：显示加载指示器
2.  错误状态：显示错误信息
3.  空状态：当没有数据时显示提示
4.  成功状态：正确展示数据

相关文档：

- [https://zh-hans.react.dev/reference/react/useEffect](https://zh-hans.react.dev/reference/react/useEffect)
- [https://zh-hans.react.dev/learn/synchronizing-with-effects](https://zh-hans.react.dev/learn/synchronizing-with-effects)
- [https://zh-hans.react.dev/learn/reusing-logic-with-custom-hooks](https://zh-hans.react.dev/learn/reusing-logic-with-custom-hooks)

