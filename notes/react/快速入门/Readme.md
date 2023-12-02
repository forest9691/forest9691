## 创建和嵌套组件 

React 应用程序是由 **组件** 组成的。一个组件是 UI（用户界面）的一部分，它拥有自己的逻辑和外观(参考下面的界面更新)。组件可以小到一个按钮，也可以大到整个页面。

React 组件是返回标签的 JavaScript 函数：

```jsx
function MyButton() {
  return (
    <button>I'm a button</button>
  );
}
```

## 添加样式 

在 React 中，你可以使用 `className` 来指定一个 CSS 的 class。它与 HTML 的 [`class`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes/class) 属性的工作方式相同：

```jsx
<img className="avatar" /> // 这种方式可以用<link>引入css文件
```

```jsx
import Style from "../Style.less"

function Img() {
    return <img classname={Style.avatar} />
}
```

然后，你可以在一个单独的 CSS 文件中为它编写 CSS 规则：

```css
/* In your CSS */
.avatar {
  border-radius: 50%;
}
```

## 显示数据 

JSX 会让你把标签放到` JavaScript`中。而大括号会让你 “回到” JavaScript 中，这样你就可以从你的代码中嵌入一些变量并展示给用户。

例如，这将显示 `user.name`：

```jsx
const button = <Button>button</Button>;
// or
return (
  <h1>
    {
        //这里是JS语法，所以可以写任意的表达式
    	user.name 
    } 
  </h1>
);
```



```jsx
return (
  <img
    className="avatar"
    src={user.imageUrl} // 你还可以将JSX属性 “转义到 JavaScript”，但你必须使用大括号 **而非** 引号。
    style={{
      // 上面第一个大括号表示的是让你回到javascript中语法编写中。
      // 上面第二个大括号表示的是一个对象，对象也是一个表达式的值
      width: user.imageSize,
      height: user.imageSize
    }}
  />
);
```

## 条件渲染 

React 没有特殊的语法来编写条件语句，因此你使用的就是普通的 JavaScript 代码。例如使用 [`if`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/if...else) 语句根据条件引入 JSX：

```jsx
let content;
if (isLoggedIn) {
  content = <AdminPanel />;
} else {
  content = <LoginForm />;
}

return (
  <div>{content}</div>
);
```

如果你喜欢更为紧凑的代码，可以使用 [条件 `?` 运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)。与 `if` 不同的是，它工作于 JSX 内部：

```jsx
<div>{isLoggedIn ? (<AdminPanel />) : (<LoginForm />)}</div>
```

