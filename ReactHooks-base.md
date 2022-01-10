### React框架Hook基础知识点
#### 原理介绍
Hook 是一些可以在函数组件里“钩入” React state 及生命周期等特性的函数,React 函数组件上添加内部 state
它能在函数组件中执行副作用，并且它与 class 中的生命周期函数极为类似
const memo = function(func) {
    let cache = {};
    return function(key) {
        if(!cache(key)) {
            cache[key] = func.apply(this, arguments);
        }
        return cache[key];
    }
}
memo(testFunc)(arg);


① 与状态组件对比
需要清除的和不需要清除的
比如发送网络请求，手动变更 DOM，记录日志，这些都是常见的无需清除的操作。因为我们在执行完这些操作之后，就可以忽略他们了。让我们对比一下使用 class 和 Hook 都是怎么实现这些副作用的
Hook 使用了 JavaScript 的闭包机制
React 保证了每次运行 effect 的同时，DOM 都已经更新完毕
每次我们重新渲染，都会生成新的effect，替换掉之前的。某种意义上讲，effect 更像是渲染结果的一部分 —— 每个 effect “属于”一次特定的渲染
使用 useEffect 调度的 effect 不会阻塞浏览器更新屏幕，这让你的应用看起来响应更快。大多数情况下，effect 不需要同步地执行。
比如发送网络请求，手动变更 DOM，记录日志，这些都是常见的无需清除的操作。
发布、订阅需要消除
useEffect 的设计是在同一个地方执行。如果你的 effect 返回一个函数，React 将会在执行清除操作时调用它：
使用 Hook 其中一个目的就是要解决 class 中生命周期函数经常包含不相关的逻辑，但又把相关逻辑分离到了几个不同方法中的问题。
除此之外，请记得 React 会等待浏览器完成画面渲染之后才会延迟调用
它类似 class 组件的this.setState，但是它不会把新的 state 和旧的 state 进行合并


#### 使用知识点
##### Hooks 需要遵循什么规则?
为了使用 hooks，你需要遵守两个规则：
* 仅在顶层的 React 函数调用 hooks。也就是说，你不能在循环、条件或内嵌函数中调用 hooks。这将确保每次组件渲染时都以相同的顺序调用 hooks，并且它会在多个 useState 和 useEffect 调用之间保留 hooks 的状态。
* 仅在 React 函数中调用 hooks。例如，你不能在常规的 JavaScript 函数中调用 hooks。
#####  生命周期方法要如何对应到 Hook？
* constructor：函数组件不需要构造函数。你可以通过调用 useState 来初始化 state。如果计算的代价比较昂贵，你可以传一个函数给 useState。
* getDerivedStateFromProps：改为 在渲染时 安排一次更新。
* shouldComponentUpdate：详见 下方 React.memo.
* render：这是函数组件体本身。
* componentDidMount, componentDidUpdate, componentWillUnmount：useEffect Hook 可以表达所有这些(包括 不那么 常见 的场景)的组合。
* getSnapshotBeforeUpdate，componentDidCatch 以及 getDerivedStateFromError：目前还没有这些方法的 Hook 等价写法，但很快会被添加。

##### useState
1.useState不适合复杂对象的更改（不会进行合并操作）(在React中state是不可变的。然而，this是可变的。React Hooks在某一个特定渲染中state和props是与其相绑定的，然而类组件并不是。)
我们推荐把 state 切分成多个 state 变量，每个变量包含的不同值会在同时发生变化。
独立的 state 变量拆分开还有另外的好处。这使得后期把一些相关的逻辑抽取到一个自定义 Hook 变得容易
如果 state 的逻辑开始变得复杂，我们推荐 用 reducer 来管理它，或使用自定义 Hook。
因为useState不能像setState那样进行合并更新，当使用useState第二个参数进行数据更新的时候，必须传入一个完整的结构，而不仅仅只是改变的那一部分。
①封装成一个通用的hook
const [person, setPerson] = useState({age:12,name:'A',id:000001})

