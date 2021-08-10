# 原型和原型链
## 原型是一个对象，其他对象可以通过它实现属性继承，而且任何一个对象都可以成为原型
## 值类型的类型判断用typeof，引用类型的类型判断用instanceof
## 对象都是通过函数创建
## prototype与__proto__的区别
   两者都是对象类型的属性，并非所有的对象类型都有prototype属性，一般只有function对象才有prototype属性(除非主动赋值)，它指向的是一个对象，将来会被多个该function的实例所继承（或者说该对象处于多个实例的原型链上）；__proto__才是真正的原型链的实际指针
instanceof的判断队则是：沿着A的__proto__这条线来找，同时沿着B的prototype这条线来找，如果两条线能找到同一个引用，即同一个对象，那么就返回true。如果找到终点还未重合，则返回false。
所有构造器/函数的__proto__都指向Function.prototype，它是一个空函数（Empty function）
let fun = Function.prototype.bind()
如果你以上述方法创建一个函数，那么可以发现这个函数是不具有 prototype 属性的
* Object 是所有对象的爸爸，所有对象都可以通过 __proto__ 找到它
* Function 是所有函数的爸爸，所有函数都可以通过 __proto__ 找到它
* Function.prototype 和 Object.prototype 是两个特殊的对象，他们由引擎来创建
* 除了以上两个特殊对象，其他对象都是通过构造器 new 出来的
* 函数的 prototype 是一个对象，也就是原型
* 对象的 __proto__ 指向原型， __proto__ 将对象和原型连接起来组成了原型链
￼
原型继承 new操作符干的事
xiaoming ↘
xiaohong -→ Student.prototype ----> Object.prototype ----> null
xiaojun  ↗
new Student()创建的对象还从原型上获得了一个constructor属性，它指向函数Student本身，并且创建的对象只有__proto__属性
创建原型对象
基于Student扩展出PrimaryStudent，可以先定义出PrimaryStudent
function PrimaryStudent(props) {
    // 调用Student构造函数，绑定this变量:
    Student.call(this, props);
    this.grade = props.grade || 1;
}
PrimaryStudent.prototype = Student.prototype;
是不行的！如果这样的话，PrimaryStudent和Student共享一个原型对象
借助一个中间对象来实现正确的原型链，这个中间对象的原型要指向Student.prototype
# 继承的实现
继承方式

那么面试官就该问了，你知道的继承方式有几种？优缺点是什么？
第一种方式：构造函数继承
function Person (name, age) {
    this.name = name;
    this.age = age;
}
Person.prototype.say = function(){
    console.log('hello, my name is ' +this.name);
};
function Man(name, age) {
    Person.apply(this, arguments);
}
//Man.prototype = new Person('pursue');
var man1 = new Man('joe');
var man2 = new Man('david');
console.log(man1.name === man2.name);//false
man1.say(); //say is not a function

评论：不靠谱，因为方法继承不过去
第二种方式：拷贝继承 把他爹的东西复制一份给自己

for(varname in Person.prototype){
Man1.prototype[name] = Person.prototype[name];
}

评论：
你爸有个陶瓷杯子挺好看，你拿着你爸的杯子去陶瓷店里跟人家说去给我按照这个杯子也给我做一个一模一样的，这不叫继承
 
第三种方式：原型链继承 就是我画图那种

Obj2.prototype = obj.prototype;

评论：
继承的意思是你爸有眼睛鼻子，你生出来的时候也有，但是你去韩国割了个拉皮，一回家发现你爸也有双眼皮了，这不叫继承。叫公用。

第四种方式：有人叫组合继承

function Man(name, age) {
    Person.apply(this, arguments);
}
Man.prototype = new Person();

评论：
看着靠谱了，就有一个小瑕疵，man的constructor 不是Man 就是说，你的杯子非得说是你爸的杯子造出来的而不是人家模具造出来的。

第五种方式： 寄生组合继承 常用经典继承方式

function Man(name, age) {
    Person.apply(this, arguments);
}
Man.prototype = new Person();//A
Man.prototype.constructor = Man;//b.


很多人 其实不明白//A这句，这样只是很暴力的在对属性进行覆盖。

如果我换种写法你们就秒懂了，

Man.prototype = Object.create(Person.prototype);//a.
相当于
function create(obj){
    function T(){};
    T.prototype = obj;
    return new T();
}

评论：最后一种是我们 经常用的，不过多解释了
第五种方式： 寄生组合继承 常用经典继承方式

function Man(name, age) {
    Person.apply(this, arguments);
}
Man.prototype = new Person();//A
Man.prototype.constructor = Man;//b.


很多人 其实不明白//A这句，这样只是很暴力的在对属性进行覆盖。

如果我换种写法你们就秒懂了，

Man.prototype = Object.create(Person.prototype);//a.
相当于
function create(obj){
    function T(){};
    T.prototype = obj;
    return new T();
}

## new操作符
1、创建一个空对象,并且 this 变量引用该对象,同时还继承了该函数的原型。
2、属性和方法被加入到 this 引用的对象中。
3、新创建的对象由 this 所引用,并且最后隐式的返回 this 


// PrimaryStudent构造函数:
function PrimaryStudent(props) {
    Student.call(this, props);
    this.grade = props.grade || 1;
}

// 空函数F:
function F() {
}

// 把F的原型指向Student.prototype:
F.prototype = Student.prototype;

// 把PrimaryStudent的原型指向一个新的F对象，F对象的原型正好指向Student.prototype:
PrimaryStudent.prototype = new F();

// 把PrimaryStudent原型的构造函数修复为PrimaryStudent:
PrimaryStudent.prototype.constructor = PrimaryStudent;

// 继续在PrimaryStudent原型（就是new F()对象）上定义方法：
PrimaryStudent.prototype.getGrade = function () {
    return this.grade;
};

// 创建xiaoming:
var xiaoming = new PrimaryStudent({
    name: '小明',
    grade: 2
});
xiaoming.name; // '小明'
xiaoming.grade; // 2

// 验证原型:
xiaoming.__proto__ === PrimaryStudent.prototype; // true
xiaoming.__proto__.__proto__ === Student.prototype; // true

// 验证继承关系:
xiaoming instanceof PrimaryStudent; // true
xiaoming instanceof Student; // true
