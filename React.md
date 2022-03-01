### React 框架原理知识点

- 1.React 渲染页面分为两个阶段：
- 调度阶段（reconciliation）：在这个阶段 React 会更新数据生成新的 Virtual DOM，然后通过 Diff 算法，快速找出需要更新的元素，放到更新队列中去，得到新的更新队列。(“Render 阶段”-纯净且不包含副作用。可能会被 React 暂停，中止或重新启动。)
- 渲染阶段（commit）：这个阶段 React 会遍历更新队列，将其所有的变更一次性更新到 DOM 上(“Commit 阶段”可以使用 DOM，运行副作用，安排更新。)

- 不可突变的数据结构：不可改变原有的数据结构
  Immutable Data 就是一旦创建，就不能再被更改的数据。对 Immutable 对象的任何修改操作都会返回一个新的 Immutable 对象。Immutable 实现的原理是 Persistent Data Structure（持久化数据结构），也就是使用旧数据创建新数据时，要保证旧数据同时可用且没有发生变化。同时为了避免 deepCopy 把所有节点都复制一遍带来的性能损耗，Immutable 使用了 Structural Sharing（结构共享），即如果对象树中一个节点发生变化，只修改这个节点和受它影响的父节点，其它节点则进行共享。

  浅比较：比较 Object.keys(state | props) 的长度是否一致，每一个 key 是否两者都有，并且是否是一个引用，也就是只比较了第一层的值，确实很浅，所以深层的嵌套数据是对比不出来的。
  从数组中截取部分作为新状态时，应使用 slice 方法;当从数组中过滤部分元素后，作为新状态时，使用 filter 方法。不应该使用 push、pop、shift、unshift、splice 等方法修改数组类型的状态，因为这些方法都是在原数组的基础上修改的。应当使用不会修改原数组而返回一个新数组的方法，例如 concat、slice、filter 等。 3. 普通对象
  对象也是可变类型，修改对象类型的状态时，应该保证不会修改原来的状态。可以使用 ES6 的 Object.assign 方法或者对象扩展语法
  请注意，如果父组件导致组件重新渲染，即使 props 没有更改，也会调用此方法，因为 diff 算法会去递归深层次比较
  ❤react 数组更新时，浅比较时父组件会更新，数组作为 props 传入子组件时，子组件不会更新 React.PureComponent

- diff 算法?2 条规则

拥有相同类的 2 个组件将生成相似的树形结构，不同类得 2 个生成不同的树形结构，将卸载原来的 dom 树
可以为列表元素添加唯一 key 标志，该元素在不同的渲染过程中保持不变
把树形结构按照层级分解，只比较同级元素。
给列表结构的每个单元添加唯一的 key 属性，方便比较。
React 只会匹配相同 class 的 component（这里面的 class 指的是组件的名字）
合并操作，调用 component 的 setState 方法的时候, React 将其标记为 dirty.到每一个事件循环结束, React 检查所有标记 dirty 的 component 重新绘制.
选择性子树渲染。开发人员可以重写 shouldComponentUpdate 提高 diff 的性能。
采用递归的方式循环节点是否需要跟新，子组件是不是需要更新，看传入的 props。
① 不同的节点类型 重新渲染
②dom 节点比较属性、内容
③ 自定义组件比较状态

