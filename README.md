<div>
  <p align="center"><img src="https://github.com/flat-dev-ti/Flat/blob/master/doc/logo.png" alt="fre logo" width="180"></p>
  <h1 align="center" style="font-size:100px;">Flat</h1>
</div>
<p align="center">A lightweight MVVM framework base on TypeScript.</p>
<p align="center">
  <img src="https://img.shields.io/github/license/flat-dev-ti/Flat.svg?style=flat-square">
  <img src="https://img.shields.io/badge/TypeScript-3.2-blue.svg?style=flat-square">
</p>

# Flat

A lightweight MVVM framework base on TypeScript.

dev...

## Render

在执行 render 的时候，会将所有的数组抽成扁平，flat 会认为，children 就只有 Text 节点和 \_Elememt 节点，相对来说，抽成扁平，在性能上也会得到提升。

为了能精确定位到更新的元素，在 Component 的原型上附加了类型为 symbol 的 \_key，用来唯一标识一个组件

不必关心节点的 key, flat 在初次解析的时候，附加了 component 的 key，key 用了 Symbol 类型，为了保证对比的是同一个 key，于是把 key 附在了组件上。

为直接定位更改的元素，flat 采用了`ES6`的 `Proxy/ Reflect`，因为已经知道了渲染的元素的位置，变更的组件的子组件并不会重新调用 render，大幅度提高了渲染的性能

## About Decorator

TypeScript 的 Decorator 不能支持改变类型，这样只有使用类 React 的继承 FlatComponent 来完成对类型的约束，使 Decorator 不能专注的完成一件事情，在用户看来，除了使用 Decorator 还要做额外的工作是在太麻烦了，于是将原来的 FlatComponent 装饰器推掉。

## String Template Compiler

对于 string template 变量的求值，默认认定为 Text 节点，在解析模版完成后，再进行值的插入。
这里有一个例外，当变量为数组时，会在解析模版之前就对其进行求值，然后再把值拼接到模版，flat 会认为，数组返回的也是一个迭代模版。

```typescript
import Flat, { FlatComponent, Prop, _Element, Ele, Children } from "../../dist";

class Greeter extends FlatComponent {
  @Children()
  names!: string[];

  render(): JSX.Element {
    let greeters = names.map(name => <p>hello {name}</p>);

    return <div>{greeters}</div>;
  }
}

let names = ["A", "B", "C"];
let input = (
  <div id="foo">
    <Greeter>{names}</Greeter>
  </div>
);

new Ele(input).bindDOM(document.querySelector("#root"));
```

TODO：

- [x] 对于自定义组件的求值策略
- [x] 组件钩子的调用时机以及确定钩子函数
- [x] ~~@FlatComponent 实现~~
- [x] Props 实现，以及 @Prop 的实现
- [ ] virtual DOM 的 Diff 算法
