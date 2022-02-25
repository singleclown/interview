### 基础知识
* 定义(==冒号后面的都是类型==)
> 1.数据类型(定义变量)-基本类型boolean、number、string、空值（Void)、null 和 undefined、symbol、bigint
undefined 和 null 是所有类型的子类型
boolean
let a:boolean=false;
数字
let a:number=6;
字符串
let a:string=‘6’
let u: undefined = undefined;
let n: null = null;
let u: void;
let num: number = u;
let unusable: void = undefined;(void
void类型像是与any类型相反，它表示没有任何类型。 当一个函数没有返回值时)
声明一个 void 类型的变量没有什么用，因为你只能将它赋值为 undefined 和 null（只在 --strictNullChecks 未指定时）
> 2.数据类型(定义变量)-引入的类型：数组、元祖、object、类(Classes)、泛型(Generics)、接口(Interfaces)以及枚举(Enums)
数组
let a:number[]=[1,2,3]
元祖
因此使用元祖可以确定元素数据类型，但不要超出范围，可以把元祖理解为固定长度，超出范围不能保证其类型
let x:[string,number]=[’10’,10]

数组泛型
let a:Array<number>=[1,2,3];
枚举
枚举类型可以为一组数值赋予友好
enum Color {Red,Green}
let a:Color=Color.Red;
从0开始为元素编号
对象(内联类型或者接口或者object)
内联类型注解
let name: {
  first: string;
  second: string;
};

name = {
  first: 'John',
  second: 'Doe'
};
let prettySure: Object = 4;( Object类型的变量只是允许你给它赋任意值 - 但是却不能够在它上面调用任意的方法)
接口
在面向对象语言中，接口是一个很重要的概念，它是对行为的抽象，而具体如何行动需要由类去实现。
TypeScript 中的接口是一个非常灵活的概念，除了可用于对类的一部分行为进行抽象以外，也常用于对「对象的形状（Shape）」进行描述。
接口（引用类型）接口是 TypeScript 的一个核心知识，它能合并众多类型声明至一个类型声明
「ts 的核心原则之一就是对值所具有的结构进行类型检查。」结构性子类型化
类型检查器不会去检查属性的顺序，只要相应的属性存在并且类型也是对的就可以。
可选属性
在TypeScript里我们可以在参数名旁使用 ?实现可选参数的功能
interface SquareConfig {
  color?: string;
  width?: number;
}
只读属性（readonly vs const
最简单判断该用readonly还是const的方法是看要把它做为变量使用还是做为一个属性。 做为变量使用的话用 const，若做为属性则使用readonly。）
interface SquareConfig {
  readyonly color: string;
  width?: number;
}
额外的属性检查
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
来避免属性名称不一致
类
class Animal {
  public name;
  public constructor(name) {
    this.name = name;
  }
}
3.任意变量 any
let a:any=4;
4.never类型表示的是那些永不存在的值的类型。 
例如， never类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型；
let a:never=throw new Error(‘error’)

5.复合类型(联合类型)
联合类型（Union Types）表示取值可以为多种类型中的一种
let myFavoriteNumber: string | number;
访问联合类型的属性或方法§
当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们只能访问此联合类型的所有类型里共有的属性或方法：

* 类型相关知识拓展
## 接口
1.接口去描述类
interface Person {
    name: string;
    age: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
赋值的时候，变量的形状必须和接口的形状保持一致，一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集
接口去描述类，类（implements）去实现
interface Person {
    name: string;
    age: number;
}

interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
接口混合类型
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}
类(接口描述了类的公共部分)
class Animal {
    move(distanceInMeters: number = 0) {
        console.log(`Animal moved ${distanceInMeters}m.`);
    }
}
默认为 public
class Animal {
    public name: string;
    public constructor(theName: string) { this.name = theName; }
    public move(distanceInMeters: number) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}
2.接口描述可索引的类型(描述数组或者对象)
函数型组件
type Props = {
  foo: string;
};

const MyComponent: React.FunctionComponent<Props> = props => {
  return <span>{props.foo}</span>;
};

<MyComponent foo="bar" />;
ts如何定义数组中嵌套对象的类型
interface ObjectOf<V> {
  [_: string]: V
}
class OrderItem {
  uuid: string;
  amount: number;
  secondaryTotal: number;
  total: number;
  originalTotal: number;
  checked: boolean;
}
interface State {
	 promoList: {
	    giftCheck:ObjectOf<OrderItem>
	    freeCheck:Array<OrderItem>
	  }
  }
