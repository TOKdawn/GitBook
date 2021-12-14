---
description: 优化请求XHR
---

# XHR

XMLHttpRequest 可进行二次封装

.timeout ---> 设定请求超时自动关闭时间,可以防止一些情况下接口太慢卡死渲染进程



.upload.onprogress || .onprogress  --->传入处理函数可用于做请求的进度条,一般用在上传下载文件

```
// onprogress回调函数为
(lengthComputable, loaded, total)=>{
  //lengthComputable 是否可获取参数
  // loaded 已上传
  //total 总数
 }
```

一般对其进行二次封装 &#x20;

```
const wrapOnProgress = (onProgress) => ({ lengthComputable, loaded, total }) => { 
        onProgress(loaded, lengthComputable ? total : Infinity)
     }
```

然后向wrapOnProgress 传入 (now, total )=>{根据两个数值做展示逻辑即可}
