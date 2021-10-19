### 基础知识js系列五
js规范
因此单纯的渲染工作，一般需要控制在10毫秒之内完成
那么浏览器渲染动画或页面的每一帧的速率都要保持16毫秒（1秒 / 60 = 16.66毫秒）之内完成。
1.类型：const引用类型可以修改值内容
2.引用变量定义：使用let代替var
3.对象定义
  字面量；属性、方法简写；属性方法都写一起；属性分组-简写的属性写前面；属性名风格统一-有引号的都写单引号；
判断属性用原型方法const has = Object.prototype.hasOwnProperty;console.log(has.call(object, key));
使用扩展运算符代替Object.assign并且不使用delete方法删除属性
❤删除属性或者添加属性
 const original = { a: 1, b: 2 };
const copy = { ...original, c: 3 };
const { a, ...noA } = copy;
对象的使用
访问属性时使用点符号
使用变量访问属性时，使用 `[]`表示法
计算指数时，可以使用 `**` 运算符
4.数组
字面量；使用push等API；使用扩展运算符拷贝数组；对于对迭代器的映射使用Array.from-const baz = Array.from(foo, bar);
在数组回调方法中使用 return 语句
书写规范：如果数组有多行，则在开始的时候换行，然后在结束的时候换行
const arr = [[0, 1], [2, 3], [4, 5]];
const objectInArray = [
      {
        id: 1,
      },
      {
        id: 2,
      },
    ];
const numberInArray = [
      1,
      2,
    ];
5.解构 函数形参对象解构，函数返回值对象解构值，数组解构const [first, second] = arr;
6.字符串 单引号定义;使用字符串模板;不使用eval；字符多时不要使用+号即不使用断开的字符串
7.方法
函数的定义是大的或复杂的使用命名的函数表达式代替函数声明
// 从变量引用调用中区分的词汇名称
    const short = function longUniqueMoreDescriptiveLexicalFoo() {
      // ...
    };
立即调用的函数表达式
切记不要在非功能块中声明函数，将函数赋值给变量
// good
    let test;
    if (currentUser) {
      test = () => {
        console.log('Yup.');
      };
    }
