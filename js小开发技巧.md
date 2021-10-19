### 小技巧

可迭代对象：Array，Map，Set，String，TypedArray(一个类型化数组（TypedArray）对象描述了一个底层的二进制数据缓冲区)，arguments 对象

#### 字符串转数组

1.split 拆分 "abc".split('') ==> ["a","b","c"] 2.扩展运算符 [...][..."abc"] ==> ["a","b","c"] 3.数组构造方式 Array.from("abc") ==> ["a","b","c"]

#### 数组合并

const arr3 =[].concat.apply(arrA, arrB)//arrA 值为[1,2,3,4]

#### 类型转换的默认值

'J' + { toString: ()=>'ava' }
2  \* { valueOf: ()=>'4' }

#### 判断小数是否相等

    <!-- Number.EPSILON 属性表示 1 与Number可表示的大于 1 的最小的浮点数之间的差值 -->
    Number.EPSILON=(function(){   //解决兼容性问题
        return Number.EPSILON?Number.EPSILON:Math.pow(2,-52);
    })();
    //上面是一个自调用函数，当JS文件刚加载到内存中，就会去判断并返回一个结果
    function numbersequal(a,b){ 
        return Math.abs(a-b)<Number.EPSILON;
    }
    //接下来再判断   
    const a=0.1+0.2, b=0.3;
    console.log(numbersequal(a,b)); //这里就为true了

#### 数据类型输出

    function dataTypeJudge(val, type) {
    const dataType = Object.prototype.toString.call(val).replace(/\[object (\w+)\]/, "$1").toLowerCase();

return type ? dataType === type : dataType;
}

#### 取整（去掉小数部分）取整个数字或四舍五入：parseInt、math.ceil,math.floor,math.round 四舍五入，|0，正则

1.parseInt(num)
2.~~num
3.num >> 0
4.num | 0
x & 1 === 0 为偶数。 x%2 === 0 为偶数

~，被称为“按位不运算符”，等价于  - n - 1
~~可以有效的否定运算

#### 过滤假值

a.filter(Boolean)
过滤假值 let a=[1,false,2,null,3,undefined,4,-0,5+0,6,NaN,7,'']

#### 取几位小数点数字（+'4.54e-2'）

求幂运算符（\*\*）Math.pow(2,3)

### 数组置空

清除数组通过设置 length 等于 0 可以快速清空一个数组


###  状态切换
var clock = function* () {
  while (true) {
    console.log('Tick!');
    yield;
    console.log('Tock!');
    yield;
  }
};
### 解构注意事项（注意，对象的解构赋值可以取到继承的属性。）
const obj1 = {};
const obj2 = { foo: 'bar' };
Object.setPrototypeOf(obj1, obj2);

const { foo } = obj1;
foo // "bar"
### 一次性函数(惰性载入函数):在某个场景下我们的函数中有判断语句，这个判断依据在整个项目运行期间一般不会变化，所以判断分支在整个项目运行期间只会运行某个特定分支，那么就可以考虑惰性载入函数
function foo(){
  if(a!=b){
foo=function(){
  console.log('aaa');
}
  }else{
foo=function(){
  console.log('bbb');
}
  }
  return foo();
}
实例：
惰性函数每次只初始化一次环境
function addEvent (type, el, fn) {
    if (window.addEventListener) {
        addEvent = function (type, el, fn) {
            el.addEventListener(type, fn, false);
        }
    }
    else if(window.attachEvent){
        addEvent = function (type, el, fn) {
            el.attachEvent('on' + type, fn);
        }
    }
}

### 交换参数数值
1.解构来交换参数数值
[array[index1],array[index2]] = [array[index2],array[index1]];
2.splice
titles[vOldId] = titles.splice(vNewId, 1, titles[vOldId])[0];

### 时间休眠(JavaScript 一直没有休眠的语法，但是借助await命令就可以让程序停顿指定的时间。下面给出了一个简化的sleep实现。)
方式1：
class Sleep {
  constructor(timeout) {
    this.timeout = timeout;
  }
  then(resolve, reject) {
    const startTime = Date.now();
    setTimeout(
      () => resolve(Date.now() - startTime),
      this.timeout
    );
  }
}

(async () => {
  const sleepTime = await new Sleep(1000);
  console.log(sleepTime);
})();
方式2：
function sleep(interval) {
  return new Promise(resolve => {
    setTimeout(resolve, interval);
  })
}