- setState-React 的批处理机制更新机制 1.生命周期和合成事件中
  在 React 的生命周期和合成事件中， React 仍然处于他的更新机制中，这时无论调用多少次 setState，都会不会立即执行更新，而是将要更新的·存入 \_pendingStateQueue，将要更新的组件存入 dirtyComponent。
  当上一次更新机制执行完毕，以生命周期为例，所有组件，即最顶层组件 didmount 后会将批处理标志设置为 false。这时将取出 dirtyComponent 中的组件以及 \_pendingStateQueue 中的 state 进行更新。这样就可以确保组件不会被重新渲染多次。
  componentDidMount(){
  this.setState({
  index:this.state.index+1
  });
  console.log('state=',this.state.index);
  }
  所以，如上面的代码，当我们在执行 setState 后立即去获取 state，这时是获取不到更新后的 state 的，因为处于 React 的批处理机制中， state 被暂存起来，待批处理机制完成之后，统一进行更新。所以。setState 本身并不是异步的，而是 React 的批处理机制给人一种异步的假象。
  componentDidMount(){
  settimeout(()=>{
  this.setState({
  index:this.state.index+1
  });
  console.log('state=',this.state.index);
  },0);
  }
  当我们在异步代码中调用 setState 时，根据 JavaScript 的异步机制，会将异步代码先暂存，等所有同步代码执行完毕后在执行，这时 React 的批处理机制已经走完，处理标志设被设置为 false，这时再调用 setState 即可立即执行更新，拿到更新后的结果。
  在原生事件中调用 setState 并不会出发 React 的批处理机制，所以立即能拿到最新结果
  最佳实践
  setState 的第二个参数接收一个函数，该函数会在 React 的批处理机制完成之后调用，所以你想在调用 setState 后立即获取更新后的值，请在该回调函数中获取。
  this.setState({
  index:this.state.index+1
  },()=>{
  console.log('state=',this.state.index);  
  });
  批处理合并操作(有时连续多次 setState 只有一次生效)
  componentDidMount(){
  this.setState({
  index:this.state.index+1
  },()=>{
  console.log('state=',this.state.index);
  });

}
两次打印出的结果是相同的
原因就是 React 会批处理机制中存储的多个 setState 进行合并，来看下 React 源码中的 \_assign 函数，类似于 Object 的 assign：
Object.assign({nextState,{index:state.index+1},{index:state.index+1}});
assign 函数中对函数做了特殊处理，处理第一个参数传入的是函数，函数的参数 preState 是前一次合并后的结果，所以计算结果是准确的
componentDidMount(){
this.setState({(state,props)=>({
index:state.index+1
}),()=>{
console.log('state=',this.state.index);
});
this.setState({(state,props)=>({
index:state.index+1
}),()=>{
console.log('state=',this.state.index);
});
}
具体表现为，多次同步执行的setState，会进行合并，类似于Object.assign，相同的key，后面的会覆盖前面的
当遇到多个setState调用时候，会提取单次传递setState的对象，把他们合并在一起形成一个新的单一对象，并用这个单一的对象去做setState的事情，就像Object.assign的对象合并后一个
React会对多次连续的 setState进行合并，如果你想立即使用上次 setState后的结果进行下一次 setState，可以让 setState 接收一个函数而不是一个对象。这个函数用上一个 state 作为第一个参数，将此次更新被应用时的 props 做为第二个参数
具体表现为，多次同步执行的setState，会进行合并，类似于Object.assign，相同的key，后面的会覆盖前面的
当遇到多个setState调用时候，会提取单次传递setState的对象，把他们合并在一起形成一个新的单一对象，并用这个单一的对象去做setState的事情，就像Object.assign的对象合并后一个
. setState在原生事件，setTimeout，setInterval，Promise等异步操作中，state会同步更新
异步操作中setState，即使在React的钩子或合成事件中，state都不会批量更新，而是会同步更新，多次连续操作setState，每次都会re-render，state会同步更新

2.React fiber机制
问题：如果数据到达的速度快于帧速率,可以合并并批量更新
在处理ui时，问题是如果一次执行太多的工作，会导致动画掉帧
目标：主要目标是使react能够利用调度的优势，功能是增量渲染：将渲染工作分割成块并将其分散到多个帧上的能力，react fiber的目标是提高其在动画、布局和手势等领域的适用性
示例：我们可以将来自用户交互的工作（例如由按钮单击引起的动画）优先于不太重要的后台工作（例如渲染刚从网络加载的新内容），以避免丢失帧。
实现方式：反应组件作为数据函数的概念，为不同类型的工作分配优先级，通过堆栈调度
React Fiber的概念：Fiber是一个比线程还小的控制粒度，ReactV16的一个重大改变就是使用了React Fiber来代替之前的Stack reconciler用以解决渲染造成的卡顿原因。Stack reconciler是通过自顶向下的同步方式来处理任务，整个过程不会中断，若过程中时间较长，浏览器主线程被阻断，就会出现卡顿现象。为解决这个问题，引入了React Fiber的概念，它的主要原理就是将一个任务分割成多个片段，每个片段执行完以后，可以给其他任务执行的机会，线程不会被独占。
3.React如何实现自己的事件机制
组件挂载、更新时：

通过 lastProps、 nextProps判断是否新增、删除事件分别调用事件注册、卸载方法。
调用 EventPluginHub的 enqueuePutListener进行事件存储
获取 document对象。
根据事件名称（如 onClick、 onCaptureClick）判断是进行冒泡还是捕获。
判断是否存在 addEventListener方法，否则使用 attachEvent（兼容IE）。
给 document注册原生事件回调为 dispatchEvent(统一的事件分发机制）。

事件初始化：
EventPluginHub负责管理 React合成事件的 callback，它将 callback存储在 listenerBank中，另外还存储了负责合成事件的 Plugin。
获取绑定事件的元素的唯一标识 key。
将 callback根据事件类型，元素的唯一标识 key存储在 listenerBank中。
listenerBank的结构是： listenerBank[registrationName][key]。

触发事件时：
触发 document注册原生事件的回调 dispatchEvent
获取到触发这个事件最深一级的元素
遍历这个元素的所有父元素，依次对每一级元素进行处理。
构造合成事件。
将每一级的合成事件存储在 eventQueue事件队列中。
遍历 eventQueue。
通过 isPropagationStopped判断当前事件是否执行了阻止冒泡方法。
如果阻止了冒泡，停止遍历，否则通过 executeDispatch执行合成事件。
释放处理完成的事件。
React在自己的合成事件中重写了 stopPropagation方法，将 isPropagationStopped设置为 true，然后在遍历每一级事件的过程中根据此遍历判断是否继续执行。这就是 React自己实现的冒泡机制。

为何React事件要自己绑定this
在上面提到的事件处理流程中， React在 document上进行统一的事件分发， dispatchEvent通过循环调用所有层级的事件来模拟事件冒泡和捕获。

在 React源码中，当具体到某一事件处理函数将要调用时，将调用 invokeGuardedCallback方法。
可见，事件处理函数是直接调用的，并没有指定调用的组件，所以不进行手动绑定的情况下直接获取到的 this是不准确的，所以我们需要手动将当前组件绑定到 this上。
原生事件和React事件的区别？

React 事件使用驼峰命名，而不是全部小写。
通过 JSX , 你传递一个函数作为事件处理程序，而不是一个字符串。
在 React 中你不能通过返回 false 来阻止默认行为。必须明确调用 preventDefault。
React的合成事件是什么？

React 根据 W3C 规范定义了每个事件处理函数的参数，即合成事件。

事件处理程序将传递 SyntheticEvent 的实例，这是一个跨浏览器原生事件包装器。它具有与浏览器原生事件相同的接口，包括 stopPropagation() 和 preventDefault()，在所有浏览器中他们工作方式都相同。

React合成的 SyntheticEvent采用了事件池，这样做可以大大节省内存，而不会频繁的创建和销毁事件对象。

另外，不管在什么浏览器环境下，浏览器会将该事件类型统一创建为合成事件，从而达到了浏览器兼容的目的。
React和原生事件的执行顺序是什么？可以混用吗？

React的所有事件都通过 document进行统一分发。当真实 Dom触发事件后冒泡到 document后才会对 React事件进行处理。

所以原生的事件会先执行，然后执行 React合成事件，最后执行真正在 document上挂载的事件

React事件和原生事件最好不要混用。原生事件中如果执行了 stopPropagation方法，则会导致其他 React事件失效。因为所有元素的事件将无法冒泡到 document上，导致所有的 React事件都将无法被触发