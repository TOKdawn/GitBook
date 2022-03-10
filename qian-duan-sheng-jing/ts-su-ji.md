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