当你不需要 `else` 分支时，你还可以使用 [逻辑 `&&` 语法](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Logical_AND#short-circuit_evaluation)：

```jsx
<div>{isLoggedIn && <AdminPanel />}</div>
```

## 渲染列表 

你将依赖 JavaScript 的特性，例如 [`for` 循环](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for) 和 [array 的 `map()` 函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 来渲染组件列表。

假设你有一个产品数组：

```jsx
const products = [
  { title: 'Cabbage', id: 1 },
  { title: 'Garlic', id: 2 },
  { title: 'Apple', id: 3 },
];
```

在你的组件中，使用 `map()` 函数将这个数组转换为 `<li>` 标签构成的列表:

```jsx
const listItems = products.map(product =>
  <li key={product.id}>
    {product.title}
  </li>
);

return ( <ul>{listItems}</ul>);
```

注意， `<li>` 有一个 `key` 属性。对于列表中的每一个元素，你都应该传递一个字符串或者数字给 `key`，用于在其兄弟节点中唯一标识该元素。通常 key 来自你的数据，比如数据库中的 ID。如果你在后续插入、删除或重新排序这些项目，React 将依靠你提供的 key 来思考发生了什么。

## 响应事件 

你可以通过在组件中声明 **事件处理** 函数来响应事件：

```jsx
function MyButton() {
  function handleClick() {
    alert('You clicked me!')
  }
  // 下面的大括号是由标签语法回到javascript请求
  return (<button onClick={handleClick}>Click me</button>);
}
```

注意，`onClick={handleClick}` 的结尾没有小括号！不要 **调用** 事件处理函数：你只需 **把函数传递给事件** 即可。当用户点击按钮时 React 会调用你传递的事件处理函数。

## 更新界面 

通常你会希望你的组件 “记住” 一些信息并展示出来，比如一个按钮被点击的次数。要做到这一点，你需要在你的组件中添加 **state**。

```jsx
import { useState } from 'react';

export default function MyApp() {
  return (
    <div>
      <h1>Counters that update separately</h1>
      <!-- 这里说明了组件拥有自己的外观或逻辑 -->
      <MyButton />
      <MyButton />
    </div>
  );
}

function MyButton() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      Clicked {count} times
    </button>
  );
}
```

## 使用 Hook 

「理解的重点」以 `use` 开头的函数被称为 **Hook**。`useState` 是 React 提供的一个内置 Hook。你可以在 [React API 参考](https://react.docschina.org/reference/react) 中找到其他内置的 Hook。你也可以通过组合现有的 Hook 来编写属于你自己的 Hook。

Hook 比普通函数更为严格。你只能在你的组件（或其他 Hook）的 **顶层** 调用 Hook。如果你想在一个条件或循环中使用 `useState`，请提取一个新的组件并在组件内部使用它。

## 组件间共享数据 

在前面的示例中，每个 `MyButton` 都有自己独立的 `count`，当每个按钮被点击时，只有被点击按钮的 `count` 才会发生改变：

| <img src="https://react.docschina.org/_next/image?url=%2Fimages%2Fdocs%2Fdiagrams%2Fsharing_data_child.dark.png&w=828&q=75" alt="" style="zoom:33%;" /> | <img src="https://react.docschina.org/_next/image?url=%2Fimages%2Fdocs%2Fdiagrams%2Fsharing_data_child_clicked.dark.png&w=828&q=75" alt="" style="zoom:33%;" /> |
| :------------------------------------------------------------: | :------------------------------------------------------------: |
| 起初，每个 `MyButton` 的 `count` state 均为 `0`              | 第一个 `MyButton` 会将 `count` 更新为 `1`                    |

然而，你经常需要组件 **共享数据并一起更新**。

为了使得 `MyButton` 组件显示相同的 `count` 并一起更新，你需要将各个按钮的 state “向上” 移动到最接近包含所有按钮的组件之中。

在这个示例中，它是 `MyApp`：

| <img src="https://react.docschina.org/_next/image?url=%2Fimages%2Fdocs%2Fdiagrams%2Fsharing_data_parent.dark.png&w=828&q=75"  style="zoom:33%;" /> | <img src="https://react.docschina.org/_next/image?url=%2Fimages%2Fdocs%2Fdiagrams%2Fsharing_data_parent_clicked.dark.png&w=828&q=75"  style="zoom:33%;" /> |
| :------------------------------------------------------------: | :------------------------------------------------------------: |
| 起初，`MyApp` 的 `count` state 为 `0` 并传递给了两个子组件   | 点击后，`MyApp` 将 `count` state 更新为 `1`，并将其传递给两个子组件 |

此刻，当你点击任何一个按钮时，`MyApp` 中的 `count` 都将改变，同时会改变 `MyButton` 中的两个 count。具体代码如下：

首先，将 `MyButton` 的 **state 上移到** `MyApp` 中：

```jsx
export default function MyApp() {

  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }


  return (

    <div>
      <h1>Counters that update separately</h1>
      <MyButton />
      <MyButton />
    </div>
  );
}

function MyButton() {
  // ... we're moving code from here ...
}
```

接着，将 `MyApp` 中的点击事件处理函数以及 **state 一同向下传递到** 每个 `MyButton` 中。你可以使用 JSX 的大括号向 `MyButton` 传递信息。就像之前向 `<img>` 等内置标签所做的那样:

```jsx
export default function MyApp() {

  const [count, setCount] = useState(0);
    
  function handleClick() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>Counters that update together</h1>
      <MyButton count={count} onClick={handleClick} />
      <MyButton count={count} onClick={handleClick} />
    </div>
  );
}
```

使用这种方式传递的信息被称作 **prop**。此时 `MyApp` 组件包含了 `count` state 以及 `handleClick` 事件处理函数，并将它们作为 **prop 传递给** 了每个按钮。

最后，改变 `MyButton` 以 **读取** 从父组件传递来的 prop：

```jsx
function MyButton({ count, onClick }) {
  return (
    <button onClick={onClick}>
      Clicked {count} times
    </button>
  );
}
```

当你点击按钮时，`onClick` 处理程序会启动。每个按钮的 `onClick` prop 会被设置为 `MyApp` 内的 `handleClick` 函数，所以函数内的代码会被执行。该代码会调用 `setCount(count + 1)`，使得 state 变量 `count` 递增。新的 `count` 值会被作为 prop 传递给每个按钮，因此它们每次展示的都是最新的值。这被称为“状态提升”。通过向上移动 state，我们实现了在组件间共享它。
