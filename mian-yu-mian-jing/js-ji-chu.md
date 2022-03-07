---
description: 重学JS一年一年 说过多少次了 学会了么嘛? 换汤不换药啊
---

# JS基础

#### 原型与原型链

```
function Person() {//构造函数

}
Person.prototype.name = 'Kevin';//实例原型
var person1 = new Person();//对象实例
var person2 = new Person();
person1.age = ’26’;
console.log(person1.__proto__ === Person.prototype); // true
console.log(Person === Person.prototype.constructor); // true
```

构造函数通过 .prototype添加原型方法 通过构造函数实例化的对象都含有 **proto** 指向其实例原型 实例原型可以通过constructor寻找到其对应的构造函数 进行属性查询时会先在实例内查询,查询不到则会找其原型,原型没有则继续向上查询原型的原型,直到Object.prototype

> null 与 undefined null 表示没有对象,不该有值 一般用在传参表示参数不是对象,不可解析,或者对象原型的终点 undefined 表示缺少值,应该有值,但是未定义, 一般用在未赋值变量 ,或者传参时应该穿的参数没提供,函数默认返回值等

#### 执行上下文

#### 闭包

定义上 函数内访问非函数参数和自身局部变量的变量就是闭包 实际上 我们说的闭包 一个函数即使创建它的上下文已经销毁，它仍然存在（比如，内部函数从父函数中返回以及在代码中引用了自由变量叫闭包

#### 宏观任务与微观任务

我们应当有这样的一个概念,JS代码是有宿主环境管理和JS引擎自我管理两种形式,而产生异步操作的两种方式setTimeout和Promise则是分别由宿主环境以及JS引擎维护的,setTimeout会产生一个宏观任务,宿主环境会分配出一个对应的执行序列处理响应的js代码,而Promise则是产生出一个属于当前JS引擎线程的异步任务,宏观任务下可以包含多个微观任务,同为宏观任务,依照对应顺序执行,同一宏观任务下的微观任务也会依照对应顺序执行,但是要注意的是后一个宏观任务下的微观任务永远也不会比前一个宏观任务下的微观任务先执行.

This的绑定主要取决于函数的调用栈以及调用位置,调用位置的判断遵循以下四个原则

* 默认绑定 ,函数在全局作用域调用时,无显示调用关系,则this指向全局对象
* 隐式绑定,独立声明的函数在以 对象.函数名() 的形式调用时函数内this指向此对象,但是值得注意的是

```
	function foo() {
		console.log(this.a);	
	}
	var obj = {
		a: 2,
		foo:foo
	};
	var bar = obj.foo;
	var a = "oops,global"
	bar(); //"oops,global"
```

在 对象.函数名 这种组合做右值的时候其实本质上只是完成了一个函数本身的引用,并不能改变函数内this指向,所以会采用默认绑定,函数传参的本质也是将实参作为右值赋给形参,所以 setTimeout( obj.foo, 100 ) 这类的写法也都是遵从默认绑定规则而不是隐式绑定

* 显示绑定, call方法和apple方法
* New 绑定
* 参数预设

```
function foo(p1,p2) {
      this.val = p1+p2
}
var bar = foo.bind(null , "p1")
var baz = new bar("p2")
baz.val //p1p2
```

#### JS中的对象

JS并不像Java，C++等面向对象语言通过类来进行对象描述，而是采用原型的方式，而JS语言的作者又因为早期的公司政治原因不得不去向java靠拢，所以在原型的基础上又引入了new this等特性，使其更像java。 JS中对象具有唯一标识性，即便完全相同的两个对象，也不是同一个对象。 对象具有状态，同一对象可以有不同的状态表达。 对象具有行为，对象可能因为其行为产生变迁。 JS的状态和行为在C++中叫成员变量和成员函数，Java中叫属性和方法。JS把状态和行为都抽象成了属性，所以Object内的变量和函数都是他的属性。 **JS内的对象具有高度的动态性，对象可以在运行过程中添加更改其状态和行为，JS甚至提供了getter和setter这种访问器属性** 对于JS来说属性并未简单的名称和值的键值对，JS的对象有两类属性。

* 数据属性： 具有四个特征Value(属性的值)Writable(可写性)Enumerable(可枚举性)Configurable(可配置性)
* 访问器属性：具备Enumerable(可枚举性)Configurable(可配置性)而且具有getter,setter 函数，在变量被读取和赋值时执行 Js对象在运行时就是一个”属性的集合”，属性以字符串或者symbol 为key，以特征值为value
