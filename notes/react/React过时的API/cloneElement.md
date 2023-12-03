

## cloneElement(element, props, ...children)

`cloneElement` 允许你基于 `element` 创建一个新的 React 元素，但新元素具有不同的 `props` 和 `children`

```js
const clonedElement = cloneElement(element, props, ...children);

/*
element: element 参数必须是一个有效的 React 元素;
props: props 参数必须是一个对象或 null;
children: 零个或多个子节点
*/
```

```jsx
import { cloneElement } from 'react';

// ...
const clonedElement = cloneElement(
  <Row title="Cabbage">
    Hello
  </Row>,
  { isHighlighted: true },
  'Goodbye'
);

console.log(clonedElement); // <Row title="Cabbage" isHighlighted={true}>Goodbye</Row>
```

### 返回值 

`cloneElement` 返回一个具有一些属性的 React element 对象：

- `type`：与 `element.type` 相同。
- `props`：将 `element.props` 与你传递的 `props` 浅合并的结果。
- `ref`：原始的 `element.ref`，除非它被 `props.ref` 覆盖。
- `key`：原始的 `element.key`，除非它被 `props.key` 覆盖。

### 通过 props 传递数据 

接受类似 `renderItem` 这样的 *render prop* 代替 `cloneElement` 的用法。在这里，`List` 接收 `renderItem` 作为 props。`List` 为数组每一项调用 `renderItem`，并传递 `isHighlighted` 作为参数：

```jsx
export default function List({ items, renderItem }) {

  const [selectedIndex, setSelectedIndex] = useState(0);

  return (
    <div className="List">
      {items.map((item, index) => {
        const isHighlighted = index === selectedIndex;
        return renderItem(item, isHighlighted);
      }
    )
  }
```

`renderItem` 属性称为“渲染属性”，因为它是决定如何渲染某些内容的属性。例如，你可以传递一个 `renderItem` 实现使用给定的 `isHighlighted` 值呈现 `<Row>`：

```jsx
<List
  items={products}
  renderItem={(product, isHighlighted) =>
    <Row
      key={product.id}
      title={product.title}
      isHighlighted={isHighlighted}
    />
  }
/>
```

> 渲染属性可以替代 `cloneElement()`、`Children`

### 通过 context 传递数据 

`cloneElement` 的另一种替代方法是 [通过 context 传递数据](https://react.docschina.org/learn/passing-data-deeply-with-context)。

例如，你可以调用 [`createContext`](https://react.docschina.org/reference/react/createContext) 来定义一个 `HighlightContext`：

```
export const HighlightContext = createContext(false);
```

`List` 组件可以将其呈现的每个 item 传递到 `HighlightContext` provider 中：

```jsx
export default function List({ items, renderItem }) {

  const [selectedIndex, setSelectedIndex] = useState(0);

  return (
    <div className="List">
      {items.map((item, index) => {
        const isHighlighted = index === selectedIndex;
        return (
          <HighlightContext.Provider key={item.id} value={isHighlighted}>
			<!--
				方法调用得到一个组件
    		-->
            {renderItem(item)}

          </HighlightContext.Provider>

        );

      })}
```

通过这种方法，`Row` 不需要接收 `isHighlighted`属性，因为它可以从 context 中读取：

```jsx
export default function Row({ title }) {
  const isHighlighted = useContext(HighlightContext);
  // ...
}    
```

这允许调用组件时无需关心是否将 `isHighlighted` 传递给了 `<Row>`：

```jsx
<List
  items={products}
  renderItem={product =>
    <Row title={product.title} />
  }
/>
```

### 将逻辑提取到自定义 Hook 中 

你可以尝试的另一种方法是将“非视觉”部分的逻辑提取到你的自定义 Hook 中，并使用 Hook 的返回值来决定渲染什么。例如，你可以编写一个 `useList` 自定义 Hook，如下所示：

```jsx
import { useState } from 'react';

export default function useList(items) {

  const [selectedIndex, setSelectedIndex] = useState(0);

  function onNext() {
    setSelectedIndex(i =>
      (i + 1) % items.length
    );
  }

  const selected = items[selectedIndex];
  return [selected, onNext];
}
```

然后你可以像这样使用它：

```jsx
export default function App() {

  const [selected, onNext] = useList(products);

  return (
    <div className="List">
      {products.map(product =>
        <Row
          key={product.id}
          title={product.title}
          isHighlighted={selected === product}
        />
      )}
      <hr />
      <button onClick={onNext}>
        下一步
      </button>
    </div>
  );
}
```