// 用法
async function one2FiveInAsync() {
  for(let i = 1; i <= 5; i++) {
    console.log(i);
    await sleep(1000);
  }
}

### 扁平化数组
扁平化
1.toString & split 
arr2.toString().split(',').map(v=>parseInt(v))
2.reduce
const arr = [1, 2, 3, 4, [1, 2, 3, [1, 2, 3, [1, 2, 3]]], 5, ["string", { type: "对象" }]];
function flat(arr) {
  return arr.reduce((prev, cur)=>{
    return prev.concat(Array.isArray(cur)?flat(cur):cur);
  }, [])
}
flat(arr)
3.join & split
4.递归
5.扩展运算符[].concat(...arr)
6.flat
const tree = [ 'a', ['b', 'c'], ['d', 'e'] ];

for(let x of iterTree(tree)) {
  console.log(x);
}
var arr4 = [1, 2, [3, 4, [5, 6, [7, 8, [9, 10]]]]];
arr4.flat(Infinity);

[].concat(...[1, 2, 3, [4, 5]]);  

var arr = [1, [2, [3, 4]]];

function flatten(arr) {

    while (arr.some(item => Array.isArray(item))) {
        arr = [].concat(...arr);
    }

    return arr;
}
7.Generator实现flat函数
function* iterTree(tree) {
  if (Array.isArray(tree)) {
    for(let i=0; i < tree.length; i++) {
      yield* iterTree(tree[i]);
    }
  } else {
    yield tree;
  }
}



### 函数组合compose
利用 compose 将两个函数组合成一个函数，让代码从右向左运行，而不是由内而外运行，可读性大大提升。这便是函数组合。
// 先定义基本运算
var split = curry(function(separator, str) { return str.split(separator) })
var head = function(str) { return str.slice(0, 1) }
var toUpperCase = function(str) { return str.toUpperCase() }
var join = curry(function(separator, arr) { return arr.join(separator) })
var map = curry(function(fn, arr) { return arr.map(fn) })

var initials = compose(join('.'), map(compose(toUpperCase, head)), split(' '));

initials("kevin daisy kelly");
function list() {
  return Array.prototype.slice.call(arguments);
}
function compose() {
    var args = arguments;
    var start = args.length - 1;
    return function() {
        var i = start;
        var result = args[start].apply(this, arguments);
        while (i--) result = args[i].call(this, result);
        return result;
    };
};
pointfree 指的是函数无须提及将要操作的数据是什么样的。依然是以最初的需求为例：



### 多个请求继发
getFoo和getBar是两个独立的异步操作（即互不依赖），被写成继发关系
// 写法一
let [foo, bar] = await Promise.all([getFoo(), getBar()]);
// 写法二
let foo = await getFoo();
let bar = await getBar();
或者
let fooPromise = getFoo();
let barPromise = getBar();
let foo = await fooPromise;
let bar = await barPromise;
async function logInOrder(urls) {
  // 并发读取远程URL
  const textPromises = urls.map(async url => {
    const response = await fetch(url);
    return response.text();
  });

  // 按次序输出
  for (const textPromise of textPromises) {
    console.log(await textPromise);
  }
}
const superagent = require('superagent');
const NUM_RETRIES = 3;

async function test() {
  let i;
  for (i = 0; i < NUM_RETRIES; ++i) {
    try {
      await superagent.get('http://google.com/this-throws-an-error');
      break;
    } catch(err) {}
  }
  console.log(i); // 3
}

test();
上面代码中，如果await操作成功，就会使用break语句退出循环；如果失败，会被catch语句捕捉，然后进入下一轮循环。
下面的例子使用try...catch结构，实现多次重复尝试。
虽然map方法的参数是async函数，但它是并发执行的，因为只有async函数内部是继发执行，外部不受影响。后面的for..of循环内部使用了await，因此实现了按顺序输出。
### 类数组转数组
// 1. slice
Array.prototype.slice.call(arrayLike); // ["name", "age", "sex"] 
var args = Array.prototype.slice.call(arguments,1);//将arguments转化为数组后，截取第一个元素之后的所有元素
// 2. splice
Array.prototype.splice.call(arrayLike, 0); // ["name", "age", "sex"] 
// 3. ES6 Array.from
Array.from(arrayLike); // ["name", "age", "sex"] 
// 4. apply
Array.prototype.concat.apply([], arrayLike)
//5…在方法中使用扩展运算符
[...arguments]
arguments 和对应参数的绑定
传入的参数，实参和 arguments 的值会共享，当没有传入时，实参与 arguments 值不会共享
arguments中this指向问题
var length = 10;
function fn() {
    console.log(this.length);
}
 
