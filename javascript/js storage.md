# js storage
- ## js在客户端存储数据的方式
  - Web Storage api：通常用来存储简单的数据，以name-value的形式存储

- ## Web Storage api
  - 存储种类：
    - localStorage
      - 该种方式即使浏览器被关闭，数据仍然不会丢失，下次重新打开浏览器时数据仍然存在
      - 无论刷新还是关闭页面，数据仍然不会丢失
    - sessionStorage：
      - 该种方式只会在浏览器打开时存储数据，一旦浏览器被关闭，存储的数据会消失
      - 该方式存储的数据在刷新页面后仍然会存在，但是当页面关闭后，数据会丢失
  - 存储信息和删除信息方式：
    - localStorage：
        ```javascript
        localStorage.setItem(name,value)
        localStorage.getItem(name)
        localStorage.removeItem(name)
        ```
    - sessionStorage:
        ```javascript
        sessionStorage.setItem(name,value)
        sessionStorage.getItem(name)
        session.removeItem(name)
        ```
  - 对于不同的域名，Storage是分别储存的，位于同一域名下的页面可以共享Storage中存储的信息，但是位于不同域名的页面Storage信息无法共享
- ## indexedDB
  - 数据库操作（数据库操作都是异步的，不必在数据库操作进行时阻塞，可以为数据库操作请求添加handler来处理完成的请求）
    - 打开db：
      ```javascript
      // 该方法会打开name指定的数据库，如果该数据库不存在，
      // 会创建一个新的数据库
      let db_request=window.indexedDB.open(name,version)
      ```
    - 为数据库操作添加handler：
        ```javascript
        // 可以为数据库操作添加success、error的handler
        db_request.addEventHandler('success',handler)
        db_request.addEventHandler('error',error_handler)

        // 为upgradeneeded事件指定handler
        // 如果一个数据库还没有被设置，或者该数据库打开
        // 的版本号大于其本身版本号
        db_request.addEventHnalder((e)=>{
            db=e.target.result
            obj_store=db.createObjectStore(name,{keyPath:"id",autoIncrement:true})

            obj_store.createIndex(name,value,{unique:false})
        })
        ```
        - 如果打开数据库操作完成，会触发success事件，打开的数据库对象可以通过db_request.result来访问
        - 如果打开数据库操作失败，会触发error事件
        - 数据库如果没有被设置，会抛出upgradeneeded事件，并且upgradeneeded事件在success事件之前
    -  向数据库的ObjectStore中添加记录：
       -  首先要开启一个事务，通过db.transaction([objectStoreName...],mode:"readwrite")
          ```javascript
          let tr=db.transaction([obj_store...],"readwrite")
          ```
       -  通过事务获取object store对象
          ```javascript
          let obj_store=tr.objectStore(obj_store)
          ```
        - 调用add操作向object store中添加记录，并且为返回的request对象的事件添加监听器
          ```javascript
          let add_request=obj_store.add({
            key1:value1,
            key2:value2,
            ...
          })
          // 为request的success事件添加handler
          add_request.addEventHanlder('success',success_handler)

          // 为transaction的complete事件添加handler
          tr.addEventHandler('complete',handler)
          // 为事务的error事件添加handler
          tr.addEventHandler('error',err_handler)
          ```
      - 遍历objectStore对象中的记录
        ```javascript
        let object_store=db.transaction(object_store_name).objectStore(obj_store_name)
        object_store.openCursor().addEventHandler('success',(e)=>{
          // 获取request.result的结果值
          const cursor=e.target.result
          if(cursor) {
            // 操作数据

            // 迭代到下一个项目
            // 如果还存在下一个记录，那么request的success
            // 事件会再次被触发
            cursor.continue()
          }
        })
        ```
      - 删除objectStore中的记录
        ```javascript
        objectStore.delete(primary_key)
        tr.addEventHandler('complete',handler)
        ```

    
