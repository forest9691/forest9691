

## Children

Children提供了以下几种API

1. Children.map()
2. Children.forEach()
3. Children.count()
4. Children.only()
5. Children.toArray()

### 组合组件

需求：希望对每一个`<p>`标签增加一个`<div>`标签。可以在`<div>`中使用`Children.map()`进行包装子节点

```jsx
export default function App() {
  return (
    <RowList>
        <p>这是第一项。</p>
        <p>这是第二项。</p>
        <p>这是第三项。</p>
    </RowList>
  );
}
```

以上方向可以转换成使用`<Row>`来包裹`<p>`, 在`<Row>`组件内使用`<div>`包裹。

```jsx
export default function App() {
  return (
    <RowList>
      <Row>
        <p>这是第一项。</p>
      </Row>
      <Row>
        <p>这是第二项。</p>
      </Row>
      <Row>
        <p>这是第三项。</p>
      </Row>
    </RowList>
  );
}
```

> 上面这个属于是静态的子组件

### 接收对象数组作为参数

使用属性传递数据，在组件内部使用`props.rows`对数据进行处理包裹。

```jsx
import { RowList, Row } from './RowList.js';

export default function App() {
  return (
    <RowList rows={[
      { id: 'first', content: <p>这是第一项。</p> },
      { id: 'second', content: <p>这是第二项。</p> },
      { id: 'third', content: <p>这是第三项。</p> }
    ]} />
  );
}
```

> 上面属性属于是动态的子组件

### 调用渲染属性以自定义渲染，prop render

```jsx
import TabSwitcher from './TabSwitcher.js';

export default function App() {
  return (
    <TabSwitcher
      tabIds={['first', 'second', 'third']}
      getHeader={tabId => {
        return tabId[0].toUpperCase() + tabId.slice(1);
      }}
      renderContent={tabId => {
        return <p>This is the {tabId} item.</p>;
      }}
    />
  );
}

```

> 上面属性属于是动态的子组件, 扩展性更好



### 总结

通过以上三个替换使用`Children`的例子来看，原本有五个`API`,由于使用上面的的方式可以替换掉`forEach()、map()`二个，其它的三个可以由替换掉的这二个来获得。比如要获取子组件的个数可以使用`props.rows.length`，再比如获取是否唯一的子组件可以使用`props.rows.length === 1`来获取。还有一个是`Children.toArray()`这个不需要进行转换了，`props.rows`本来就定义为是一个数据。

以上三种代码片段也是三种组件编码风格，还有一种是高阶组件编码风格。
