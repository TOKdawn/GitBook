# TS 速记



* 类型别名(type)和接口(interface)都可以用作复杂类型的二次封装,也可以通过不同的方式继承成新的类型(extends &),但是接口是可以二次扩展的,类型别名则不行
* 断言 ( xx as number ) 断言可能会组织一些强转,可以通过二次断言绕过(const a = (expr as any) as T)先断言成any在把any断言成目标类型
* 字面量类型 let color = “red” | “blue”; 一般用作指定参数或者返回值的函数

```
function compare(a: string, b: string): -1 | 0 | 1 {
  return a === b ? 0 : a > b ? 1 : -1;
}
```

* 字面量推断

```
declare function handleRequest(url: string, method: “GET” | “POST”): void;

const req = { url: “https://example.com”, method: “GET” };
handleRequest(req.url, req.method);

// Argument of type ‘string’ is not assignable to parameter of type ‘”GET” | “POST”’.
```

在你直接初始化一个对象req时,TypeScript 会假设这个对象的属性的值未来会被修改,所以只会把变量method设置为number类型而不是 “GET”,而函数接收的类型是“GET” | “POST” 所以报错. 解决方法如下

```
// Change 1:
const req = { url: “https://example.com”, method: “GET” as “GET” };//声明时就加上类型断言
// Change 2
handleRequest(req.url, req.method as “GET”);

const req = { url: “https://example.com”, method: “GET” } as const;//把整个对象作为一个类型字面量
handleRequest(req.url, req.method);
```

* strictNullChecks关 如果一个值可能是 null 或者 undefined，它依然可以被正确的访问.反止则不行
* 非空断言操作符（后缀 !） `console.log(x!.toFixed());` 加上!后会自动抛弃null 和undefined
* 真值收窄 可以通过 !! 等真值判断进行值过滤 同样的可以进行类型收窄的操作还有 in操作收窄 instance操作收窄 赋值语句收窄
* 当联合类型中的每个类型，都包含了一个共同的字面量类型的属性，TypeScript 就会认为这是一个**可辨别联合（discriminated union）**，然后可以将具体成员的类型进行收窄。
* never 类型,表示不可能存在状态,因此可以做为switch的default处理,以确保数据进行了理想处理

```
interface Triangle {
  kind: “triangle”;
  sideLength: number;
}
 
type Shape = Circle | Square | Triangle;//增加了一个switch未包含的类型
 
function getArea(shape: Shape) {
  switch (shape.kind) {
    case “circle”:
      return Math.PI * shape.radius ** 2;
    case “square”:
      return shape.sideLength ** 2;
    default:
      const _exhaustiveCheck: never = shape;
      // 报错 因为任何类型都不能赋值给never .
      return _exhaustiveCheck;
  }
}
```

* 函数表达式 `fn: (a: string) => void`
* 函数可以带有属性

```
type DescribableFunction = {
  description: string;
  (someArg: number): boolean;
};
function doSomething(fn: DescribableFunction) {
  console.log(fn.description + “ returned “ + fn(6));
}
```

* 泛型

```
function firstElement<Type>(arr: Type[]): Type | undefined {
  return arr[0];
}
```

* 泛型约束

```
//函数返回两个值中更长的那个。为此，我们需要保证传入的值有一个 number 类型的 length 属性。我们使用 extends 语法来约束函数参数：
function longest<Type extends { length: number }>(a: Type, b: Type) {
  if (a.length >= b.length) {
    return a;
  } else {
    return b;
  }
}
```

* 函数重载 尽可能的使用联合类型替代重载

```
function makeDate(timestamp: number): Date;
function makeDate(m: number, d: number, y: number): Date;
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}
const d1 = makeDate(12345678);
const d2 = makeDate(5, 5, 5);
const d3 = makeDate(1, 3);

// No overload expects 2 arguments, but overloads do exist that expect either 1 or 3 arguments.
```

实现签名必须和重载签名兼容,并且涵盖所有重载签名的类型

#### 几种特殊类型:

* void 不会返回任何值,隐式返回undefined
* Object 任何非原始类型(string,number,bigint,boolean,symbol,null,undefined)的都是object
* unknown 可以表示任何值,类似any,但是更安全,对其任何操作都合法
* never 函数不返回任何值
* Function 函数
* parameters 形参 arguments 实参

### 函数

#### 调用签名

Js内函数除了可以被调用还可以有自己的属性值,

```
type DescribableFunction = {
  description: string; //函数的属性值
  (someArg: number): boolean; //函数本身基础描述
};
function doSomething(fn: DescribableFunction) {
  console.log(fn.description + “ returned “ + fn(6));
}
```

#### 构造签名

js 函数在描述时如果加上new关键字,则会被认为是一个构造函数,用于产生一个新对象

```
type SomeConstructor = {
  new (s: string): SomeObject; //构造函数
};
function fn(ctor: SomeConstructor) {
  return new ctor(“hello”); //构造函数返回对象
}
```

#### 泛型约束

!要注意泛型是用于关联多个值之间关系的,如果一个类型菜蔬在函数签名内只出现一次没和任何东西产生关联,则不需要使用泛型

```
function greet<Str extends string>(s: Str) {//错误的
  console.log(“Hello, “ + s);
}

function greet(s: string) {//正确的
  console.log(“Hello, “ + s);
}
```

可以使用继承构建泛型以约束一个含有特定参数的对象类型

```
function longest<Type extends { length: number }>(a: Type, b: Type) {
  if (a.length >= b.length) {
    return a;
  } else {
    return b;
  }
}
```

#### 函数重载

函数重载由重载声明和函数实现两部分组成,要注意函数实现时参数必须兼容所有声明

```
function makeDate(timestamp: number): Date;//声明
function makeDate(m: number, d: number, y: number): Date;//声明
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {//实现
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  } else {
    return new Date(mOrTimestamp);
  }
}
const d1 = makeDate(12345678);
const d2 = makeDate(5, 5, 5);
const d3 = makeDate(1, 3);
```
