---
description: 上一版实在过于又臭又长,循环嵌套太多了,在大佬的指点下进行了一下代码重构.
---

# css variable 解析2.0

### 整体思路

这一版其实压缩了一多半的代码量,底层思路其实也改动了很多.因为css variable最多只会有一层嵌套,而且一单匹配到正确值可以立刻退出,所以在没必要像上一版那样过多用for循环嵌套. 为了代码的整体性,新版设计成了只穿一个css key参数的版本,而整体的mostSpecificRule通过闭包获取. 因为用了递归的实现思路,而一个程序同时进行 _实际cssKey_ _实际cssvalue_ _css variable_ 以及\_css variable fallback\_等多种数据进行处理,所以乍一看其实还是有些吃力的.

### 具体逻辑

`color: var(--varOne,var(--varTwo,red))` 首先我们要明确函数的入参都会有什么

* color:具体的css key 要进行 getPropertyValue拿取值,
* \--varOne:cssVar key要进行 getPropertyValue拿真实值,
* var(--varTwo,red): 查不到值时的fallback 不需要进行getPropertyValue,
* red:真实的css value 或者fallback后的实际备选值,不需要进行getPropertyValue

函数处理的最一开始,通过是否包含`var(` 判断,来条件执行getPropertyValue方法

但实际上我们只能通过 var( 的检查判断出第三种情况不执行getPropertyValue方法,第四种情况还是会进行getPropertyValue操作,这是错误的

还好这里我们可以在函数返回的时候进行补救判断&#x20;

`return propertyValue || propertyCss.startsWith('--') ? propertyValue : propertyCss`

成功查值则肯定返回查询结果,对应情况一 和 情况二(有对应值的时候)&#x20;

查值不成功,则需要进一步判断是否以`- -`开头,&#x20;

不是就返回自身代表它是真实css value.(第四种情况)&#x20;

是那就返回查询结果 代表为情况二(无对应值的时候)

### 递归构建

最后在函数递归入口进行退回处理,

查到v.key直接替换,

查不到则用getPropertyCssValue(v.fallback)替换,完善整个递归流程

```
    const value = getPropertyCssValue(v.key)
    // Detect the need for fallback
    propertyValue = propertyValue.replace(
      v.text,
      value ? value : getPropertyCssValue(v.fallback)
    )
```

### 完整代码:

```
const getPropertyCssValue = (propertyCss: string): string => {
  //Screening fallback
  let propertyValue =
    propertyCss.indexOf(‘var(‘) == -1
      ? mostSpecificRule.style.getPropertyValue(propertyCss.trim()) // mostSpecificRule is a CSSStyleRule for DOM, comes from the Lexical Closure
      : propertyCss
  const cssVariables: {
    text: string //Varlables text
    fallback: string // Varlables fallback
    key: string //Varlables key
  }[] = []
  for (
    let index = propertyValue.indexOf(‘var(‘), left = index + 4, bracketsLevels = 0;
    index < propertyValue.length;

  ) {
    // not found
    if (index < 0) {
      break
    }
    if (propertyValue.charAt(index) === ‘)’) bracketsLevels—
    else if (propertyValue.charAt(index) === ‘(‘) bracketsLevels++
    if (bracketsLevels == 0 && index > left) {
      const text = propertyValue.substring(left - 4, index + 1)
      const inner = text.substring(4, text.length - 1)
      const separator = inner.indexOf(',')
      const length = inner.length
      const sp = separator < 0 ? length : separator
      cssVariables.push({
        text, //var(—one,var(—two,rgb(1,1,1)))
        fallback: inner.substring(sp + 1), //var(--two,rgb(1,1,1))
        key: inner.substring(0, sp).trim() //--one
      })
      //Process the next css Varlables
      index = propertyValue.indexOf('var(', index)
      left = index + 4
      continue
    }
    index++
  }
  if (cssVariables.length === 0) {
    //propertyCss is a  normal cssValue return itself
    //propertyCss is a cssVariable key return getPropertyValue()
    return propertyValue || propertyCss.startsWith('--') ? propertyValue : propertyCss
  }
  cssVariables.map(v => {
    const value = getPropertyCssValue(v.key)
    // Detect the need for fallback
    propertyValue = propertyValue.replace(
      v.text,
      value ? value : getPropertyCssValue(v.fallback)
    )
  })
  return propertyValue
}
```
