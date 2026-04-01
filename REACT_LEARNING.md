# React 学习文档

这是一份面向入门和快速复习的 React 学习笔记，重点回答三个问题：

1. React 是什么
2. React 的核心是什么
3. React 常用的 API / Hooks 有哪些

## 1. React 是什么

React 是一个用于构建用户界面的 JavaScript / TypeScript 库。它最适合做“状态驱动”的界面：数据变化后，界面自动更新。

React 的典型特点：

- 组件化：把页面拆成可复用的小块
- 声明式：你描述 UI 应该长什么样，而不是手动一步步改 DOM
- 单向数据流：数据通常从父组件流向子组件
- 状态驱动更新：state 变了，UI 跟着变

## 2. React 的核心是什么

React 的核心可以概括为四件事：

### 2.1 组件

React 的 UI 由组件组成。组件是可复用的 UI 单元，可以是函数组件，也可以是类组件，但现代 React 主要使用函数组件。

### 2.2 状态

状态是组件内部保存的动态数据。状态变化会触发重新渲染。

### 2.3 声明式渲染

你只需要写出“数据 -> UI”的映射关系，React 会负责根据状态变化更新界面。

### 2.4 Hooks

Hooks 是 React 函数组件获取状态、副作用、缓存、上下文等能力的主要方式。现代 React 的核心编程模型基本就是 Hooks。

## 3. React 的工作方式

React 的基本流程可以理解为：

1. 组件接收 `props`
2. 根据 `props` 和 `state` 计算 UI
3. React 渲染界面
4. `state` 改变后重新执行组件函数
5. React 对比前后结果，只更新变化的部分

你不需要直接操作 DOM，大多数情况下只需要操作数据和状态。

## 4. 常用 API / Hooks

下面这些是最常用的 React API。实际开发里，先掌握前 8 个就够用了。

### 4.1 `useState`

用于组件内部状态管理。

```tsx
const [count, setCount] = useState(0)
```

最小示例：

```tsx
import { useState } from 'react'

function Counter() {
  const [count, setCount] = useState(0)

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  )
}
```

常见用途：

- 表单输入
- 开关状态
- 计数器
- 本地 UI 状态

### 4.2 `useEffect`

用于副作用，比如请求数据、订阅事件、手动操作浏览器 API。

```tsx
useEffect(() => {
  document.title = `Count: ${count}`
}, [count])
```

最小示例：

```tsx
import { useEffect, useState } from 'react'

function TitleSync() {
  const [count, setCount] = useState(0)

  useEffect(() => {
    document.title = `Count: ${count}`
  }, [count])

  return <button onClick={() => setCount(c => c + 1)}>Inc</button>
}
```

常见用途：

- 拉取接口
- 监听事件
- 清理定时器
- 订阅 / 取消订阅

### 4.3 `useContext`

用于跨层级共享数据，避免层层传 props。

```tsx
const theme = useContext(ThemeContext)
```

最小示例：

```tsx
import { createContext, useContext } from 'react'

const ThemeContext = createContext('light')

function Toolbar() {
  const theme = useContext(ThemeContext)
  return <div>Theme: {theme}</div>
}
```

常见用途：

- 主题
- 用户信息
- 国际化
- 全局配置

### 4.4 `useRef`

用于保存一个不会触发重渲染的可变值，也常用于拿到 DOM 引用。

```tsx
const inputRef = useRef<HTMLInputElement | null>(null)
```

最小示例：

```tsx
import { useRef } from 'react'

function FocusButton() {
  const inputRef = useRef<HTMLInputElement | null>(null)

  return (
    <>
      <input ref={inputRef} />
      <button onClick={() => inputRef.current?.focus()}>
        Focus
      </button>
    </>
  )
}
```

常见用途：

- 访问 DOM
- 保存定时器 ID
- 保存上一次值

### 4.5 `useMemo`

用于缓存计算结果，避免重复计算。

```tsx
const filtered = useMemo(() => heavyFilter(items), [items])
```

最小示例：

```tsx
import { useMemo } from 'react'

function List({ items }: { items: string[] }) {
  const sorted = useMemo(() => [...items].sort(), [items])
  return <div>{sorted.join(', ')}</div>
}
```

常见用途：

- 重计算代价高的派生数据
- 避免不必要的重复计算

### 4.6 `useCallback`

用于缓存函数引用，常配合 `memo` 使用。

