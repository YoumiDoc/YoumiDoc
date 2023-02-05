---
title: React
order: 50
group:
  title: 工程化篇
---

## 简介

React 是一个用于构建用户界面的 JavaScript 库.

为什么选择 React?

因为 React 是 Facebook 团队开发的,高效可靠且,并且提倡 All in JavaScript.

> 本文旨在零基础速通,深入还需进一步学习.

快速开始:

```bash
npm i create-react-app -g
create-react-app project-name
```

## JSX

React 的遵循`All in JS`的理念,是像这样利用虚拟 DOM 来创建真实 DOM 的：

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

然而上面的`createElement`太麻烦且抽象了,所以就诞生了 JSX 这种更直观的写法：

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
> 3. 通过{}包裹 js 的内容进行动态解析,如注释`{/* 我是注释内容 */}`
> 4. React 重写了 JS 的事件系统,改用小驼峰写法,像 onClick 这样,并且 React 会将事件自动处理成事件委派模式
> 5. 绑定事件时,加括号会导致在解析时直接调用

经常使用的话,自然也就熟悉了.

可以试着用`create-react-app`快速构建一个 React 项目, 然后敲一下示例 demo：

```jsx
import React from 'react';

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
      {/*记得注释要写花括号,因为这算是JS代码的注释*/}
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

当然, 循环渲染时记得给标签加上`key`属性以便于底层进行`diff`算法.
**在保证元素下标不变时**, 可以使用下标作为`key`属性的值, 通常会比选择其他值更快.

## 组件

上述示例 Demo 中已经出现过了,这里就简单提一下

### 类组件

React 16 之前常采用类组件(Class),大概长下面这样:

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

但是由于其`this`常常让开发者感到迷惑,`extends`在实际开发时基本没用过,更多时候是用 组合 而不是 继承

所以后来开始采用 函数组件 (Function), 本文档之后的内容也以函数组件为主

如果想体验一下用类组件开发, 那么可以参考: [React 官方教程](https://react.docschina.org/tutorial/tutorial.html)

### 函数组件

普通函数和箭头函数都是可以的

```jsx | pure
function Welcome() {
  return <div>Hello</div>;
}

// 使用时: <Welcome></Welcome>
```

但是有个问题,原生 JS 的函数是无**状态**且无**生命周期**的, 状态可以理解为数据,无状态即函数执行完成后,内部声明的变量等数据也不再存在

为了解决这个问题,就引入了 Hooks

## Hooks

是一些名称以`use`开头的特殊函数

### useState

最常用的 Hook, 能让函数组件拥有状态, 当这些状态发生更改时, 默认情况下该组件及其所有子组件都会发生重新渲染.

状态是否发生更改的标准, 遵循 JS 的`===`比较规则.

来看看实际代码:

```jsx
import React, { useState } from 'react';

const Count = () => {
  // const [响应式变量, 处理这个变量的函数(dispatch)] = useState(初始值);
  const [count, setCount] = useState(0);
  return (
    <div>
      <div>响应式状态示例</div>
      <button onClick={() => setCount(count + 1)}> +1</button>
      <div>点了 {count} 次</div>
    </div>
  );
};

const Input = () => {
  const [str, setStr] = useState('');
  return (
    <div>
      <hr />
      <h3>受控组件示例</h3>

      <div>非受控的部分, 即施加控制的一方:</div>
      <input
        onChange={(e) => {
          setStr(e.target.value);
        }}
      />
      <div>受控的部分, 即状态被控制的一方:</div>
      <input value={str} />
    </div>
  );
};

export default () => {
  return (
    <>
      <Count />
      <Input />
    </>
  );
};
```

组件在不同生命周期使用同名 Hook 并非同一个函数, 比如首次渲染挂载时的`useState`调用的是底层的`mountState`方法, 而状态更新时的`useState`调用的则是底层的`updateState`方法.

这意味着初始化值的逻辑只会执行一次.

同时我们也知道, 数组对象等内部数据越多则开销越大, 但函数声明的开销和其内的代码量无关, 所以在初始化值为复杂数据或复杂计算时, 我们可以这样优化：

```jsx | pure
// 优化前：
const [data1, setData1] = useState(complexData);
// 优化后：
const [data1, setData1] = useState(() => complexData);
```

再次强调, 只有在数据比较复杂时才需要写作函数的返回值。
因为函数会占用堆内存, 所以在处理简单数据时也这么做就会得不偿失！

另外, `useState`在 将要 更新时, 底层会开始维护一个更新队列, 等到 执行 更新时, 底层会在处理完这个队列后才进行赋值.
这听起来有些抽象, 结合下面的代码看看应该就能够理解了:

```jsx
import { useState } from 'react';

