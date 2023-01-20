---
title: React
order: 50
group:
  title: 工程化篇
---

## React

为什么选择 React?

因为 React 是 Facebook 团队开发的，高效可靠且，并且提倡 All in JavaScript.

本文旨在速通，深入还需进一步学习.

### 学习资料

[React 官网](https://ak.hypergryph.com/)

[React 技术揭秘](https://react.iamkasong.com/)

### JSX

React 的遵循`All in JS`的理念，是像这样利用虚拟 DOM 来创建真实 DOM 的：

```jsx | pure
React.createElement(
  ComponentA,
  { attr1: 'A', attr2: 0 },
  React.createElement(
    ComponentB1,
    { attr1: 'B', attr2: 1 },
    React.createElement(ComponentC1, propsC1),
    React.createElement(ComponentC2, propsC2),
  ),
  React.createElement(
    'h1',
    { style: { backgroundColor: 'red' } },
    `Hello ${varWorld}`,
  ),
);
```

然而上面的`createElement`太麻烦且抽象了，所以就诞生了 JSX 这种更直观的写法：

```jsx | pure
<ComponentA attr1="A" attr2={0}>
  <ComponentB1 attr1="B" attr2={1}>
    <ComponentC1 {...propsC1} />
    <ComponentC2 {...propsC2} />
  </ComponentB1>
  <h1 style={{ backgroundColor: 'red' }}>Hello {varWorld}</h1>
</ComponentA>
```

使用 JSX 需要有如下注意事项：

> 1. 标签的 class 需要写作 className
> 2. 需要有一个最外层元素包裹, 可以用`<></>`包裹避免多余的渲染
> 3. 通过{}包裹 js 的内容进行动态解析，如注释`{/* 我是注释内容 */}`
> 4. React 重写了 JS 的事件系统，改用小驼峰写法，像 onClick 这样，并且 React 会将事件自动处理成事件委派模式
> 5. 绑定事件时，加括号会导致在解析时直接调用

经常使用的话，自然也就熟练了。

示例 demo：

```jsx
import React from 'react';
import { render } from 'react-dom';

const YoumiComponent = () => {
  return (
    <div>
      <hr />
      <div>这是组件: YoumiComponent</div>
    </div>
  );
};

export default () => {
  const name = '我是一个页面Demo, 点击下方按钮查看源码';
  const site = 'https://github.com/CoderSerio/Youmi/';
  const field = ['前端', '后端', '安全开发', '更多方向'];
  const style = { backgroundColor: '#royalblue', color: 'pink' };
  return (
    <>
      {/*记得注释要写花括号，因为这算是JS代码的注释*/}
      <h2 className="title">{name}</h2>
      <a style={style} href={site}>
        这是一个有颜色的链接
      </a>
      <div>利用数组的map方法进行循环渲染:</div>
      {field.map((item, index) => {
        return <li key={index}>{item}</li>;
      })}

      <div>
        这是一个看不见的条件渲染:
        {false && <div>看不见我捏</div>}
      </div>
      <YoumiComponent />
    </>
  );
};
```

### 组件

上述示例 Demo 中已经出现过了，这里就简单提一下

#### 类组件

React 16 之前常采用类组件(Class)，大概长下面这样:

```jsx | pure
import React from 'react';

class Welcome extends React.Component {
  constructor() {
    super();
    this.state = {
      data: 123,
      code: 'hhh',
    };
  }
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

// 使用组件时,直接当作标签来写即可: <Welcome></Welcome>;
```

但是由于其`this`常常让开发者感到迷惑，`extends`在实际开发时基本没用过，更多时候是用 组合 而不是 继承

所以后来开始采用 函数组件 (Function)

本文档之后的内容也以函数组件为主

#### 函数组件

普通函数和箭头函数都是可以的

```jsx | pure
function Welcome() {
  return <div>Hello</div>;
}

// 使用时: <Welcome></Welcome>
```

但是有个问题，原生 JS 的函数是无**状态**且无**生命周期**的, 状态可以理解为数据，无状态即函数执行完成后，内部声明的变量等数据也不再存在

为了解决这个问题，就引入了 Hooks

### Hooks

是一些名称以`use`开头的特殊函数

#### useState
字面意思
最常用的 Hook, 直接来看看使用情景:

```jsx
import React, { useState } from 'react';

const Count = () => {
  // const [响应式变量, 处理这个变量的函数] = useState(初始值);
 const [count, setCount] = useState(0);
  return (
    <div>
      <div>响应式状态示例</div>
      <button onClick={() => setCount(count + 1)}> +1</button>
      <div>点了 {count} 次</div>
    </div>
  );
}

const Input = () => {
  const [str, setStr] = useState('');
  return (
    <div>
      <hr />
      <div>受控组件示例</div>
      <input onChange={(e) => {setStr(e.target.value);}} />
      <div>被控制的部分:</div>
      <input value={str}/>
    </div>
  )
}

export default () => {
  return (
    <>
      <Count />
      <Input />
    </>
  );
}
```

#### useEffect
可以为函数组件提供三个生命周期
`mounted`, `updated` 和 `unmounted`

下面是一些常见的写法:
```jsx | pure
import { useEffect, useState } from 'react';

export default () => {
  const [data, setData] = useState('xxx');
  
  // 第一种: 
  useEffect(() => {
    console.log('挂载, 状态更新时触发');
    return () => {
      console.log('销毁时触发');
    }
  });

  // 第二种:
  useEffect(() => {
    console.log('仅在挂载时触发');
  }, []);

  // 第三种:
  useEffect(() => {
    console.log('监听指定状态data, 当data发生变化时候触发');
  }, [data]);

  return (<></>)
}
```

#### useMemo和useCallback
memo即memory, 也可以猜到这个hook是和缓存相关的

```jsx
import { useState, useMemo } from 'react';

const MemoryCalculate = (props) => {
  // 触发函数次数
  const [cnt, setCnt] = useState(0);
  // return useMemo(() => {
  //   // 如果触发了这个回调, 那么+1
  //   setCnt(cnt + 1);
    return (
      <>
        <hr/>
          <div>这是MemoryCalculate组件</div>
          <div>触发回调的次数: {cnt}</div>
          <div>计算结果: {props.data}</div>
        <hr/>
      </>
    )
  // }, [props.data]);
}

export default () => {
  const [value, setValue] = useState('');
  const [value2, setValue2] = useState('')
  
  // 进行某种计算操作
  const calc = () => {
    if (typeof +value !== 'number') {
      setValue2(-1);
    } else {
      setValue2(value * 2);
    }
  }
  
  return (
    <>
      <input onChange={(e) => {setValue(e.target.value)}} />
      <button onClick={calc}>计算</button>
      <MemoryCalculate data={value2} />
    </>
  )
}
```

### 组件通信

### 并发渲染