const changeAge = (age) => {
  setPerson(prevPerson => ({...prevPerson, age}))
}
function useSetState(initial = {}) {
  const [state, saveState] = useState(initial)
  const setState = useCallback((newState) => {
    saveState(prev => ({...prev, ...newState}))
  }, [])

  return [state, setState]
}
使用:
const [person, setPerson] = useSetState({age:12,name:'A',id:000001})

const changeAge = (age) => {
  setPerson({age})
}
②如果状态比较复杂，推荐使用useReducer
③const [state, setState] = useState({});
setState(prevState => {
  // 也可以使用 Object.assign
  return {...prevState, ...updatedValues};
});
如果你的更新函数返回值与当前 state 完全相同，则随后的重渲染会被完全跳过。
2.useState异步回调的问题
当使用usestate对数据进行更新，并不能立刻获取到最新的数据。(想获取到最新值)
react hooks中setState 确实拿到上一次的缓存,是因为，函数在每次渲染时也是独立的。这就是 Capture Value 特性，后面遇到这种情况就不会一一展开，只描述为 “此处拥有 Capture Value 特性”。
Capture Value 概念的解释：每次 Render 的内容都会形成一个快照并保留下来，因此当状态变更而 Rerender 时，就形成了 N 个 Render 状态，而每个 Render 状态都拥有自己固定不变的 Props 与 State。
一、配合useEffect使用
const [modelStatus, setModelStatus] =useState("");

function statusHandleChange(val) {
        setModelStatus(val);
        search();
    }

function search() {
    
	console.log(modelStatus);   // 这里拿到的值 是上一次的值,不是最新的
	props.callApi(modelStatus);
}

解决方法是:
第一种:

function statusHandleChange(val) {
        setModelStatus(val);
        // 直接把参数的值 传进去 拿到的就是最新的了
        search(val);
    }

function search(value) {
    
	console.log(value);  
	props.callApi(value);
}

第二种方法:

const [modelStatus, setModelStatus] =useState("");
const modelStatusRef = useRef(null);

useEffect(()=>{
	// 每次 更新 把值 复制给 modelStatusRef
	modelStatusRef.current = modelStatus;
}, [modelStatus]); // 依赖的值 等modelStatus 改变了 才出发里面的值

function statusHandleChange(val) {
        setModelStatus(val);
        
        // **设置一个延迟 0毫秒,这个 很重要**
        setTimeout(search, 0);
    }

function search(value) {
	// 这里的值 就是 拿到最新的值了
    let _modelStatus = modelStatusRef .current;
	console.log(_modelStatus );  
	props.callApi(_modelStatus );
}

第三种方法: (其实第三种方法跟第二种方法差别不大)

  useEffect(()=>{
           setModelStatus(modelStatus);
   },[modelStatus]);
//创建Context组件
const ThemeContext = React.createContext({
  theme: 'dark',
  toggle: () => {}, //向上下文设定一个回调方法
});

//运行APP
class App extends React.Component {
  constructor(props) {
    super(props);

    this.toggle = () => { //设定toggle方法，会作为context参数传递
      this.setState(state => ({
        theme:
          state.theme === themes.dark
            ? themes.light
            : themes.dark,
      }));
    };

    this.state = {
      theme: themes.light,
      toggle: this.toggle,
    };
  }

  render() {
    return (
      <ThemeContext.Provider value={this.state}> //state包含了toggle方法
        <Content />
      </ThemeContext.Provider>
    );
  }
}

//中间组件
function Content() {
  return (
    <div>
      <Button />
    </div>
  );
}

//接收组件
function Button() {
  return (
    <ThemeContext.Consumer>
      {({theme, toggle}) => (
        <button
          onClick={toggle} //调用回调
          style={{backgroundColor: theme}}>
          Toggle Theme
        </button>
      )}
    </ThemeContext.Consumer>
  );
}