export default () => {
  const [value1, setValue1] = useState(0);
  const [value2, setValue2] = useState(0);

  const add1 = () => {
    setValue1(value1 + 1); // value为0, 将value + 1计算结果1放入队列, value本身不更新仍然为0
    setValue1(value1 + 1); // value还是0, 重复上述过程
    setValue1(value1 + 1); // 同上
    // 处理完成, 将队列末尾的结果赋值给value, value = 1
  };

  const add2 = () => {
    setValue2(value2 + 1);
    setValue2((preValue) => preValue + 1); // 回调能拿到 本次更新 在更新队列中的 前一个更新 的结果
    setValue2((preValue) => preValue + 1);
  };

  return (
    <>
      <div>点一次触发三个+1:</div>
      <button onClick={add1}>+3?: {value1}</button>
      <hr />
      <div>解决方法:</div>
      <button onClick={add2}>+3!: {value2}</button>
    </>
  );
};
```

上述代码中`add1`的原理是什么呢?

`React`底层具有**批量更新**机制, 会对一次事件上下文中的更新进行压缩处理, 也就是把该上下文中对于同一状态的更新放入一个队列, 最后取队尾元素为结果仅进行一次更新, 然后仅进行一次渲染.这样做的好处是能避免频繁的渲染.

补充一个知识点: 在 React**18**以下的版本中, React 底层在开始处理更新时, 会更改一个布尔变量为`true`表示启用**批量更新**, 结束时则会改为`false`表示关闭.
如果将状态更新操作放入异步函数内, `React`开始处理后, 只会将异步函数放入任务队列, 然后就结束了**批量更新**, 而其中的更新操作就可以逃过**批量更新**.

所以在 React**18**以下的版本中, 我们也可以像下面这样做:

```jsx | pure
// react18以下的版本可以这样做, 新版本不再允许这种“异步逃逸”
// 但除此以外, 底层原理大致是一样的, 所以还是很有学习价值
const [value3, setValue3] = useState(0);

const add3 = () => {
  setTimeout(() => {
    // 等到事件循环阶段才执行更新操作，躲过了批处理更新
    setValue3(value3 + 1);
    setValue3(value3 + 1);
    setValue3(value3 + 1);
  });
};

// react18以下的版本, 如果硬要在异步中触发批量更新
// 那么可以使用react-dom提供的unstable_batchedUpdates方法
// 而新版本则是默认做了这个处理
```

### useEffect

可以为函数组件提供三个生命周期:
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
    };
  });

  // 第二种:
  useEffect(() => {
    console.log('仅在挂载时触发');
  }, []);

  // 第三种:
  useEffect(() => {
    console.log('监听指定状态data, 当data发生变化时候触发');
  }, [data]);

  return <></>;
};
```

### useMemo

memo 即 memory, 能够将函数的计算结果缓存, 使用的场景便是缓存开销较大的计算:
这是加上 useMemo 的:

```jsx
import { useState, useMemo } from 'react';

const expCalculate = (limit) => {
  let num = 0;
  for (let i = 1; i < 1000000000; i++) {
    num++;
  }
  return num;
};

export default () => {
  const [value, setValue] = useState(0);
  // 加了useMemo, 监听内容为空表示任何情况都直接复用上次的计算结果
  // 可以根据需求自行指定监听内容
  const calculate = useMemo(() => expCalculate(), []);
  return (
    <button
      onClick={() => {
        setValue(value + 1);
      }}
    >
      重新渲染 {value}
    </button>
  );
};
```

这是不加 useMemo 的:

```jsx
import { useState, useMemo } from 'react';

const expCalculate = (limit) => {
  let num = 0;
  for (let i = 1; i < 1000000000; i++) {
    num++;
  }
  return num;
};

export default () => {
  const [state, setState] = useState(0);
  // 不加useMemo
  const [value, setValue] = useState(0);
  const calculate = expCalculate();
  return (
    <button
      onClick={() => {
        setValue(value + 1);
        setState(state + 1);
      }}
    >
      重新渲染 {value}
    </button>
  );
};
```

### useCallback

与`useMemo`类似, 但是专门用于缓存函数.

在介绍场景之前,还得说明一个知识点: 父组件状态更新进而重新渲染时, 默认情况下其所有子组件也会重新渲染, 这就造成了额外的开销.这里可以使用`React.memo`方法让子组件在接收参数不变时不进行重新渲染.

但是如果子组件接收到的参数是一个函数时, 由于函数是引用数据类型, 所以父级组件每次重新渲染后传递的都是新的函数, 使得`React.memo`处理过的子组件依然会发生重新渲染.

这时候, 就该使用`useCallback`来将要传递的函数存储下来:

```jsx | pure
import React, { useCallback } from 'react';
// 需要使用memo
const Child = React.memo((props) => {
  return (
    <button
      onClick={() => {
        props.clickEvent();
      }}
    >
      点击
    </button>
  );
});

export default () => {
  // 依赖为空, 表示在任何情况下都会缓存这个函数
  const click = useCallback(() => {
    alert(1);
  }, []);
  return (
    <div>
      <Child clickEvent={click} />
    </div>
  );
};
```