interface aa{
  [a:string]:any
}

interface aa{
  [a:number]:any
}
这是因为当使用 number来索引时，JavaScript会将它转换成string然后再去索引对象
注意点：1.写了索引类型以后，其他定义需要返回类型和可索引类型的一致
2.可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型
interface UserArr{
     //定义索引key为number类型，索引值为string类型
     [index:number]:string
 }
 
 var arr1:UserArr;
  arr1=["aa","bb","cc"];
 var arr2: UserArr
  arr2={1:"hello",2:"world"};
 console.log(arr1);
 console.log(arr2);
3.把类当做接口使用
class Point {
    x: number;
    y: number;
}
interface Point3d extends Point {
    z: number;
}
let point3d: Point3d = {x: 1, y: 2, z: 3};

## 数组
解构
let o={
  a:'foo',
  b:12,      
  c:'bar' 
};
let {a,b,d:ff=43}:{a:string,b:number,d?:number}=o;

let fibonacci: number[] = [1, 1, 2, 3, 5];
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
let {a,b}:{a:string,b:number}=o;
// 1.数组元素的赋值-JavaScript语法
var first=someArray[0];
var second=someArray[1];
var third=someArray[2];

// 2.对比一下TypeScript、ES6提供简介、高效、可读的解构语法
var [first,second,third]=someArray;

// 3. 对任意深度的嵌套数组进行解构
var [foo,[[bar],baz]]=[1,[[2],3]];
console.log(foo);//1
console.log(bar);//2
console.log(baz);//3

// 4.忽略尾随元素
let [first]=[1,2,3,4];
console.log(first);//1

// 5 .忽略部分元素
let [,second,,forth]=[1,2,3,4];
console.log(second);//2
console.log(forth);//4
// 1. 解构对象
let o={
  a:'foo',
  b:12,      
  c:'bar' 
};
let {a,b}=o;

// 2. 没有声明的赋值
({a,b}={a:'foo',b:12,c:'bar'});//注意此处，需要用括号括起来，因为javascript通常会以{起始的语句作为一个代码块。

// 3.对象里面可以使用 ... 语法创建剩余变量
let {a,...passthrough}=o;
let total=passthrough.b+passthrough.c.length;

// 4. 属性重命名
let {a:newName1,b:newName2}=o;

//a:newName1读作“a作为newName1”,等价于一下写法：
let newName1=o.a;
let newName2=o.b;


// 6 .指定默认值(即使b为udefined，obj的属性a,b也都会有值)
function keepWhole(obj:{a:string,b?:number}{
   let {a,b=1001}=obj; 
});
// 1. 数组展开
let first=[1,2];
let second=[3,4];
let all=[0,...first,...second,5];
console.log(all);//0,1,2,3,4,5

// 2. 对象展开(属性值覆盖)
let default={ food:'spicy',price:'$',drink:'coko'};
let search={...default,food:'rich'};
console.log(search);//{food:'rich',price:'$',drink:'coko'}

// 3.对象展开：它只包含自身的可枚举的属性。 并且会丢失展开对象的方法：
class C={p:12,m(){}};
let c=new C();
let clone={...c};
console.log(clone.p);//12
console.log(clone.m);//error!
类数组
interface IArguments {
    [index: number]: any;
    length: number;
    callee: Function;
}
function sum() {
    let args: IArguments = arguments;
}
任意类型
let list: any[] = ['xcatliu', 25, { website: 'http://xcatliu.com' }];
## 泛型（类型变量-T帮助我们捕获用户传入的类型，是个变量，自动捕获类型）描述函数或者类
我们需要一种方法使返回值的类型与传入参数的类型是相同的。 这里，我们使用了 类型变量，它是一种特殊的变量，只用于表示类型而不是值。T为类型参数
泛型就是不预先确定的数据类型，具体的类型在使用的时候再确定的一种类型约束规范
不预先确定的数据类型，具体的类型需要在使用的时候才能确定
直接把泛型类型放在类后面，可以帮助我们确认类的所有属性都在使用相同的类型
设计泛型的关键目的是在成员之间提供有意义的约束，这些成员可以是：
* 类的实例成员
* 类的方法
* 函数参数
* 函数返回值
function identity<T>(arg: T): T {
    return arg;
}
function loggingIdentity<T>(arg: Array<T>): Array<T> {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}
Array<T>或者number[]两种写法
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <T>(arg: T) => T = identity;
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <U>(arg: U) => U = identity;
泛型类型可指定 泛型参数当作整个接口的一个参数
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}
let myIdentity: GenericIdentityFn<number> = identity;
泛型约束-泛型与继承接口
一个接口来描述约束条件
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}
2.泛型约束
泛型约束中使用类型参数
由于事先不知道它是哪种类型，所以不能随意的操作它的属性或方法：
使用了 extends 约束了泛型 T 必须符合接口 Lengthwise 的形状，也就是必须包含 length 属性。
原理：类型约束，需预定义一个接口
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key]
}
keyof T是 string或者number类型。
interface Map<T> {
  [key: string]: T;
}