配合useEffect使用
创建一个新的变量保存最新的数据
3.使用useState，回调函数形式更改数据
4.useState存入的值只是该值的引用（引用类型）
5.useState，如果保存引用数据，useEffect检测不到变化
const textObj = {name:'dx'}
const [useState1, setUseState1] = useState(textObj)
/** usestate的操作不要放在函数的最外层，这里只是简单的代码展示，你可以将set操作放在某个函数里面 */
setUseState1((oldUseState1) => {
  oldUseState1.age = 18
  return oldUseState1
}
useEffect(() => {
  console.log(useState1)  
},[useState1])
//结果是没有任何反应
但是如果你在不止一个地方用到了这个函数或者别的原因，你无法把一个函数移动到effect内部，还有一些其他办法：
如果这函数不依赖state、props内部的变量。可以把这个函数移动到你的组件之外。这样就不用其出现在依赖列表中了。
如果其不依赖state、props。但是依赖内部变量，可以将其在effect之外调用它，并让effect依赖于它的返回值。
万不得已的情况下，你可以把函数加入effect的依赖项，但把它的定义包裹进useCallBack。这就确保了它不随渲染而改变，除非它自身的依赖发生了改变。
EQ:effect 的依赖频繁变化
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(c => c + 1); // ✅ 在这不依赖于外部的 `count` 变量
    }, 1000);
    return () => clearInterval(id);
  }, []); // ✅ 我们的 effect 不使用组件作用域中的任何变量

  return <h1>{count}</h1>;
}
6.useState无法保存一个函数
在useState中，函数会自动调用，并且保存函数返回的值，而不能保存函数本身
使用useState不能保存函数，那么可以使用useCallback这个hook
7.惰性初始 state
initialState 参数只会在组件的初始渲染中起作用，后续渲染时会被忽略。如果初始 state 需要通过复杂计算获得，则可以传入一个函数，在函数中计算并返回初始的 state，此函数只在初始渲染时被调用
const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props);
  return initialState;
});


##### useEffect会使用useEffect来隔离副作用
在函数组件主体内（这里指在 React 渲染阶段）改变 DOM、添加订阅、设置定时器、记录日志以及执行其他包含副作用的操作都是不被允许的，因为这可能会产生莫名其妙的 bug 并破坏 UI 的一致性。
使用 useEffect 完成副作用操作。赋值给 useEffect 的函数会在组件渲染到屏幕之后执行
传给 useEffect 的函数会在浏览器完成布局与绘制之后，在一个延迟事件中被调用。
默认情况下，effect 会在每轮组件渲染完成后执行。这样的话，一旦 effect 的依赖发生变化，它就会被重新创建。
如果想执行只运行一次的 effect（仅在组件挂载和卸载时执行），可以传递一个空数组（[]）作为第二个参数。这就告诉 React 你的 effect 不依赖于 props 或 state 中的任何值，所以它永远都不需要重复执行
如果你传入了一个空数组（[]），effect 内部的 props 和 state 就会一直持有其初始值
规则：数组中包含了所有外部作用域中会发生变化且在 effect 中使用的变量，否则你的代码会引用到先前渲染中的旧变量
1.在代码中，我们使用async / await从第三方API获取数据。如果你对async/await熟悉的话，你会知道，每个async函数都会默认返回一个隐式的promise。但是，useEffect不应该返回任何内容
2.当useEffect的第二个参数传数组传一个依赖项，当依赖项的值发生变化，都会触发useEffect执行。
useEffect的不作为componentDidUnmount的话，传入第二个参数时一定注意：第二个参数不能为引用类型，引用类型比较不出来数据的变化，会造成死循环
请记住：只有某个变量更新后，需要重新执行useEffect的情况，才需要将该变量添加到useEffect的依赖数组中。
useEffect(() => {
    const fetchData = async () => {
      setIsError(false);
      setIsLoading(true);
 
      try {
        const result = await axios(url);
 
        setData(result.data);
      } catch (error) {
        setIsError(true);
      }
 
      setIsLoading(false);
    };
 
    fetchData();
  }, [url]);

由于我们使用了async/await，可以使用一个大大的try-catch
 hooks的依赖项目可以按照数据类型分为基本数据类型和引用数据类型，当为基本数据类型时useEffect可以根据依赖项的变化而去发生改变，当为引用数据类型时，useeffect总是会被触发，因为useEffect这里进行的时浅比较
 想引用数据变化时才触发
 1.使用useRef
 const usePrevious=(value)=>{
     const ref=useRef();
     useEffect(()=>{
         ref.current=value;
     },[value]);
     return ref.current;
 }
 使用