永远不要定义一个参数为 `arguments
function foo(name, options, args) {
      // ...
    }
不要使用 `arguments`, 选择使用 rest 语法 `...` 代替
// good
    function concatenateAll(...args) {
      return args.join('');
    }
使用默认的参数语法，而不是改变函数参数
 // good
    function handleThings(opts = {}) {
      // ...
    }
❤避免使用默认参数的副作用-默认值不使用表达式，总是把默认参数放在最后，不要再赋值参数
var b = 1;
    // bad
    function count(a = b++) {
      console.log(a);
    }
永远不要使用函数构造器来创建一个新函数
❤书写规范-函数签名中的间距
    const x = function () {};
    const y = function a() {};
❤将传入的对象作为参数进行操作可能会在原始调用程序中造成不必要的变量副作用-因为是值传递
 function f2(obj) {
      const key = Object.prototype.hasOwnProperty.call(obj, 'key') ? obj.key : 1;
    }
很❤优先使用扩展运算符 `...` 来调用可变参数函数
// good
    new Date(...[2016, 8, 5]);
console.log(...x);
8.箭头函数
使用匿名函数时，转为用箭头函数，例如map这种高阶函数
 // good
    [1, 2, 3].map((number, index) => ({
      [index]: number,
    }));
❤书写规范-如果表达式跨越多个行，用括号将其括起来
 // good
    ['get', 'post', 'put'].map(httpMethod => (
      Object.prototype.hasOwnProperty.call(
        httpMagicObjectWithAVeryLongName,
        httpMethod,
      )
    ));
书写规范如果你的函数接收一个参数，则可以不用括号，省略括号
// good
    [1, 2, 3].map(x => x * x);

    // good
    [1, 2, 3].map(number => (
      `A long string with the ${number}. It’s so long that we don’t want it to take up space on the .map line!`
    ));
 // good
    [1, 2, 3].map((x) => {
      const y = x + 1;
      return x * y;
    });
避免箭头函数符号 (`=>`) 和比较运算符 (`<=`, `>=`) 的混淆。 
 const itemHeight = item => (item.height > 256 ? item.largeSize : item.smallSize);
9.类
方法返回了 `this` 来供其内部方法调用
// good
    class Jedi {
      jump() {
        this.jumping = true;
        return this;
      }

      setHeight(height) {
        this.height = height;
        return this;
      }
    }
只要在确保能正常工作并且不产生任何副作用的情况下，编写一个自定义的 `toString()` 方法也是可以的。
如果没有指定类，则类具有默认的构造器。 一个空的构造器或是一个代表父类的函数是没有必要的
10.模块
使用模块 (`import`/`export`)
// best
    import { es6 } from './AirbnbStyleGuide';
    export default es6;
不要使用通配符导入，导入导出模块名名字一致
只从一个路径导入所有需要的东西
// good
    import foo, { named1, named2 } from 'foo';
不要导出可变的引用。
const foo = 3;
    export { foo };
在单个导出的模块中，选择默认模块而不是指定的导出。使用default
 // good
    export default function foo() {}
❤所有的 `import`s 语句放在所有非导入语句的上边
❤书写规范多行导入应该像多行数组和对象一样缩进
// good
    import {
      longNameA,
      longNameB,
      longNameC,
      longNameD,
      longNameE,
    } from 'path';
11.迭代器和发生器
不要使用迭代器。 你应该使用 JavaScript 的高阶函数代替 `for-in` 或者 `for-of`例如reduce、map
12.变量定义
let与const变量定义分开；不使用全局变量var；定义变量时不需要提前-在你需要的使用定义变量，但是要把它们放在一个合理的地方。
不要链式变量赋值
❤避免使用不必要的递增和递减
避免在赋值语句 `=` 前后换行
注意变量提升
匿名函数表达式提升变量名，而不是函数赋值
很❤命名函数表达式提升的是变量名，而不是函数名或者函数体，函数声明提升其名称和函数体
13比较运算符
对于布尔值使用简写，但是对于字符串和数字进行显式比较
 // good
    if (name !== '') {
      // ...
    }

// good
    if (collection.length > 0) {
      // ...
    }
 // good
    if (isValid) {
      // ...
    }
如果存在声明 (例如. `let`, `const`, `function`, 和 `class`)，使用大括号来创建块 。
 // good
    switch (foo) {
      case 1: {
        let x = 1;
        break;
      }
      case 2: {
        const y = 2;
        break;
      }
      case 3: {
        function f() {
          // ...
        }
        break;
      }
      case 4:
        bar();
        break;
      default: {
        class C {}
      }
    }
❤三目表达式不应该嵌套，通常是单行表达式。-分离为两个三目表达式
const maybeNull = value1 > value2 ? 'baz' : null;
 // best
    const foo = maybe1 > maybe2 ? 'bar' : maybeNull;
避免不必要的三目表达式
// good
    const foo = a || b;
    const bar = !!c;
    const baz = !c;
❤使用该混合运算符时，使用括号括起来
// good
    const foo = (a && b < 0) || c > 0 || (d + 1 === 0);
14.代码块-if语句
当有多行代码块的时候，使用大括号包裹
如果一个 `if` 块总是执行一个 `return` 语句，那么接下来的 `else` 块就没有必要了。 如果一个包含 `return` 语句的 `else if` 块，在一个包含了 `return` 语句的 `if` 块之后，那么可以拆成多个 `if` 块

    // good
    function foo() {
      if (x) {
        return x;
      }

      return y;
    }

    // good
    function cats() {
      if (x) {
        return x;
      }

      if (y) {
        return y;
      }
    }
❤如果你的控制语句 (`if`, `while` 等) 太长或者超过了一行最大长度的限制，则可以将每个条件（或组）放入一个新的行。 逻辑运算符应该在行的开始。
  // good
    if (foo === 123 && bar === 'abc') {
      thing1();
    }

    // good
    if (
      (foo === 123 || bar === 'abc')
      && doesItLookGoodWhenItBecomesThatLong()
      && isThisReallyHappening()
    ) {
      thing1();
    }
❤不要使用选择操作符代替控制语句
// bad
    !isRunning && startRunning();

    // good
    if (!isRunning) {
      startRunning();
    }
15.注释
使用 `/** ... */` 来进行多行注释

    // good
    /**
     * make() returns a new element
     * based on the passed-in tag name
     */
    function make(tag) {

      // ...

      return element;
    }
❤书写规范使用 `//` 进行单行注释。 将单行注释放在需要注释的行的上方新行。 在注释之前放一个空行，除非它在块的第一行。
❤用一个空格开始所有的注释，使它更容易阅读
❤使用 `FIXME` 或者 `TODO` 开始你的注释可以帮助其他开发人员快速了解，如果你提出了一个需要重新审视的问题，或者你对需要实现的问题提出的解决方案。 这些不同于其他评论，因为他们是可操作的。 这些行为是 `FIXME: -- 需要解决这个问题` 或者 `TODO: -- 需要被实现`。
❤使用 `// TODO:` 注释解决问题的方法。
class Calculator extends Abacus {
      constructor() {
        super();

        // TODO: total 应该由一个 param 的选项配置
        this.total = 0;
      }
    }
