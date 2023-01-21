---
title: 关于微信小程序在app.js修改后的全局变量值在其他页面的异步获取
order: 40
group:
  title: 微信小程序
---

# 关于微信小程序在 app.js 修改后的全局变量值在其他页面的异步获取

## 前言

大坑，在 app.js 修改全局对象后，在其他文件进行使用时发现不论如何都是 null，各种查资料都无法解决，猛然想起加载顺序时才后知后觉，index.js 的文件在使用 app.js 的全局变量时，全局变量还未被赋值，所以就得到了初始化的 null。

## 解决

问题既然发现了，解决的思路也就明确了，关于这种时间先后的问题，我们直接使用 Promise 异步处理来解决。基本思想是封装取值函数，在给全局变量赋值之后，用 resolve 函数将全局变量抛出，在其他文件中，使用该封装函数的 then 方法来获得正确的 Promise 对象，这样问题就解决了，下面上代码：

**app .js：**

```JavaScript
 getuserinfo: function () {
   return new Promise((resolve,reject)=>{
    wx.cloud.callFunction({
      // 要调用的云函数名称
      name: 'getopenid',
      // 传递给云函数的event参数
      data: {  }
    }).then(res1=>{
      this.globalData.id=res1.result;
      Userifms.where({
        userid: _.exists(res1.result)//查询数据库是否有该用户
      }).get().then(res=>{
        if(res.data.length!=0){
          this.globalData.userInfo=res.data[0]
          resolve(this.globalData.userInfo)//异步操作最重要的一步，赋值后抛出。
        }
      })
    })
 })
},
```

**index.js：**

```js
var app = getapp(); //初始化app不要忘记
const userinfos = app.getuserinfo().then((res) => {
  return res;
}); //成功得到Promise对象
```

> 注意到这一步时获取的仍是 Promise 对象，想要得到里面的全局对象需要再使用一次 then 方法，如下：
>
> ```js
> var userinfo = null;
> userinfos.then((res) => {
>   userinfo = res;
> });
> ```
>
> 也是在上一步的 res 中使用。

不得不说，异步问题真的不想碰到啊！~~
