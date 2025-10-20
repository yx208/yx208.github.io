---
title: React-Router-Dom createBrowserRouter 的 Loader 立即执行特性
date: 2025/08/10 15:40:00
---

## 发现概述

在使用 React Router Dom 的 `createBrowserRouter` 时，**匹配当前 URL 的路由的 loader 函数会在路由器创建时立即执行**，而不是等到 `RouterProvider` 渲染时才执行。

## 问题现象

以下代码的执行顺序令人意外：

```javascript
import ReactDOM from "react-dom/client";
import { createBrowserRouter, RouterProvider } from "react-router-dom";

const router = createBrowserRouter([
    {
        path: "/", // 假设当前 URL 就是 "/"
        loader: () => {
            console.log("hello");
            return null;
        },
        element: <div>Hello</div>
    }
]);

const check = (fn: () => void) => {
    fn();
};

check(() => {
    console.log("inner");
    ReactDOM.createRoot(document.getElementById("root")!).render(
        <RouterProvider router={router}></RouterProvider>
    );
});
```

**预期输出顺序：**
```
inner
hello
```

**实际输出顺序：**
```
hello 1750820993452
inner 1750820993452
```

## 验证方法

### 方法一：详细的执行流程追踪

```javascript
console.log("1. Before createBrowserRouter");

const router = createBrowserRouter([
    {
        path: "/",
        loader: () => {
            console.log("2. loader executed");
            return null;
        },
        element: <div>Hello</div>
    }
]);

console.log("3. After createBrowserRouter");

const check = (fn: () => void) => {
    fn();
};

check(() => {
    console.log("4. inner");
    ReactDOM.createRoot(document.getElementById("root")!).render(
        <RouterProvider router={router}></RouterProvider>
    );
    console.log("5. after render");
});
```

**输出结果：**
```
1. Before createBrowserRouter
2. loader executed
3. After createBrowserRouter
4. inner
5. after render
```

### 方法二：非匹配路径测试

```javascript
const router = createBrowserRouter([
    {
        path: "/other-path", // 不匹配当前 URL
        loader: () => {
            console.log("hello"); // 这个不会执行
            return null;
        },
        element: <div>Hello</div>
    }
]);

check(() => {
    console.log("inner"); // 只会输出这个
    ReactDOM.createRoot(document.getElementById("root")!).render(
        <RouterProvider router={router}></RouterProvider>
    );
});
```

**输出结果：**
```
inner
```

## 技术原理

React Router 引入了**数据路由器（Data Router）**的概念，这是一个重要的性能优化特性：

- `createBrowserRouter` 在创建时会检查当前 URL
- 如果发现有路由匹配当前 URL，会**立即执行对应的 loader**
- 这样可以尽早开始数据加载，提升用户体验
- 这种行为是**同步**的，发生在路由器创建阶段

## 实际影响

### 积极影响
- **更快的数据加载**：数据获取提前开始
- **更好的用户体验**：减少了等待时间
- **并行处理**：数据加载可以与组件渲染并行进行

### 需要注意的问题
- **副作用执行时机**：如果 loader 中有副作用（如 API 调用、状态更新），执行时机比预期更早
- **调试困惑**：可能导致调试时的困惑，特别是在追踪执行顺序时
- **测试影响**：在编写测试时需要考虑这种立即执行的行为

## 最佳实践

### 1. Loader 函数应该是纯净的
```javascript
// ✅ 好的做法：只负责数据获取
const router = createBrowserRouter([
    {
        path: "/users",
        loader: async () => {
            const response = await fetch('/api/users');
            return response.json();
        },
        element: <UsersList />
    }
]);
```

### 2. 避免在 Loader 中执行副作用
```javascript
// ❌ 不好的做法：在 loader 中执行副作用
const router = createBrowserRouter([
    {
        path: "/",
        loader: () => {
            // 这会在路由器创建时立即执行！
            analytics.track('page_view');
            updateGlobalState();
            return null;
        },
        element: <Home />
    }
]);
```

### 3. 如果需要延迟执行，使用组件内的 useEffect
```javascript
// ✅ 好的做法：副作用放在组件中
function Home() {
    useEffect(() => {
        analytics.track('page_view');
        updateGlobalState();
    }, []);
    
    return <div>Home</div>;
}

const router = createBrowserRouter([
    {
        path: "/",
        loader: () => {
            // 只负责数据获取
            return fetchHomeData();
        },
        element: <Home />
    }
]);
```

## 总结

React Router 的 `createBrowserRouter` 会立即执行匹配当前 URL 的 loader，理解这个优化特性有助于：

- 正确理解代码执行顺序
- 避免意外的副作用执行
- 利用它提升用户体验