//T[U]是索引访问操作符;U是一个属性名称。
let keys: keyof Map<number>; //string | number
let value: Map<number>['antzone'];//number
class Animal {
    name: string;
}
class Dog extends Animal {
    breed: string;
}

// 错误：使用数值型的字符串索引，有时会得到完全不同的Animal!
interface NotOkay {
    [x: number]: Dog;
    [x: string]: Animal;
}
const a:NotOkay={2:{name:'3',breed:'3'}};
const a:keyof NotOkay=2;
console.log(a)
泛型函数的类型与非泛型函数的类型没什么不同，只是有一个类型参数在最前面
type Log = <T>(value: T) => T
let myLog: Log = log

interface Log<T> {
    (value: T): T
}
let myLog: Log<number> = log // 泛型约束了整个接口，实现的时候必须指定类型。如果不指定类型，就在定义的之后指定一个默认的类型
myLog(1)
function getInfo1<T extends string>(age: T): T  {
    return `虎克今年:${age}`as T;
}

getInfo1("18");
条件类型推断是依赖于范型约束的吧。范型约束主要是要求你的范型参数必须有什么条件，比如T extends { hello: () => string } 表明这个T必须有个hello方法。类型推断就是在此基础上可以推断出 hello: () => ?? 这个 ?? 的类型。
3.泛型类
class GetMin<T>{
     arr:T[]=[];
     add(ele:T){
         this.arr.push(ele);
     }
    min():T{
       var min=this.arr[0];
       this.arr.forEach(function (value) {
          if(value<min){
              min=value;
          }
       });
       return min;
    }
}
  var gm1= new  GetMin<number>();
  type helloReturn = T extends { hello: () => infer U } ? U : never; 似乎是这样
type Foo<T> = T extends { NodeC: infer U } ? { NodeC: U } : never;

class LinkedList {
    private dummyNode: Foo<this>["NodeC"];

    constructor() {
        const NodeC = this.NodeC;
        this.dummyNode = new NodeC();
    }

    private NodeC = class {
        constructor() {

        }
    }
}

const list = new LinkedList();
console.log(list);
type Foo<T> = T extends { NodeC: infer U } ? { NodeC: U } : never;

class LinkedList {
    private dummyNode: Foo<this>["NodeC"];

    constructor() {
        const NodeC = this.NodeC;
        this.dummyNode = new NodeC();
    }

    private NodeC = class {
        constructor() {

        }
    }
}

const list = new LinkedList();
console.log(list)
## 函数类型
ts定义返回函数类型
一般函数声明：
//第一种形式
let c: Function;
c = function(): void {
    console.log('It work');
}
 
//第二种形式
function test(): Function{
        return function():void{
                console.log('it work');
        }
}
 
let a:Function = test();
a();
 
//第三种形式，箭头函数
let d: (para: string) => string;
d = function(param: string): string {
        return param;
}
 
//第四种形式，类型别名，箭头函数
type e = (para: string) => string;
const f:e = function(pass: string): string{
 
        return pass;
        
        }
 
//第五种形式，接口
interface g{
        (para: string): string;
        }
