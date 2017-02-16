React提供清晰简洁的视图层(`View`)解决方案

DOM操作非常昂贵，前端开发中，性能消耗最大的就是DOM操作，而且这部分代码会让整体项目变得难以维护，React将真实的DOM转化为`JavaScript对象树`，也就是`virtual DOM`

React提供了直观的`shouldComponentUpdate`生命周期回调，来减少数据变化后不必要的`virtual DOM`对比过程，以保证性能

`virtual DOM`的构建和更新是在内存中完成的，并不会渲染到真实的DOM中

React中创建的元素可以分为两大类：`DOM元素`和`组件元素`，分别对应着`原生DOM元素`和`自定义元素`。

原生DOM元素用js对象可以描述为

```html
<button class="btn"><em>Confirm</em></button>
```
转化为`json`对象

```js
{
    type: 'button',
    props: {
        className: 'btn',
        children: {
            type: 'em',
            props: {
                children: text
            }
        }
    }
}
```

但如果我们封装一个`Button`,它就可表示为这样

```js
{
    type: 'Button',
    props: {
        color: 'blue',
        children: 'Confirm'
    }
}
```

React组件可以分为两大类
狭义上的组件：UI组件，如`Tabs`组件
广义的组件：即带有业务含义和数据UI组件的组合，在复杂的场景下，一般可以采用分层的思路去处理

React组件的3个组成部分`属性、状态、生命周期`

在合适的情况下，最佳实践是使用无状态组件

`props`：如果顶层组件初始化`props`,那么React会向下遍历整棵树，重新尝试渲染所有的相关子组件
`state`：只关心每个组件自己内部的状态，这些状态只能在组件内部改变

`props本身是不可变的`，如果我们试图改变`props`的原始值，React会给出错误警告

`propTypes`用于规范`props`的类型与必须的状态。如果组件定义了`propTypes`，那么在开发环境下，就会对组件的props值进行类型检查，如果传入的值不匹配，控制台就会报错

React生命周期可以分为两类
- 当组件被挂载或者卸载时
    + propTypes
    + defaultProps
    + componentWillMount
    + render
    + componentDidMount
    + componentWillUnmount
- 当组件接收到新的数据时
    + componentWillReceiveProps
    + shouldComponentUpdate
    + componentWillUpdate
    + componentDidUpdate
    + render

`shouldComponentUpdate`是性能优化的主要方式之一

无状态组件总是会重新渲染，使用`Recompose`库的`pure`方法

```js
const OptimizedComponent = pure(ExpensiveComponent);
```

pure方法做的事情就是将无状态组件转化为`class`语法加上`PureRender`后的组件

使用`ES6`开发React主要的两点不同就是`static propTypes`, `static defaultProps`

`ReactDOM`中的API非常少，只有`findDOMNode`, `unmountComponentAtNode`, `render`

#### findeDOMNode
当组件被渲染到真实的DOM中时，`findDOMNode`返回该React组件实例相对应的DOM节点

#### render
`render`方法将组件挂载到`container`中，并且返回`element`实例。如果是无状态组件，返回的就是`null`
当React组件渲染完成需要更新时，会使用`DOM diff`算法做局部更新，这是React最大的亮点之一

如果将`refs`放到原生的DOM组件上，我们通过`refs`获得的就是DOM节点
如果将`refs`放到React组件上，我们获得的就是组件的实例，就可以调用组件上的实例方法

对于DOM操作，我们不仅可以使用`findDOMNode`获得组件DOM，还可以使用`refs`获得组件内部的DOM

```jsx
class App extends Component {
    componentDidMount() {
        const myComp = this.refs.myComp;
        const dom = findDOMNode(myComp);
    }
    render() {
        return <div><Comp ref='myComp'></div>;
    }
}
```

无论是`findDOMNode`还是`refs`都无法用于无状态组件中，原因是无状态组件挂载时只是方法调用，没有新建实例

并不推荐在React中使用React.findDOMNode来操作DOM.因为这在大部分情况下都打破了封装性，而且通常都能用更清晰的方法在React中构建代码

React基于`virtual DOM`实现了一个`SynthicEvent(合成事件层)`

在React底层，主要做了两件事情:`事件委派`, `自动绑定`
#### 事件委派
React并不会将事件绑定到真实的节点上，而是把时间绑定到结构的最外层，做一个统一的事件监听器，这个事件监听器维持了一个映射来保存所有的组件内部的事件监听和处理函数

#### 自动绑定
在React中每个方法的上下文都会自动指向该组件的实例，即自动绑定`this`到当前组件，但是在`ES6 classes`或者纯函数时，这种自动绑定就不复存在了，我们需要手动绑定this

方法一 `bind`
方法二 `stage-0`草案中的`::`

```jsx
<button onClick={::this.handleClick}>button</button>
```

方法三 `构造函数内声明`

```js
constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
}
```

方法四 `箭头函数`

```jsx
handleClick = () => {
    // ...code
}
// 或者
<button onClick={() => this.handleClick()}>button</button>
```























