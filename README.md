# Promise 

## 1、什么是Promise ？ 

- 在ES6中一个非常重要和好用的特性就是Promise
  - 但是在初次接触Promise会一脸懵逼， Promise是异步编程的一种解决方案
  - 通常我们在异步编程中都会使用到Promise

 

## 2、网络请求的回调地狱？

- 以下的代码一层嵌套一层， 一层嵌套一层， 可读性很差， 我们就称之为回调地狱。 Promise 可以非常优雅的来解决这个问题。 

```
setInterval(function(){
  setInterval(function(){
    setInterval(function(){
        setInterval(function(){

      },1000)
    },1000)
  },1000)
},1000)
```



## 3、Promise的基本用法

我们先来看看Promise最基本的语法， 这里我们使用定时器来模拟异步事件

- 假设瞎买你的data是从网络上1秒后请求的数据
- `console.log` 就是我们的处理方式

```
//这是我们以前没有Promise时， 异步函数的处理方式
setTimeout(function(){
  let data = 'hello promise';
  console.log(data);
},1000)
```

- 下面我们把它换成Promise

  ```
  new Promise((resolve, reject)=>{
    setTimeout(function(){
    	let data = 'hello promise'
    	let isSuccess = true
    	if(isSuccess){
         resolve('操作成功--'+data)
    	}
    	else{
        reject(('操作失败--'+data))
    	}
     
    },1000)
  }).then(data=>{ //当Promise 里面的异步操作完成并成功就会来到then
    console.log(data)
  }).catch(error=>{//当Promise 里面的异步操作完成并成功就会来到catch
    console.log(data)
  })
  ```

  - 这个使用了promise的代码会让我们感觉像脱裤子放屁一样， 多此一举

    - 首先， 使用promise包裹后， 代码的复杂度明显要比原来的复杂
    - 其次， 下面的Promise代码中的`resolve`  `reject`  `then` `catch` 都是些什么东西。

    > 我们先不管第一个复杂度的问题， 因为这样一个屁大点的程序根本看不出Promise真正得的作用。 

##4、promise 的创建定义

- 当我们在创建Promise 的时候， 是需要我们传递一个函数作为参数的， 如下：

  ```
  // 1.创建Promise时， 需要传递一个函数类型的参数
  // 2.这个函数类型参数里面也要求要有两个参数， 且这两个参数也是函数类型的。
  new Promise((resoleve, reject)=>{
    //在promise 里做异步处理的操作
    // 如果异步操作成功就调用 resolve方法
    // 操作失败就调用 reject 方法
  })
  ```

- Promise的使用示例

  - 使用promise之前, 代码虽然要精简一些,但是没有层次感, 难以阅读

    ```
    setTimeout(()=>{
    	//resolve('第一次成功')
    	reject('第一次失败')
    	setTimeout(()=>{
          //resolve('第二次成功')
          reject('第二次失败')
    		setTimeout(()=>{
              //resolve('第二次成功')
              reject('第二次失败')

            },1000)
    	},1000)
    },1000)
    ```

  - 使用Promise之后, 代码更有层次感, 便于阅读

    不论多少层嵌套, 只需要使用`.then`  `.then`  一层层的处理, 如果出错, 只需要在catch里面统一处理即可. 

    ```
    new Promise((resolve , reject)=>{
          setTimeout(()=>{
            resolve('第一次成功')
            //reject('第一次失败')
          },1000)
        })
          .then((data)=>{
            console.log(data);
            return new Promise((resolve, reject)=>{
              setTimeout(()=>{
                resolve('第二次成功')
                //reject('第二次失败')
              },1000)

            })
          })
          .then((data)=>{
            console.log(data);
            return new Promise((resolve, reject)=>{
              setTimeout(()=>{
                //resolve('第三次成功')
                reject('第三次失败')
              },1000)

            })
          })
          .then(data=>{
            console.log(data)
          })
          .catch(error=>{
            console.log(error);
          })
    ```







## 5、Promise的三种状态