const h: g = function(pass: string): string{
        return pass;
this问题
foo.on('error', (this: Foo, err: any) => { // DON'T DO THIS
应该是这样的：
foo.on('error', function(this: Foo, err: any) {
或这个：
foo.on('error', function(this: typeof foo, err: any) {
或者
foo.on('error', (err: any) => {
  console.log(err);
  foo.emit('end');
});
1.函数声明
输入多余的（或者少于要求的）参数，是不被允许
function sum(x: number, y: number): number {
    return x + y;
}
2.函数表达式
let mySum: (x: number, y: number) => number = function (x: number, y: number): number {
    return x + y;
};
3.用接口定义函数(函数的参数名不需要与接口里定义的名字相匹配)
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
4.可选参数（可选参数后面不允许再出现必需参数了）
function buildName(firstName: string, lastName?: string) {
    if (lastName) {
        return firstName + ' ' + lastName;
    } else {
        return firstName;
    }
}
5.参数默认值（添加了默认值的参数识别为可选参数）
function buildName(firstName: string, lastName: string = 'Cat') {
    return firstName + ' ' + lastName;
}
6.剩余参数（...rest 的方式获取函数中的剩余参数数组的类型来定义它）
function push(array: any[], ...items: any[]) {
    items.forEach(function(item) {
        array.push(item);
    });
}
7.函数重载(重载允许一个函数接受不同数量或类型的参数时)
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string | void {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
8.this指向
interface Card {
    suit: string;
    card: number;
}
interface Deck {
    suits: string[];
    cards: number[];
    createCardPicker(this: Deck): () => Card;
}
let deck: Deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    // NOTE: The function now explicitly specifies that its callee must be of type Deck
    createCardPicker: function(this: Deck) {
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
this参数在回调函数里
详看官网例子
提供一个显式的 this参数。 this参数是个假的参数，它出现在参数列表的最前面
或者用箭头函数去绑定
### 类知识
装饰器
类装饰器
function Greeter1(greeting: string) {
  return function (target: Function) {
    target.prototype.greet = function (): void {
      console.log(greeting);
    };
  };
}

@Greeter1("Hello TS!")
class Greeting {
  greet:()=>void;
  constructor() {
    // 内部实现
  }
}

let myGreeting = new Greeting();
myGreeting.greet();
@sealed
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

function sealed(constructor: Function) {
    Object.seal(constructor);
    Object.seal(constructor.prototype);
}
类属性装饰器
function logProperty(target: any, key: string) {
  delete target[key];

  const backingField = "_" + key;

  Object.defineProperty(target, backingField, {
    writable: true,
    enumerable: true,
    configurable: true
  });

  // property getter
  const getter = function (this: any) {
    const currVal = this[backingField];
    console.log(`Get: ${key} => ${currVal}`);
    return currVal;
  };

  // property setter
  const setter = function (this: any, newVal: any) {
    console.log(`Set: ${key} => ${newVal}`);
    this[backingField] = newVal;
  };

  // Create new property with getter and setter
  Object.defineProperty(target, key, {
    get: getter,
    set: setter,
    enumerable: true,
    configurable: true
  });
}

class Person { 
  @logProperty
  public name: string;

  constructor(name : string) { 
    this.name = name;
  }
}

const p1 = new Person("semlinker");
p1.name = "kakuqo";
参数装饰器
function Log(target: Function, key: string, parameterIndex: number) {
  let functionLogged = key || target.prototype.constructor.name;
  console.log(`The parameter in position ${parameterIndex} at ${functionLogged} has
 been decorated`);
}

class Greeter {
  greeting: string;
  constructor(@Log phrase: string) {
 this.greeting = phrase; 
  }
}
方法装饰器
function hack(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    const oldFunction = target[propertyKey]; // 获取方法引用
    const newFunction = function(...args: any[]) {
        console.log('call function ', propertyKey);
        oldFunction.call(target, ...args);
    }
    descriptor.value = newFunction; // 替换原声明
}

class Demo {
    @hack
    demo() {
        console.log('call demo');
    }
}

const demo = new Demo();
demo.demo();
* 类型断言
如果没有明确的指定类型，那么 TypeScript 会依照类型推论（Type Inference）的规则推断出一个类型
如果定义的时候没有赋值，不管之后有没有赋值，都会被推断成 any 类型而完全不被类型检查
## 类型断言语法
值 as 类型或者<类型>值
<Foo> 的语法在 tsx 中表示的是一个 ReactNode，在 ts 中除了表示类型断言之外，也可能是表示一个泛型

## 用途
1.将一个联合类型断言为其中一个类型,就能访问特定的方法或属性(只能访问此联合类型的所有类型中共有的属性或方法)
function isFish(animal: Cat | Fish) {
    if (typeof animal.swim === 'function') {
        return true;
    }
    return false;
}
2.将一个父类断言为更加具体的子类
interface ApiError extends Error {
    code: number;
}
interface HttpError extends Error {
    statusCode: number;
}

function isApiError(error: Error) {
    if (typeof (error as ApiError).code === 'number') {
        return true;
    }
    return false;
}
3.将任何一个类型断言为 any
(window as any).foo = 1;
## 将 any 断言为一个具体的类型
能够将调用了它之后的返回值断言成一个精确的类型
function getCacheData(key: string): any {
    return (window as any).cache[key];
}

interface Cat {
    name: string;
    run(): void;
}

const tom = getCacheData('tom') as Cat;
tom.run();
## 断言限制
子类可以被断言为父类
interface Animal {
    name: string;
}
interface Cat extends Animal {
    run(): void;
}
interface Animal {
    name: string;
}
interface Cat {
    name: string;
    run(): void;
}

function testAnimal(animal: Animal) {
    return (animal as Cat);
}
function testCat(cat: Cat) {
    return (cat as Animal);
}
interface Animal {
    name: string;
}
interface Cat {
    name: string;
    run(): void;
}

let tom: Cat = {
    name: 'Tom',
    run: () => { console.log('run') }
};
let animal: Animal = tom;
Animal 兼容 Cat
* 声明文件
TypeScript 作为 JavaScript 的超集，在开发过程中不可避免要引用其他第三方的 JavaScript 的库。虽然通过直接引用可以调用库的类和方法，但是却无法使用TypeScript 诸如类型检查等特性功能。为了解决这个问题，需要将这些库里的函数和方法体去掉后只保留导出类型声明，而产生了一个描述 JavaScript 库和模块信息的声明文件。通过引用这个声明文件，就可以借用 TypeScript 的各种特性来使用库文件了。
declare 关键字来告诉 TypeScript，你正在试图表述一个其他地方已经存在的代码
在这里 TypeScript 将不会把它编译成任何代码，同时开发者需要确保这些在编译时存在。
declare:作用
在编写类声明文件（.d.ts）时，declare 表示声明作用。
declare const myPoint: { x: number; y: number };
当你安装 TypeScript 时，会顺带安装一个 lib.d.ts 声明文件。这个文件包含 JavaScript 运行时以及 DOM 中存在各种常见的环境声明。
环境声明是一种告诉TypeScript编译器实际源代码存在于其他位置的方法。当你使用像jquery/angularjs/nodejs这样的第三方js库时，你无法在TypeScript中重写它
按惯例，环境声明保存在类型声明文件中，扩展名如下（d.ts）
Sample.d.ts
上述文件将不会被转换为JavaScript。它将用于类型安全和智能感知。
环境文件将不包含实现，它只是类型声明。声明现在需要包含在TypeScript文件中
// 示例 B
interface Point {
  x: number;
  y: number;
}
declare const myPoint: Point;

* 类型别名(类型别名常用于联合类型)-类型别名用来给一个类型起个新名字。
字符串字面量类型用来约束取值只能是某几个字符串中的一个
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    } else {
        return n();
    }
}
type类似于表达式的定义
type Point = {
  x:number;
  y:number;
}
interface 和 type 两个关键字的含义和功能都非常的接近。这里我们罗列下这两个主要的区别：
interface：
* 同名的 interface 自动聚合，也可以跟同名的 class 自动聚合
* 只能表示 object、class、function 类型
type:
* 不仅仅能够表示 object、class、function
* 不能重名（自然不存在同名聚合了），扩展已有的 type 需要创建新 type
* 支持复杂的类型操作
在实际开发中，有的时候也会遇到 interface 能够表达，但是 type 做不到的情况：「给函数挂载属性」
给函数添加属性（利用类型转换就行）
方案1不好
interface IAbc {
	(a: string, b: number): void;
	(a: string): void;
	someAttr?: ()=>void; // 加了个问号
}
let abc: IAbc = (a: any, b?:any) => {
	if (typeof b === 'number'){}
	else {}
}
abc.someAttr = ()=>{};
方案2好
interface IAbc {
    (a: string, b?: number): void
    [propName: string]: () => void | undefined
	(a: string): void;

}

const abc = ((a: string, b?: number) => {
    if (typeof b === 'number') {
        console.log(b)
    } else {
        console.log(a)
    }
}) as IAbc

abc.someAttr = () => {
    console.log('some attr')
}
方案3这样someAttr需要加问号
interface IAbc {
	(a: string, b: number): void;
	(a: string): void;
	someAttr?: ()=>void;
}

let abc: IAbc = (a: any, b?:any) => {
	if (typeof b === 'number'){}
	else {}
}
abc.someAttr = ()=>{};
参考答案不好
interface F { (): any; someValue: number }
const f: F = Object.assign(() => {}, { someValue: 3 })