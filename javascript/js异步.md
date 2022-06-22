# js异步编程
- ## js异步简介
  - 异步是潜在的长时间运行的方法，在调用异步方法时，并不需要阻塞等到长任务运行完成，而是可以在长任务执行时同时执行其他方法，在异步方法执行完后会显示执行结果
  - 异步方法的使用场景：
    - 当在同步方法中执行长任务时，在同步方法执行完成之前，对界面中元素进行的操作都会被阻塞
  - 实现异步操作的方式：
    - event handler：
      - 通过为事件注册一个函数，可以在异步方法完成后调用
      - 早期异步api会通过event handler来实现异步操作，如XMLHttpRequest
    - callback：
      - 当需要向callback中传入callback时，代码将会十分的难以阅读
    - promise：
      - 在基于promise实现的异步操作中，可以在调用异步方法后立刻返回promise对象，如果异步方法没有执行完成，此时promise中的status为pending，显示异步方法还在执行。可以为promise指定一个callback，当异步操作调用完成，会执行callback
- ## fetch方法的使用
```javascript
function fetch_baidu() {
        let fetch_obj = fetch("https://www.baidu.com")
        console.log(fetch_obj)
        fetch_obj.then(response => {
            console.log('status: '+response.status)
            console.log("data: " + response.body)
        })
        console.log("fetch finished...")
    }
```
- ## 链式异步调用和错误处理
  - 如果想要为异步方法的handler中添加异步方法，不要在then函数中添加then函数，而是采用promise chain的方式
  ```javascript
  // 例如，response.json()方法也是异步方法，返回promise
  let http_promise=fetch("https://baidu.com")
  http_promise.then(response=>{
      if(!response.ok) {
          throw new Error("return code error")
      }
      let json_promise=response.json()
      json_promise.then(json=>{
          console.log(json)
      })
  })
  // 不要在then方法中嵌套then方法，而是将其转化为promise chain
  let http_promise=fetch("https://baidu.com")
  http_promise.then(response=>{
      if(!resposne.ok) {
          throw new Error("return code error")
      }
      return response.json()
  }).then(json=>{
      console.log(json)
  })
  // 通过该方法可以防止then方法中多层嵌套
  ```
  - 如果想要为promise定义错误处理的handler，可以在末尾catch方法中传递handler
  ```javascript
  let http_promise=fetch("https://baidu.com")
  http_promise.then(response=>{
      if(!resposne.ok) {
          throw new Error("return code error")
      }
      return response.json()
  }).then(json=>{
      console.log(json)
  }).catch(error=>{
      console.log(error)
  })
  ```
- ## Promise
  - Promise的状态
    - pending：该promise已经被创建并且关联了异步方法，但是该方法还没有执行完成
    - fulfilled：该异步方法执行成功，then()被调用
    - rejected：异步方法执行失败，catch()被调用
    > 此处，promise成功和失败是取决与讨论的api的，例如，对于fetch()方法，即使服务端返回404错误，该promise也会被认为是成功的，但是如果是网络问题导致请求发送失败，该方法会被认为是失败的
   - Promise.all和Promise.any
     - 如果不想同步的执行异步方法，可以使用Promise.all或者Promise.any来组织promise
     - Promise.all接收一个promise数组，只有当数组所有的promise执行成功之后，then()中的方法会被执行，如果其中任何一个promise执行失败，catch()方法被执行
     - Promise.any接收一个promise数组，当数组中任何一个promise执行成功之后，then()方法会被执行，如果所有的promise都失败，catch会被执行
 - ## async和await关键字
   - 在方法开头加上async关键字，可以标识该方法为异步方法，在async方法内部，可以使用awiat关键字。await关键字可以放在一个返回promise方法调用之前，被await标识的方法不再返回promise而是会返回fulfilled value。
   - 通过await/async关键字，可以像编写同步代码一样编写异步代码。
   - async标识的方法会返回promise
 - ## Promsie构造函数
   - Promise构造函数接受一个函数(resolve,reject)=>{},该函数被作为Promise对象的执行器，resolve和reject都是只接受一个参数的函数
 - ## Worker
   - 默认情况下，整个window是单线程的，如果在同步执行一个长任务的情况下，其他所有的操作都会被阻塞，可以开启多线程来来实现并行执行
   - async/await和promise并不是通过多线程来实现异步操作的，通过async和await并不会创建一个新的线程，故而想要通过多线程提高性能，不能通过简单使用await/promise来实现
   - 为了保证线程安全，worker之间是隔离的，相互之间无法访问其他worker的变量，也无法访问dom（window、document、page），workder之间只能通过message交流