同样地, `React.memo`默认使用浅比较, 你可以在其第二个参数位传入一个回调, 回调会接收到两个参数——上一次的`props`和本次的`props`, 然后自定义比较逻辑。

### useRef

最基本的用处是, 获取到一个元素对应的 DOM 节点:

```jsx
import { useState, useRef } from 'react';

export default () => {
  const scrollRef = useRef(null);
  const [scrollTop, setScrollTop] = useState(0);

  const onScroll = () => {
    if (scrollRef?.current) {
      // 访问DOM节点获取滚动条滚动高度
      setScrollTop(scrollRef?.current.scrollTop);
    }
  };

  return (
    <div>
      <div>滚动条滚动高度：{scrollTop}</div>
      <div
        style={{
          height: 200,
          overflowY: 'auto',
        }}
        ref={scrollRef}
        onScroll={onScroll}
      >
        <div style={{ height: 1000 }}></div>
      </div>
    </div>
  );
};
```

还有另外一个用处是, 可以像`useState`一样维护状态, 并且总能及时拿到最新的值, 但是更改它的值不会引起重新渲染, 所以在不需要对视图做更改的时候可以考虑使用`useRef`:

```jsx | pure
const stateRef = useRef(1);
// 使用stageRef.current即可访问

// 像使用一般变量一样更新数据:
stateRef.current = 2;
```

更多 Hook 建议查阅官方文档做进一步学习

### Hook 使用规范

只在当前组件的作用域最顶层使用 Hook,不要在循环、条件语句、函数等次级作用域中使用,如果必须要有条件语句等,那么可以考虑将其放到 effect 内部

```jsx | pure
useEffect(function persistForm() {
  // 👍 将条件判断放置在 effect 中
  if (name !== '') {
    localStorage.setItem('formData', name);
  }
});
```

为什么不可以把 Hook 放到循环, 条件等语句中呢?
因为 Hook 底层是用一个不具名节点链表来维护执行顺序的, 所以其执行顺序必须被严格保证.

## 组件通信

通过阅读了上述章节的代码, 就应该已经了解到其中几种方式了.
React 本身提供的方式有:

- props, child(父子间通信)
- Context/Provider(跨组件通信)

### props

通过参数传递, 可以让父级向子级传递数据.
如果这个参数是一个能修改父级状态的函数, 那么就能让子级向父级传递数据.

```jsx
import { useState } from 'react';

const Child = (props) => {
  const { parentState, setParentState } = props;
  const style = { border: '1px solid red' };

  return (
    <div style={style}>
      <button onClick={() => setParentState(parentState + 1)}>
        子级组件的按钮
      </button>
      {/* 访问组件嵌套形式传递的数据 */}
      {props.children}
    </div>
  );
};

export default () => {
  const [state, setState] = useState(0);
  return (
    <div>
      <div>父级组件的状态: {state}</div>
      {/* 父级向子级传递参数 */}
      <Child parentState={state} setParentState={setState}>
        <div>组件嵌套的形式亦可传递数据</div>
        <div>组件嵌套传递的数据可以通过props.children访问到</div>
      </Child>
    </div>
  );
};
```

### Context/Provider

`Context`是一个"传送门", 通向的"异世界"`Provider`

```jsx
import React, { useState } from 'react';

// 创建"传送门"实体
const Context = React.createContext();
const GrandChild = () => {
  // 开启"传送门": 调用useContext方法, 访问Provider的状态
  const { parentState, setParentState } = React.useContext(Context);
  return (
    <div>
      <div>我是GrandChild!!!</div>
      <div>Context传送门中出现的是: {parentState}</div>
    </div>
  );
};

const Child = () => {
  return (
    <div>
      <hr />
      <div>我是Child!!!</div>
      <hr />
      <GrandChild />
    </div>
  );
};

const Parent = () => {
  const [parentState, setParentState] = useState(
    <span style={{ color: 'red' }}>是Parent的状态!!!</span>,
  );
  {
    /* Provider向其所有子元素提供数据 */
  }
  return (
    <Context.Provider value={{ parentState, setParentState }}>
      <div>
        <div>我是Parent!!!</div>
        <Child />
      </div>
    </Context.Provider>
  );
};

export default Parent;
```

## 继续学习

本想继续补充一些关于底层源码相关的进阶内容, 但是笔者对于深入浅出地阐述这部分内容不具有信心, 而且这也超越了本文用于新手教学的作用范围, 所以提供一些优秀资料供读者自行学习.

[React 官网](https://reactjs.org/)
[React 技术揭秘](https://react.iamkasong.com/hooks/prepare.html)