var obj = {
  length: 5,
  method: function(fn) {
    fn();
    arguments[0]();
  }
}
类数组判断
console.log(typeof arguments);    // 'object'
// arguments 对象只能在函数内使用
function test(a){
    console.log(a,Object.prototype.toString.call(arguments));
    console.log(arguments[0],arguments[1]);
    console.log(typeof arguments[0]);
}
test(1);
只能在函数中判断出1 "[object Arguments]"
Object.prototype.toString.call(var arrayLike = {0: 'name', 1: 'age', 2: 'sex', length: 3 })"object"
函数外判断


### 判断是否是纯粹的对象
所谓"纯粹的对象"，就是该对象是通过 "{}" 或 "new Object" 创建的，该对象含有零个或者多个键值对。
var class2type = {};

// 相当于 Object.prototype.toString
var toString = class2type.toString;

// 相当于 Object.prototype.hasOwnProperty
var hasOwn = class2type.hasOwnProperty;

function isPlainObject(obj) {
    var proto, Ctor;

    // 排除掉明显不是obj的以及一些宿主对象如Window
    if (!obj || toString.call(obj) !== "[object Object]") {
        return false;
    }

    /**
     * getPrototypeOf es5 方法，获取 obj 的原型
     * 以 new Object 创建的对象为例的话
     * obj.__proto__ === Object.prototype
     */
    proto = Object.getPrototypeOf(obj);

    // 没有原型的对象是纯粹的，Object.create(null) 就在这里返回 true
    if (!proto) {
        return true;
    }

    /**
     * 以下判断通过 new Object 方式创建的对象
     * 判断 proto 是否有 constructor 属性，如果有就让 Ctor 的值为 proto.constructor
     * 如果是 Object 函数创建的对象，Ctor 在这里就等于 Object 构造函数
     */
    Ctor = hasOwn.call(proto, "constructor") && proto.constructor;

    // 在这里判断 Ctor 构造函数是不是 Object 构造函数，用于区分自定义构造函数和 Object 构造函数
    return typeof Ctor === "function" && hasOwn.toString.call(Ctor) === hasOwn.toString.call(Object);
}
console.log(hasOwn.toString.call(Ctor)); // function Object() { [native code] }
console.log(Object.prototype.toString.call(Ctor)); // [object Function]
### 数组去重
1.遍历使用indexof判断，加入新数组
2.记录出现的次数，对象键值法去重
3.排序后相邻去除法，然后遍历时,新数组只加入不与前一值重复的值
4.获取没重复的最右一值放入新数组，双层for循环，获取没重复的最右一值放入新数组。
* （检测到有重复值时终止当前循环同时进入顶层循环的下一轮判断）*
就是遍历时，后面的数据还有该项，该数字就不用放入
5.set
function uniq(array){
    array.sort();
    var temp=[array[0]];
    for(var i = 1; i < array.length; i++){
        if( array[i] !== temp[temp.length-1]){
            temp.push(array[i]);
        }
    }
    return temp;
}

var aa = [1,2,"2",4,9,"a","a",2,3,5,6,5];
console.log(uniq(aa));
function combine(){ 
    let arr = [].concat.apply([], arguments);  //没有去重复的新数组 
    return Array.from(new Set(arr));
} 
function uniq(array){
    var temp = [];
    var index = [];
    var l = array.length;
    for(var i = 0; i < l; i++) {
        for(var j = i + 1; j < l; j++){
            if (array[i] === array[j]){
                i++;
                j = i;
            }
        }
        temp.push(array[i]);
        index.push(i);
    }
    console.log(index);
    return temp;
}