```tsx
const handleClick = useCallback(() => {
  setCount(c => c + 1)
}, [])
```

最小示例：

```tsx
import { useCallback, useState } from 'react'

function Clicker() {
  const [count, setCount] = useState(0)
  const inc = useCallback(() => setCount(c => c + 1), [])
  return <button onClick={inc}>{count}</button>
}
```

常见用途：

- 传给子组件的回调
- 避免无意义的函数引用变化

### 4.7 `useReducer`

适合复杂状态逻辑，尤其是状态变化比较多的时候。

```tsx
const [state, dispatch] = useReducer(reducer, initialState)
```

最小示例：

```tsx
import { useReducer } from 'react'

type State = { count: number }
type Action = { type: 'inc' }

function reducer(state: State, action: Action): State {
  if (action.type === 'inc') return { count: state.count + 1 }
  return state
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 })
  return <button onClick={() => dispatch({ type: 'inc' })}>{state.count}</button>
}
```

常见用途：

- 多状态联动
- 表单状态
- 复杂交互

### 4.8 `memo`

用于记忆组件，避免 props 不变时重复渲染。

```tsx
const Item = memo(function Item(props) {
  return <div>{props.name}</div>
})
```

最小示例：

```tsx
import { memo } from 'react'

const Item = memo(function Item({ name }: { name: string }) {
  return <div>{name}</div>
})
```

常见用途：

- 列表项
- 重渲染成本较高的子组件

### 4.9 `useLayoutEffect`

和 `useEffect` 类似，但在浏览器绘制前执行。

常见用途：

- 测量布局
- 避免闪烁

最小示例：

```tsx
import { useLayoutEffect, useRef } from 'react'

function Measure() {
  const ref = useRef<HTMLDivElement | null>(null)

  useLayoutEffect(() => {
    const width = ref.current?.getBoundingClientRect().width
    console.log(width)
  }, [])

  return <div ref={ref}>Hello</div>
}
```

### 4.10 `forwardRef`

把 `ref` 转发给子组件内部元素。

常见用途：

- 封装输入框、按钮等基础组件

最小示例：

```tsx
import { forwardRef } from 'react'

const Input = forwardRef<HTMLInputElement, { placeholder?: string }>(
  function Input(props, ref) {
    return <input ref={ref} placeholder={props.placeholder} />
  },
)
```

### 4.11 `Suspense`

用于等待异步资源，常配合 `lazy` 做代码分割。

最小示例：

```tsx
import { lazy, Suspense } from 'react'

const Page = lazy(() => import('./Page'))

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Page />
    </Suspense>
  )
}
```

### 4.12 `lazy`

用于懒加载组件。

```tsx
const Page = lazy(() => import('./Page'))
```

### 4.13 `useTransition` / `useDeferredValue`

用于优化交互响应，适合高频更新或重渲染较重的场景。

最小示例：

```tsx
import { useDeferredValue, useState } from 'react'

function Search({ items }: { items: string[] }) {
  const [query, setQuery] = useState('')
  const deferredQuery = useDeferredValue(query)
  const filtered = items.filter(item => item.includes(deferredQuery))

  return (
    <>
      <input value={query} onChange={e => setQuery(e.target.value)} />
      <div>{filtered.join(', ')}</div>
    </>
  )
}
```

## 5. 最常见的组合

### 5.1 表单

- `useState`
- `useEffect`
- `useRef`

### 5.2 数据请求

- `useEffect`
- `useState`
- `useMemo`

### 5.3 全局状态

- `useContext`
- `useReducer`

### 5.4 性能优化

- `memo`
- `useMemo`
- `useCallback`

## 6. 学习 React 的正确顺序

建议按这个顺序学：

1. JSX
2. 组件和 props
3. `useState`
4. `useEffect`
5. 事件处理
6. 条件渲染和列表渲染
7. `useContext`
8. `useReducer`
9. `useMemo` / `useCallback`
10. `memo` / `forwardRef` / `lazy` / `Suspense`

## 7. 一句话总结

React 的核心不是“写 HTML”，而是：

- 用组件组织 UI
- 用 state 描述变化
- 用声明式方式让界面跟着数据自动更新
- 用 Hooks 在函数组件中完成状态、副作用和复用逻辑

## 8. React + TypeScript 入门

在 React 里用 TypeScript，重点是给 `props`、事件、状态、ref 和返回值加类型。