- 首先, 当我们开发中有异步操作时, 我们就可以给异步操作包装一个Promise
  - 异步操作之后会有三种状态
    - `pending`:  等待状态, 比如: 正在进行的网络请求, 或者定时器没有到时间
    - `fulfill`: 满足状态, 当我们主动回调了resolve时, 就处于该状态, 并且会回调`.then()`
    - `reject` : 拒绝状态, 当我们在主动回调reject时, 就处于该状态, 并且会回调`.catch()` 



## 6、Promise的链式调用

- 普通 Promise 调用

  ```
  new Promise((resolve , reject)=>{
        setTimeout(()=>{
          resolve('第一次成功')
          //reject('第一次失败')
        },1000)
      })
        .then((data)=>{
          console.log(data);
          return new Promise((resolve, reject)=>{
            setTimeout(()=>{
              resolve('第二次成功')
              //reject('第二次失败')
            },1000)

          })
        })
        .then((data)=>{
          console.log(data);
          return new Promise((resolve, reject)=>{
            setTimeout(()=>{
              //resolve('第三次成功')
              reject('第三次失败')
            },1000)

          })
        })
        .then(data=>{
          console.log(data)
        })
        .catch(error=>{
          console.log(error);
        })
  ```

- Promise 链式调用

  > 成功直接使用 return Promise.resolve(). 失败直接使用 return Promise.reject() 或使用 throw('错误内容')

  ```
  new Promise((resolve , reject)=>{
        setTimeout(()=>{
          resolve('第一次成功')
          //reject('第一次失败')
        },1000)
      })
        .then((data)=>{
          console.log(data);
          return Promise.resolve('第二次成功')
        })
        .then((data)=>{
          console.log(data);
          return Promise.resolve('第三次成功')
          // 如果在链式 调用种某一环节失败了, 可以使用
          // return Promise.reject('失败了')
          // 也可以使用 throw 抛出错误
        })
        .then(data=>{
          console.log(data)
        })
        .catch(error=>{
          console.log(error);
        })
  ```

- 更简洁的Promise 链式调用

  > 直接省略掉 Promise.resolve

  ```
  new Promise((resolve , reject)=>{
        setTimeout(()=>{
          resolve('第一次成功')
          //reject('第一次失败')
        },1000)
      })
        .then((data)=>{
          console.log(data);
          return '第二次成功'
        })
        .then((data)=>{
          console.log(data);
          return '第三次成功'
        })
        .then(data=>{
          console.log(data)
        })
        .catch(error=>{
          console.log(error);
        })
  ```

  ​

## 7、Promise all 使用

我们平时可能有这样的需求, 同时发出请求1 和 请求2, 当请求1和请求2同时成功了就去执行成功的操作, 当其中有一个发生错误就立即停止并处理错误, 这种情景就可以使用 Promise.all 将所有的异步任务包裹起来一起做

- 当Promise.all 中所有的异步任务都执行成功完成后, 会把对应的任务结果通过`.then(resulets=>{})` 回调出来, results 是一个数组, 里面存储的是对应的结果, 顺序和任务的顺序一致. 如果只要其中有一个错误就会通过`.catch(err=>{})` 回调出来, catch中的err 是最早出现reject的异步任务的错误信息.

```

Promise.all([

      // 异步任务1
      new Promise((resolve, reject)=>{
        // 使用定时器模拟任务
        setTimeout(()=>{
          resolve('第一件事完成了')
        },1000)
      }),

      // 异步任务2
      new Promise((resolve,reject)=>{
        setTimeout(()=>{
          resolve('第二件事完成了')
        },2000)
      }),
      // 异步任务3
      new Promise((resolve,reject)=>{
        setTimeout(()=>{
          resolve('第三个成功')
          //reject('第三件事完失败')
        },2000)
      }),
    ]).then(results=>{  // 执行的结果全部存在数组中, 序号与任务添加的新婚徐一直
      console.log(results);
    })
      .catch(errors=>{
        console.log(errors);
      })
```





