class Calculator extends Abacus {
      constructor() {
        super();

        // FIXME: 这里不应该使用全局变量
        total = 0;
      }
    }
❤16.空格-书写规范
在主体前放置一个空格
使用 tabs (空格字符) 设置为 2 个空格
// good
    function baz() {
    ∙∙let name;
    }
// good
    function test() ∙{
      console.log('test');
    }

 // good
    dog.set('attr', {
      age: '1 year',
      breed: 'Bernese Mountain Dog',
    });
在控制语句（`if`, `while` 等）开始括号之前放置一个空格。 在函数调用和是声明中，在参数列表和函数名之间没有空格。
// bad
    if(isJedi) {
      fight ();
    }

    // good
    if (isJedi) {
      fight();
    }

用空格分离操作符
// good
    const x = y + 5;
在使用长方法连滴啊用的时候使用缩进(超过两个方法链)。 使用一个引导点，强调该行是方法调用，而不是新的语句。
❤在块和下一个语句之前留下一空白行。

    // good
    if (foo) {
      return bar;
    }

    return baz;
逗号之前避免使用空格，逗号之后需要使用空格。
/ good
    var foo = 1, bar = 2;
    var arr = [1, 2];
在对象的属性和值之间强化间距。
17.类型转换
在语句开始前进行类型转换
// good
    const totalScore = String(this.reviewScore);
使用 `Number` 进行类型铸造和 `parseInt` 总是通过一个基数来解析一个字符串。
// good
    const val = Number(inputValue);
布尔类型：
  // best
    const hasAge = !!age;
❤18.命名规范
避免单字母的名字。用你的命名来描述功能-函数名
 // good
    function query() {
      // ...
    }
在命名对象、函数和实例时使用驼峰命名法（camelCase）。
 // good
    const thisIsMyObject = {};
    function thisIsMyFunction() {}
只有在命名构造器或者类的时候才用帕斯卡拼命名法（
// good
    class User {
      constructor(options) {
        this.name = options.name;
      }
    }

    const good = new User({
      name: 'yup',
    });
不要使用前置或者后置下划线。
// good
    this.firstName = 'Panda';
不要保存 `this` 的引用。 使用箭头函数或者 [函数#bind]
文件名应该和默认导出的名称完全匹配
❤ function makeStyleGuide() {
      // ...
    }

    export default makeStyleGuide;

当你导出默认函数时使用驼峰命名法。 你的文件名应该和方法名相同。
当你导出一个构造器 / 类 / 单例 / 函数库 / 暴露的对象时应该使用帕斯卡命名法。


如果需要引用模板字符串本身，在需要时执行，可以像下面这样写。
// 写法一
let str = 'return ' + '`Hello ${name}!`';
let func = new Function('name', str);
func('Jack') // "Hello Jack!"

// 写法二
let str = '(name) => `Hello ${name}!`';
let func = eval.call(null, str);
func('Jack') // "Hello Jack!"
使行超过100个字符的字符串不应使用字符串连接跨多行写入


 ```javascript
    // bad
    const errorMessage = 'This is a super long error that was thrown because \
    of Batman. When you stop to think about how Batman had anything to do \
    with this, you would get nowhere \
    fast.';

    // bad
    const errorMessage = 'This is a super long error that was thrown because ' +
      'of Batman. When you stop to think about how Batman had anything to do ' +
      'with this, you would get nowhere fast.';

    // good
    const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';