### 8.1 组件 Props

```tsx
type UserCardProps = {
  name: string
  age?: number
}

function UserCard({ name, age }: UserCardProps) {
  return <div>{name} {age ?? 'unknown'}</div>
}
```

### 8.2 `useState` 类型

```tsx
const [count, setCount] = useState<number>(0)
const [user, setUser] = useState<{ name: string } | null>(null)
```

### 8.3 事件类型

```tsx
function handleChange(e: React.ChangeEvent<HTMLInputElement>) {
  console.log(e.target.value)
}
```

### 8.4 `ref` 类型

```tsx
const inputRef = useRef<HTMLInputElement | null>(null)
```

### 8.5 常见原则

- 优先给组件 props 定义类型
- 状态为空时，用 `null` 或联合类型表达
- 不要为了“类型完整”写过度复杂的类型
- 先写清数据结构，再写 UI

## 9. React 常见面试题

### 9.1 React 的核心是什么？

组件、状态、声明式渲染、Hooks。

### 9.2 `props` 和 `state` 有什么区别？

- `props` 是父组件传给子组件的数据，通常只读
- `state` 是组件内部状态，会触发重新渲染

### 9.3 `useEffect` 和 `useLayoutEffect` 区别是什么？

- `useEffect` 在渲染后执行
- `useLayoutEffect` 在浏览器绘制前执行，更适合测量布局

### 9.4 为什么 `setState` 后 UI 会更新？

因为状态变化会触发组件重新渲染，React 再把新结果和旧结果对比更新 DOM。

### 9.5 `useMemo` 和 `useCallback` 有什么区别？

- `useMemo` 缓存的是值
- `useCallback` 缓存的是函数

### 9.6 为什么不要滥用 Hooks？

Hooks 不是越多越好。只有在状态、副作用、缓存或上下文真的需要时才用。

### 9.7 什么是受控组件？

输入框的值由 React state 控制的组件。

### 9.8 什么是单向数据流？

数据从上层组件向下层组件传递，子组件通常通过回调把事件传回去。

## 10. Hooks 速查表

### 状态

- `useState`: 组件本地状态
- `useReducer`: 复杂状态逻辑

### 副作用

- `useEffect`: 数据请求、订阅、定时器
- `useLayoutEffect`: 布局测量、避免闪烁

### 复用和引用

- `useRef`: DOM 引用、持久变量
- `useContext`: 跨层共享数据

### 性能

- `useMemo`: 缓存值
- `useCallback`: 缓存函数
- `memo`: 缓存组件
- `lazy`: 懒加载组件
- `Suspense`: 等待懒加载或异步资源

### 交互优化

- `useTransition`: 把低优先级更新延后
- `useDeferredValue`: 延迟消费高频变化的数据

## 11. React 项目结构示例

一个常见的中小型 React 项目可以这样组织：

```text
src/
├── components/      # 通用 UI 组件
├── pages/           # 页面级组件
├── hooks/           # 自定义 hooks
├── context/         # 全局上下文
├── services/        # 请求和业务接口
├── utils/           # 工具函数
├── types/           # TypeScript 类型
├── assets/          # 图片、样式等静态资源
└── App.tsx          # 根组件
```

### 建议

- `components` 放可复用、无业务偏好的组件
- `pages` 放页面级逻辑
- `hooks` 放可复用状态逻辑
- `services` 放请求和数据访问
- `types` 放共享类型定义

## 12. 实战建议

- 先把组件拆小，再考虑复用
- 先保证数据流清晰，再做性能优化
- 优先掌握 `useState`、`useEffect`、`useContext`、`useRef`
- 不要为了炫技过度抽象，先让代码可读

## 13. 常见错误清单

- 在 JSX 里直接修改 state 对象或数组
- `useEffect` 依赖数组写错，导致死循环或不更新
- 把所有逻辑都塞进一个大组件
- 滥用 `useMemo` / `useCallback`，让代码更难读
- 以为 `ref` 变化会自动触发渲染
- 忽略 `key`，导致列表渲染异常
- 把副作用写在组件函数体里，而不是 `useEffect`

## 14. 记忆口诀

- `props` 是外部传入的
- `state` 是内部变化的
- `effect` 是处理副作用的
- `ref` 是保存引用和不触发渲染的值
- `memo` 是减少重复渲染的
- `context` 是跨层共享数据的