const Counter = () => {
    const [value, setValue] = React.useState(0);
    const lastValue = usePrevious(value);

    return (
        <div>
            <p>
                Current: {value} - Previous: {lastValue}
            </p>
            <button onClick={() => setValue(value + 1)}>Increment</button>
        </div>
    );
};
 2.使用use-deep-compare-effect包
 将useEffect方法替换成useDeepCompareEffect即可
 useDeepCompareEffect(()=>{

 },[defaultKeys]);

 3.useCallback
 返回该回调函数的 memoized 版本，该回调函数仅在某个依赖项改变时才会更新
 把回调函数传递给经过优化的并使用引用相等性去避免非必要渲染（例如 shouldComponentUpdate）的子组件时，它将非常有用
 ①父组件函数在子组件被调用的时候，此时内部的query是初始传入的，因此不会取父组件值
 useCallback 的作用在于利用 memoize 减少无效的 re-render，来达到性能优化的作用,callback 内部对 state 的访问依赖于 JavaScript 函数的闭包。如果希望 callback 不变，那么访问的之前那个 callback 函数闭包中的 state 会永远是当时的值。
 共享相同代码的不同函数实例往往在React组件内部创建
当 React 组件主体创建一个函数（例如回调或事件处理程序）时，这个函数会在每次渲染时重新创建。
在某些情况下，你需要保留一个函数的一个实例
①包装在 React.memo()（或 shouldComponentUpdate ）中的组件接受回调prop。
②当函数用作其他hooks的依赖项时 useEffect(...，[callback])
第二个问题，为什么滥用useCallback有时候会适得其反？因为单从组件上看，inline函数是一定会创建的（上面的callback内联函数），每次函数的创建都需要占用内存，而useCallback的目的就是为了缓存inline函数，而无意义的创建和内部每次的浅比较都是会消耗些许性能的。因此如果传入useCallback的第二个参数是一个经常变更的state，那么callback也就无法缓存的。所以useCallback的使用一定要分清使用场景才能达到效果。
useMemo把“创建”函数和依赖项数组作为参数传入,把执行结果加入缓存并返回。而useCallback只是缓存函数而不调用。也可以理解为useMemo是值对依赖项是否有依赖的缓存，useCallBack是函数对依赖项的缓存。

4.useMemo
这种优化有助于避免在每次渲染时都进行高开销的计算
传入 useMemo 的函数会在渲染期间执行
原则不要只为了保持一致的引用就记忆一个值。如果你需要根据更改的 prop 或值重新计算该值，那就请随意使用 useMemo Hook。
传递给 useMemo 的函数开销大不大
给定相同的输入值时，对记忆（memoized）值的引用是否会发生变化(变量的数据类型)
使得控制具体子节点何时更新变得更容易，减少了对纯组件的需要
function App() {
  const [count, setCount] = useState(1);
  const countRef = useRef();// 在组件生命周期内保持唯一实例，可穿透闭包传值

  useEffect(() => {
    countRef.current = count; // 将 count 写入到 ref
  });
  // 只有countRef变化时，才会重新创建函数
  const callback = useCallback(() => {
    const currentCount = countRef.current //保持最新的值
    console.log(currentCount);
  }, [countRef]);
  return (
    <Parent callback={callback} count={count}/>
  )
}
function Parent({ count, callback }) {
  // count变化才会重新渲染
  const child1 = useMemo(() => <Child1 count={count} />, [count]);
  // callback变化才会重新渲染，count变化不会 Rerender
  const child2 = useMemo(() => <Child2 callback={callback} />, [callback]);
  return (
    <>
      {child1}
      {child2}
    </>
  )
}

5.useRef
useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。返回的 ref 对象在组件的整个生命周期内持续存在
useRef 就像是可以在其 .current 属性中保存一个可变值的“盒子”

