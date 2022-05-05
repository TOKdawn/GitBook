---
description: 给一个开源项目做的css variable支持,外部逻辑都没啥复杂的,主要难点在于解析variable值.
---

# css variable 解析

### CSS value合理类型

```
.two {
  color: var(—my-var, red); /* Red if —my-var is not defined */
}

.three {
  background-color: var(--my-var, var(--my-background, pink)); /* pink if --my-var and --my-background are not defined */
}

.three {
  background-color: var(--my-var, --my-background, pink); /* Invalid: "--my-background, pink" */
}
```

### 处理思路

首先对字符串进行解析,分割出最外层var(),记录一个css值内包含多少个成立的变量. 直接拿取`var(`位置,然后步进查询`)`中间遇到`(`做括号层级的增加,匹配到第一个符合条件的`)`结束,然后递归处理剩下的字符串

然后再逐个对var()值进行解析,根据css var的规则其实可以很简单的发现,文件的替换关系值采用从左到右的规则,具体的var()层级关系不关键,所以只要抽离 成对的`var(` 和 `)`然后再以 `,`字符做分割就好.

要注意的是可能会存在`rgba(1,1,1)`这样的css值,所以在进行逗号拆分时要排除掉括号内的逗号,正则:`/,(?![^(]*\))/`

最后在实际使用时顺序匹配关键字数组就可以了,以`--`开头的是css变量.反之为变量值.

拿到css var 具体值后用`getComputedStyle(element).getPropertyValue("--my-var")` 拿取目标dom上需要替换的css变量值就好

#### 源码:

```
let res = []
var splitCssValue = function(CssValue,startIndex){
  var cssStr = CssValue.slice(startIndex)
  let bracketsIndex = 0
  if (cssStr.indexOf('var(') >= 0 ){
    var start = cssStr.indexOf('var(');
    for(var I=start+4; I<cssStr.length; I++){
      //get effective var() form selector
      if(cssStr[i] == '('){
        bracketsIndex++
      }
      if(cssStr[i] == ')'){
        if(bracketsIndex == 0){
	  //This is a effective var()
          var end = i+1
          let valueString = cssStr.slice(start,end)
          let ValueIndex = 0
          let resSrt = ‘’
          //exclude ‘var(‘ and ‘)’ form cssValueString
          for(let ch = 0; ch < valueString.length; ch++){
            if(valueString[ch]==‘v’ && valueString.slice(ch,ch+4) == ‘var(‘){
              ValueIndex++
              ch+=3;
              continue;
            }
            if(valueString[ch] == ')' && ValueIndex>0){
              ValueIndex--
              continue;
            }
            resSrt+=valueString[ch]
          }
          var re = /,(?![^(]*\))/
            res.push({
              start: start+startIndex,
              end: end+startIndex,
              varString: valueString,
              varValue:resSrt.split(re)
            })  
            return splitCssValue(CssValue,startIndex+end)
        }else{
          bracketsIndex--
        }
      }
    }
    //brackets number discord law
    return  false
  }
  return true;
}


function doTurn(){
  const varReg = /var\(.*?\)/gi
  if (cssValue && varReg.test(cssValue)) {
    const originalValue = cssValue.replace(/^\s+|\s+$/g, '')
    let res = ''
    this.cssVariableList = []
    console.log('originalValue', originalValue)
    if (this.analysisCssVariables(originalValue, 0)) {
      console.log('cssVariableList', this.cssVariableList)
      this.cssVariableList.map(CssVariable => {
        if (CssVariable.valueName.length > 0) {
          for (let I = 0; I < CssVariable.valueName.length; I++) {
            const name = CssVariable.valueName[I].replace(/^\s+|\s+$/g, ‘’)
            if (name.slice(0, 2) == ‘—‘) {
              const css = getComputedStyle(node).getPropertyValue(name)
              console.log('css,name', css, name)
              if (css) {
                res = originalValue.replace(
                  originalValue.substring(CssVariable.startIndex, CssVariable.endIndex),
                  css
                )
                return
              }
            } else {
              if (name) {
                res = originalValue.replace(
                  originalValue.substring(CssVariable.startIndex, CssVariable.endIndex),
                  name
                )
                return
              }
            }
          }
        }
      })
      console.log('last Res', res)
      return res
    } else {
      return cssValue
    }
  }
}

```
