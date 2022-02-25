### this指针
* 定义
> this 指针存在于函数中，用以标识函数运行时所处的上下文。函数的类型不同，this指向规则也不一样：对于普通函数，this始终指向全局对象window；对于构造函数，this则指向新创建的对象；对于方法，this指向调用该方法的对象。另外，Function对象也提供了call、apply和 bind 等方法来改变函数的 this 指向，其中，call 和 apply 主动执行函数，bind一般在事件回调中使用，而 call 和 apply的区别只是参数的传递方式不同。如果往深的去理解，无论什么函数，this是否被改变， 本质上，this 均指向触发函数运行时的那个对象。而在函数运行时，this 的值是不能被改变的


* 源由
ECMAScript 的类型分为语言类型和规范类型。
ECMAScript 语言类型是开发者直接使用 ECMAScript 可以操作的。其实就是我们常说的Undefined, Null, Boolean, String, Number, 和 Object。
而规范类型相当于 meta-values，是用来用算法描述 ECMAScript 语言结构和 ECMAScript 语言类型的。规范类型包括：Reference, List, Completion, Property Descriptor, Property Identifier, Lexical Environment, 和 Environment Record。
没懂？没关系，我们只要知道在 ECMAScript 规范中还有一种只存在于规范中的类型，它们的作用是用来描述语言底层行为逻辑。
今天我们要讲的重点是便是其中的 Reference 类型。它与 this 的指向有着密切的关联。


* Reference
所以 Reference 类型就是用来解释诸如 delete、typeof 以及赋值等操作行为的。
这里的 Reference 是一个 Specification Type，也就是 “只存在于规范里的抽象类型”。它们是为了更好地描述语言的底层行为逻辑才存在的，但并不存在于实际的 js 代码中。
这段讲述了 Reference 的构成，由三个组成部分，分别是：
* base value:base value 就是属性所在的对象或者就是 EnvironmentRecord，它的值只可能是 undefined, an Object, a Boolean, a String, a Number, or an environment record 其中的一种。
* referenced name:是属性的名称。
* strict reference:是否为严格模式

举个例子：
var foo = 1;

// 对应的Reference是：
var fooReference = {
    base: EnvironmentRecord,
    name: 'foo',
    strict: false
};
再举个例子：
var foo = {
    bar: function () {
        return this;
    }
};
 
foo.bar(); // foo

// bar对应的Reference是：
var BarReference = {
    base: foo,
    propertyName: 'bar',
    strict: false
};


* Reference 组成部分的方法，比如 GetBase 和 IsPropertyReference。
这两个方法很简单，简单看一看：
1.GetBase
GetBase(V). Returns the base value component of the reference V.
返回 reference 的 base value。
2.IsPropertyReference
IsPropertyReference(V). Returns true if either the base value is an object or HasPrimitiveBase(V) is true; otherwise returns false.
简单的理解：<font color=Red>如果 base value 是一个对象，就返回true</font>。
3.GetValue
除此之外，紧接着在 8.7.1 章规范中就讲了一个用于从 Reference 类型获取对应值的方法： GetValue。
简单模拟 GetValue 的使用：
var foo = 1;

var fooReference = {
    base: EnvironmentRecord,
    name: 'foo',
    strict: false
};

GetValue(fooReference) // 1;
GetValue 返回对象属性真正的值，但是要注意：
调用 GetValue，返回的将是具体的值，而不再是一个 Reference
这个很重要，这个很重要，这个很重要。

* 如何确定this的值
1.计算 MemberExpression 的结果赋值给 ref
2.判断 ref 是不是一个 Reference 类型
2.1 如果 ref 是 Reference，并且 IsPropertyReference(ref) 是 true, 那么 this 的值为 GetBase(ref)

2.2 如果 ref 是 Reference，并且 base value 值是 Environment Record, 那么this的值为 ImplicitThisValue(ref)

2.3 如果 ref 不是 Reference，那么 this 的值为 undefined
总结就是：计算括号左边表达式的值，如果是对象，就取对象的值如果不是对象是单个方法名就是EnvironmentRecord，返回window，如果是表达式或者其他，就是undefined，然后转为window对象
在全局执行上下文中，this 的值指向全局对象。(在浏览器中，this引用 Window 对象)。
在函数执行上下文中，this 的值取决于该函数是如何被调用的。如果它被一个引用对象调用，那么 this 会被设置成那个对象，否则 this 的值被设置为全局对象或者 undefined（在严格模式下）。