function uniq(array){
    var temp = [];
    for(var i = 0; i < array.length; i++) {
        //如果当前数组的第i项在当前数组中第一次出现的位置是i，才存入数组；否则代表是重复的
        if(array.indexOf(array[i]) == i){
            temp.push(array[i])
        }
    }
    return temp;
}
### 浮点数精度问题
例如：7*0.8
* 思路1 ：将小数转化为整数进行运算
* 实现思想：先将小数转化为字符串，判断小数部分位数，并且将运算两边小数同时乘以10最大小数位数,再将最后结果除以10最大小数位数
* 思路2：限制精度，只保留小数部分位数，减小精度出现的误差问题
* 因为小数精度过高的情况下可能出现无限循环，出现截断或者进位等情况
2. 第三方封装类库（math库使用、bignumber,big,decimal-大概是用类数组存储数据位，保持精度的可靠性）
### 科学计数法
2e3
### 生成树形结构数据(递归)
var getJsonTree = function(data, parentId) {
    var itemArr = [];
    for (var i = 0; i < data.length; i++) {
        var node = data[i];
        if (node.parentId == parentId) {
            var newNode = {};
            newNode.id = node.id;;
            newNode.nodes = getJsonTree(data, node.id);
            itemArr.push(newNode);
        }
    }
    return itemArr;
};
### Url查询参数分割查询
名称输入框输入内容统一校验规则：不能以下划线开头,支持数字,字母,中文和下划线组合，最多输入 100 个字符
pattern = /^(?!_)(?!\*\.\?\+\$\^\[\]\(\)\{\}\|\\\/~!@#&=<>:"{},;'·~%……《》：“”‘’，。、\s「」)[-\w\u4e00-\u9fa5]+(?!(\2))$/g;
const getURLParameters = url =>
  (url.match(/([^?=&]+)(=([^&]*))/g) || []).reduce(
    (a, v) => ((a[v.slice(0, v.indexOf('='))] = v.slice(v.indexOf('=') + 1)), a),
    {}
  );
  var regex = /(?!^)(?=(\d{3})+$)/g;


### javascript如何判断是否为日期格式字符串？isNaN判断非数字
if(isNaN(data)&&!isNaN(Date.parse(data))){
　　console.log("data是日期格式！")
}
### 限制inputnumber最大输入并且超过最大安全数字
import React from "react";
import ReactDOM from "react-dom";
import "antd/dist/antd.css";
import "./index.css";
import { InputNumber } from "antd";
import { Decimal } from "decimal.js";

function onChange(value) {
  console.log("changed", value);
}
export const demicalFormatter = (value, count = 6) => {
  let result = value;
  const demical = value && value.toString().split(".")[1];
  if (demical && demical.length > count) {
    result = new Decimal(value).toFixed(count);
  }
  return result;
};
export const preventKeyPress = (e, max, min = Decimal("0")) => {
  let val = e.target.value;
  if (val) {
    try {
      const decimal = new Decimal(val);
      console.log("prevent-decimal", decimal);
      if (decimal.greaterThanOrEqualTo(max)) {
        e.nativeEvent.preventDefault();
      }
      if (decimal.lessThanOrEqualTo(min)) {
        e.nativeEvent.preventDefault();
      }
    } catch (err) {
      console.log("prevent-err", err);
      return err;
    }
  }
};
ReactDOM.render(
  <InputNumber
    style={{
      width: 200
    }}
    defaultValue="1"
    min="0"
    max={"9999.999999"}
    onKeyPress={(e) => preventKeyPress(e, "9999.999999")}
    formatter={(value) => demicalFormatter(value)}
    onChange={onChange}
    stringMode
  />,
  document.getElementById("container")
);

### 发布订阅
 class pubSub {
    constructor(...args) {
        this.queuedObservers = new Set();
        this.set = this.set.bind(this)
    }
    throwIfMissing = () => {
        throw new Error('Missing parameter');
    }
    //观察者
    observe = (fn = this.throwIfMissing()) => {
        this.queuedObservers.add(fn);
    }
    set = (target, key, value, receiver) => {
        const result = Reflect.set(target, key, value, receiver);
        let t = this;
        this.queuedObservers.forEach(observer => {
            t.value = observer(this.proxyObj);
        });
        return result;
    }
    //观察的目标
    observable = (obj = this.throwIfMissing()) => {
      //包装器：包装观察的对象
       this.proxyObj= new Proxy(obj, { set: this.set })
    }
}
方法2：
const EventEmitter = {
  events: {},
  trigger(event, data, params) {
    if (!this.events[event]) return;
    for (let i = 0; i < this.events[event].length; i++) {
      this.events[event][i](data, params);
    }
  },
  on(event, callback) {
    if (!this.events[event]) {
      this.events[event] = [];
    }
    event && this.events[event].push(callback);
  },
  off(event) {
    if (this.events && this.events[event]) {
      delete this.events[event];
    }
  },
  getEventLen(event) {
    return Array.isArray(this.events[event]) ? this.events[event].length : 0;
  }
};
### 分组
export const group = (array, subGroupLength) => {
    let index = 0;
    let newArray = [];
    while (index < array.length) {
        newArray.push(array.slice(index, index += subGroupLength));
    }
    return newArray;
}

### 限频
防抖和节流的作用都是防止函数多次调用。区别在于，假设一个用户一直触发这个函数，且每次触发函数的间隔小于wait，防抖的情况下只会调用一次，而节流的 情况会每隔一定时间（参数wait）调用函数。
function debounce(func, wait, immediate) {

    let timeout, result;
    let debounced = function () {
        let context = this;
        let args = arguments;
        if (timeout) clearTimeout(timeout);
        if (immediate) {
            // 如果已经执行过，不再执行
            let callNow = !timeout;
            timeout = setTimeout(function () {
                timeout = null;
            }, wait)
            if (callNow) result = func.apply(context, args)
        }else {
            timeout = setTimeout(function () {
                func.apply(context, args)
            }, wait);
        }
        
        return result;
    };

    debounced.cancel = function () {
        clearTimeout(timeout);
        timeout = null;
    };

    return debounced;
}
把保存计时器 ID 的 time 值设置为 null 有两个作用:
---在一个周期内第一次触发，就立即执行一次，然后一定时间段内都不能再执行目标函数
作为开关变量，表明一个周期结束。使得 callNow 为 true，目标函数可以在新的周期里被触发时被执行
timeout 作为闭包引用的上层函数的变量，是不会自动回收的。手动将其设置为 null ，让它脱离执行环境，一边垃圾收集器下次运行是将其回收

/**垃圾写法
 * 
 * @param {func} 函数作为参数 
 * @param {delay} 延迟时间，单位毫秒
 */
const throttle = (func,delay) => {
    clearTimeout(func.tId);
    func.tId = setTimeout(function(){
        func.call();
    },delay);
};


### 尾调用及优化（尾调用不一定出现在函数尾部，只要是最后一步操作即可。）
函数体内返回调用另外函数的结果例如（return g(x)）
函数调用会在内存形成一个“调用记录”，又称“调用帧”,调用帧存在于调用栈内。
尾调用优化-即只保留内层函数的调用帧，只有在不使用外层函数的内部变量才可以优化
优化
将表达式的值放入函数参数中，减少函数嵌套
2.尾递归优化-应用尾递归函数(柯里化)
循环可以用递归代替，而一旦使用递归，就最好使用尾递归。
所有用到的内部变量改写成函数的参数。
将多参数的函数转换成单参数的形式。这里也可以使用柯里化。
函数式编程有一个概念，叫做柯里化（currying），意思是将多参数的函数转换成单参数的形式。这里也可以使用柯里化。
3.thunk函数-其实就是写了个闭包(实现方式)
将参数放到一个临时函数之中，再将这个临时函数传入函数体
多参数函数，将其替换成只接受回调函数作为参数的单参数函数。
fs.readFile(fileName,callback);
let thunk = function(fileName){
return function (callback){
return s.readFile(fileName,callback);
}
}
let readFileThunk = thunk(filename);
readFileThunk(callback);
纯函数是函数式编程的概念，必须遵守以下一些约束。
* 		不得改写参数
* 		不能调用系统 I/O 的API
* 		不能调用Date.now()或者Math.random()等不纯的方法，因为每次会得到不一样的结果
### await语法
缺点在于滥用 await 可能会导致性能问题，因为 await 会阻塞代码，也许之后的异步代码并不依赖于前者，但仍然需要等待前者完成，导致代码失去了并发性。
很多人以为await会一直等待之后的表达式执行完之后才会继续执行后面的代码，实际上await是一个让出线程的标志。await后面的函数会先执行一遍，然后就会跳出整个async函数来执行后面js栈（后面会详述）的代码。等本轮事件循环执行完了之后又会跳回到async函数中等待await
后面表达式的返回值，如果返回值为非promise则继续执行async函数后面的代码，否则将返回的promise放入promise队列（Promise的Job Queue）只执行await语句后就返回
var a = 1;var n=5;
var b = async () => {
  a = n+await ++a+n;
  console.log('2', a) // -> '2' 10
  a = (await 10) + a
  console.log('3', a) // -> '3' 20
}
b();n++;
a++;
console.log('1', a)