useRef Hook 来保持对不需要更新的初始 prop 值的引用，在重新渲染时保持对值的引用不变
useRef 来保持对开销较大的函数评估的引用——只要该函数不需要在 props 更改时重新计算就没问题
useRef 会在每次渲染时返回同一个 ref 对象
function MeasureExample() {
  const [height, setHeight] = useState(0);

  const measuredRef = useCallback(node => {
    if (node !== null) {
      setHeight(node.getBoundingClientRect().height);
    }
  }, []);

  return (
    <>
      <h1 ref={measuredRef}>Hello, world</h1>
      <h2>The above header is {Math.round(height)}px tall</h2>
    </>
  );
}
当 ref 对象内容发生变化时，useRef 并不会通知你。变更 .current 属性不会引发组件重新渲染
function MeasureExample() {
  const [rect, ref] = useClientRect();
  return (
    <>
      <h1 ref={ref}>Hello, world</h1>
      {rect !== null &&
        <h2>The above header is {Math.round(rect.height)}px tall</h2>
      }
    </>
  );
}

function useClientRect() {
  const [rect, setRect] = useState(null);
  const ref = useCallback(node => {
    if (node !== null) {
      setRect(node.getBoundingClientRect());
    }
  }, []);
  return [rect, ref];
}
 6.useContext
 useContext(MyContext) 只是让你能够读取 context 的值以及订阅 context 的变化
 当组件上层最近的 <MyContext.Provider> 更新时，该 Hook 会触发重渲染
 不一定非要使用redux，比如下面的几个场景
  部分复杂页面状态需要进行祖孙传递
  项目很小，至少很少量的全局数据需要维护
  如果state发生改变，比如一个很小的点发生改变，整个页面都会重新渲染
  传值到第三层就说明业务解耦有问题
  Context 主要应用场景在于很多不同层级的组件需要访问同样一些的数据。请谨慎使用，因为这会使得组件的复用性变差。
  一种无需 context 的解决方案是将 Avatar 组件自身传递下去
  如果子组件需要在渲染前和父组件进行一些交流，你可以进一步使用 render props。
Context 能让你将这些数据向组件树下所有的组件进行“广播”，所有的组件都能访问到这些数据，也能访问到后续的数据更新。使用 context 的通用的场景包括管理当前的 locale，theme，或者一些缓存数据

 6.使用useReducer整合逻辑
 const [state, dispatch] = useReducer(reducer, initialArg, init);
 当出现这种 根据前一个状态更新状态 的时候，我们可以用useReducer去替换useState
 React Hooks的方式去获取。这是一种关注数据流和同步思维的方式
 React会保证dispatch在组件的声明周期内保持不变。所以上面的例子中不需要依赖dispatch。
用了useReducer我们就可以移除list依赖。
这使得我们的fetchData函数和list状态解耦。我们的fetchData函数不再关心怎么更新状态，
state 逻辑较复杂且包含多个子值，或者下一个 state 依赖于之前的state等(比如列表数据依赖列表id的变化)。并且，使用 useReducer 还能给那些会触发深更新的组件做性能优化，因为你可以向子组件传递 dispatch 而不是回调函数。
function Children(props) {
  const { fetchData } = props;

  return (
    <div>
      <button onClick={() => { fetchData(); }}>点击</button>
    </div>
  );
};

const initialList = [];

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return [...state, ...action.payload];
    default:
      throw new Error();
  }
}

export default function Demo() {
  const [list, dispatch] = useReducer(reducer, initialList);

  const fetchData = useCallback(async () => {
    const res = await axios(`/getList`);
    dispatch({
      type: 'increment',
      payload: res
    });
  }, []);

  useEffect(() => {
    fetchData();
  }, [fetchData]);

  return (
    <div>
      <ul>
        {list.map(({ text }) => {
          return (
            <li key={text}>{ text }</li>
          );
        })}
      </ul>
      <Children fetchData={fetchData} />
    </div>
  );
};
以用一个增长的计数器来在 state 没变的时候依然强制一次重新渲染
const [ignored, forceUpdate] = useReducer(x => x + 1, 0);

  function handleClick() {
    forceUpdate();
  }
  7.如何实现 shouldComponentUpdate
  const Button = React.memo((props) => {
  // 你的组件
});
React.memo 等效于 PureComponent，但它只比较 props。（你也可以通过第二个参数指定一个自定义的比较函数来比较新旧 props。如果函数返回 true，就会跳过更新。）