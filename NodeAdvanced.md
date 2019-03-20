Node Advanced 
===
###### tags: `Node`, `Learning`

Nodejs internal
===
- V8: a open source project js engine created by google, can execute js code outside of browser(30%JS + 70% C++)
- libuv: c++ open source project that gives node access to the operating system(ex: file system, networking, other stuff) (100% C++)

### example of how nodejs and v8, libuv works
- choose a nodejs module: pbkdf2 function as example
- pbkdf2 is a hashing algroithm in node's crypto library
- go into nodejs's source code, find two folder: lib and src
> lib: contains all the js definitions of function and modules
 src: c++ implementations of those functions in lib
- for example, pbkdf2 function definition in lib/internal/crypto/pbkdf2.js: [link](https://github.com/nodejs/node/blob/master/lib/internal/crypto/pbkdf2.js)
- find the actual implementation of pbkdf2 in src (c++ code): [link](https://github.com/nodejs/node/blob/master/src/node_crypto.cc)

> **V8** project is used to translate the node js values(Boolean, function, object ...etc) and translate it into c++ equivalents. V8 is used to interpret and execute JS code
> **libuv** is used for accessing the filesystem and some aspects of concurrency

![](https://i.imgur.com/eEtpBU9.jpg)


### Event loop
- Node use  **single thread** running **one event loop**
- lifecycle of event loop
```javascript=
// node myFile.js (execute the node program)

// 記錄一些操作的 array, program 開始跑之前就存在
const pendingTimers = [];
const pendingOSTaskes = [];
const pendingOperations = [];

// node will first execute the code inside of it, event loop does not immediately executed yet
// New timers, tasks, operations are recorded from myFile running
// node 會先跑一次程式，把符合 timers, OStask, operations 的呼叫紀錄進上面的 array 中
myFile.runContents();

// 一個檢查 event loop 是否要繼續的 function，裡面檢查是否有還沒跑完的 timer, task , operations
function shouldContinue(){
    // check for three things:
    // check One: Any pending setTimeout, setInterval, setImmediate ?
    // check Two: Any pending operating system tasks? (ex: server listening to port)
    // check Three: Any pending long running operations?(ex: fs module)
     
    return pendingTimers.length || pendingOSTasks.length || pendingOperations.length
}

// then nodejs's event loop start
// entire body executes in one 'tick' (eventloop 整個跑一次叫 one tick)
// event loop 開始跑，會call shouldContinue 來檢查要不要繼續跑 loop
while(shouldContinue()){
    // have 5 steps:

    // 1) node looks at pending timers and sees if any functions are already to be called. (only look for setTimeout and setInterval)

    // 2) node looks at pendingOSTasks and pendingOperations and calls relevant callbacks
    
    
    // 3) node pause execution. Continue when...
    // - a new pendingOSTask is done
    // - a new pendingOperation is done
    // - a timer is about to complete
    //  node will not run event loop as quick as possible, it will wait at step3 to see if some events is triggerd

    // 4) Look at pendingTimers. (only look for setImmediate)
    
    // 5) handle any 'close events'
}

// exit back to terminal
```
![](https://i.imgur.com/WFrfcp8.png)


## Is node really single thread?
- node Event Loop : is single threaded
- Some of node Framework/std Lib: NOT single Threaded

### Prove
- if you run one pbkdf2 function takes x second
- if node is really single thread, it will take 2x second to finish two pdkdf2 functions
![](https://i.imgur.com/MFGB2JT.png)

- but in reality, run two pbkdf2 function will not take 2x second, but just a little bit more than x second
![](https://i.imgur.com/REr0NYQ.png)
![](https://i.imgur.com/axebq04.png)

- run five pbkdf2 functions, four of them will take 2x second, the last of them will take 3x seconds
![](https://i.imgur.com/rKSo2Pm.png)
![](https://i.imgur.com/xk7AUDC.png)


### libuv
- libuv will control a thread pool with default 4 thread
- can use follow code to modify the thread pool's size
```javascript=
process.env.UV_THREADPOOL_SIZE = numberOfThread
// uv means libuv
```
- modify our thread to five, and run five pbkdf2 functions
```javascript=
process.env.UV_THREADPOOL_SIZE = 5
```
![](https://i.imgur.com/F6nvpF7.png)

> then all five threads get foughly equal amounts of CPU times, so we saw everything complete at the same time (可以想像一個CPU 要做 2.5 個 pbkdf2 function, 所以每個function 會花 2.5x seconds)

### some libuv threads pool question
1. 會使用到 libuv 的 thread 的包含在： (1) pendingTimers 跟 (2) pendingOperations 中的某些 function，default 會起 4 個 thread 來跑。（如上述說到的 pbkdf2 這個 hashing function, 及大多數的 fs module）

![](https://i.imgur.com/VIIfCiS.png)

2. 至於在 pendingOSTaskes 中的那些 function（如 networking、listen on some port, https module 之類的），則是透過 libuv **交給 OS 來跑**，libuv 不會直接控制他們（所以跟 libuv 中的 thread pool 完全沒關係），會起多少個 thread 完全是由 OS 來控制。例如同時跑 6 個 https.request 打 google，六個 function 的完成時間幾乎會是一樣的，因為他不需要透過 libuv 的 thread 來控制，而是直接把工作交給 OS 來控制

![](https://i.imgur.com/jFTTw3c.png)
> 平常使用的 app.listen(port) 就是 pendingOSTasks 中的一員，因為這個 task 會一直被放在 pendingOSTasks 這個 array 中，所以 node event loop 會一直跑下去

>所以 Event loop 會用 **single thread** 來跑「檢查是否還有工作還沒做完」的過程 (跑一次叫一個 tick)，但在這三個 array （pendingTimers, pendingOperations, pendingOSTaskes） 中的工作則會是用 **multithread** 來跑。其中 **pendingTimers** 跟 **pendingOperations** 會由 libuv 的 thread pool 來執行（預設四個thread），而 **pendingOSTaskes** 中的工作則是被交給 OS 直接執行

### Some node interesting behavior
- if we run the code below:
```javascript=
const https = require('https');
const crypto = require('crypto');
const fs = require('fs');
const start = Date.now();

function doRequest(){
    https.request('https://www.google.com', res=>{
        res.on('data', ()=>{});
        res.on('end', ()=>{
            console.log('Request', Date.now()-start);
        });
    }).end();
}



function doHash(){
    crypto.pbkdf2('a','b',100000, 512, 'sha512', ()=>{
        console.log('Hash:', Date.now()-start);
    })
}

doRequest();

fs.readFile('multitask.js','utf8',()=>{
    console.log('FS', Date.now()-start);
})

doHash();
doHash();
doHash();
doHash();
```
- we will get the result like this
![](https://i.imgur.com/qBm0XAD.png)

- reason: 
    - https (doRequest) use OS, so it do not invole the threadpool
    - FS module and pdkdf2 (doHash) use threadpool
    - fs.readFile will go to hardrive for 2 time, in the first time it will get some statistic of that file, and the second time it will streamed back the file contents. so there are two timming that node will wait
    - libuv 中的 4 個 thread，假設 #1 先處理 fs.read， #2~#4 處理前三的 hash，在#1遇到fs的第一次停頓時， #1 就會先去處理最後一個 hash 工作，直到 #2~#4 中的某個 thread 處理完他的 hash 後，發現 fs 還需要被處理，就會接手處理 fs，僅管 fs 還會有第二次停頓，但因為沒有其他工作要做了，所以這個 thread 就會等待並把 fs 處理完
    ![](https://i.imgur.com/rH9kOlh.png)


Improving node performances
===
- there are two ways to improve node performance
1. use node in 'cluster' mode - recommend way, can use in real world project (do not work well with nodemon, so dont use nodemon with node cluster)
2. use worker thread - experimental way

## node cluster
- clustering: will start multiple node processes
- Cluster manager(left hand side): parent process, responsible for monitoring the health of individual instances of application(right hand side), these application are running on our singular computer. the cluster manager itself doesnt running application code, but monitoring the instances(start, stop, restart them, send them data)
![](https://i.imgur.com/5na6T1g.png)

- normal application (singal node applicaiton, no clustering)
![](https://i.imgur.com/jyUqgVr.png)

- cluster application
with the cluster module(standard library module build in node js) cluster manager will call fork() function, node will executes our code a second time that then starts up our worker instance
 ![](https://i.imgur.com/8TA7gif.png)
 
- check if a instance is cluster manager: 
use cluster.isMaster to check, true if the instance if cluster manager, false is it is a worker instance

- when to use cluster:
if you have some routes inside of your app that usually thake a while to process, but you have other routes that are very quick, if use node in normal mode, than the quick routes are punished to wait for the slow route to complete. By using cluster you can start multiple instances of your server that can handle more request

### Testing cluster's performance: 
- ab: apache benchmark, ab是Apache自带的压力测试工具，macOS 下可直接使用
```shell=
ab -c 50 -n 500 localhost:3000/
# 用 ab 這個壓測工具，打 localhost:3000/ 共 500 個 request，維持同時間有 50 個 request 在執行
```

- testing code:
為了方便起見，我們先只起一個 child，並且設定每個child 的 thread pool 都只有一個 thread。（正常來說每個child都會有 4個 thread）
```javascript=
// all of the children inside of our cluster will only have one thread avalible. set this to make our experiment more easier
process.env.UV_THREADPOOL_SIZE = 1;
const cluster = require('cluster');

// is the file being executed in master mode?
if(cluster.isMaster){
    // if in master mode, Cause index.js to be executed again but in child(worker, slave) mode
    // every single child has their own separate thread pool, in this case, there is only one thread in each thread pool
    cluster.fork();
}else{
    // if child mode
    // Im going to act like a server and do nothing else
    const express = require('express');
    const crypto = require('crypto');
    const app = express();

    app.get('/', (req, res)=>{
        // use this hash function to simulate some real process in server
        crypto.pbkdf2('a','b', 100000, 512, 'sha512', ()=>{
            res.send('Hi there');
        })
    })

    app.get('/fast', (req, res)=>{
        res.send('This was fast');
    })
    
    app.listen(3000);
}


```

- 首先要先測baseline：跑一個request 要花的時間，假設是 x sec
```shell=
ab -c 1 -n 1 localhost:3000/
```
- 接下來測 ab -c 2 -n 2 localhost:3000/，同時發兩的request給 server。因為只有一個 child 在執行，所以第一個 request 會花 x sec，第二個request會等第一個跑完才開始跑，所以會花 2x sec
![](https://i.imgur.com/v5OnnUF.png)

- 接下來變成 2 個 child 來跑（多呼叫一次 cluster.fork()），發現只要花 x sec，因為同時有兩個 child 在執行
![](https://i.imgur.com/7eQQEjK.png)

- cluster 看起來很好，但用太多會有反效果。現在我們同時起 6 個 child，再跑一次 code, 並同時發 6 個 request ab -c 6 -n 6 localhost:3000/，發現每個 request 要花的時間都變成 2x+ sec, 因為電腦的 CPU 達到上限，一個 core 要同時處理多個request，導致雖然每個 request 要花的時間是相同的（同時開始處理），但每個要花的時間都變長了。

- 假如我們換回兩個 child，但一樣跑6個 request (request ab -c 6 -n 6 localhost:3000/)，發現會先執行兩個 request，再執行 2 個，最後再執行 2 個（因為只有兩個 child），但先執行的 request 很快被處理掉，最後執行的 request 甚至比上一個狀況（6個 child）要花的時間更少或差不多，可見 child 並不是越多越好，這跟電腦本身的的配置有關（CPU, core）
![](https://i.imgur.com/oEsXACH.jpg)

### PM2 module
- 一個 open source，很好的控管 cluster 的 module, a cluster manager solution, can help manage health of child
- very widely use in production
- 啟動 pm2：
```shell=
 pm2 start index.js -i 0
 # -i 不是必要的
 # -i 用來指定要開幾個 instance的
```
- 通常會用 -i 0 ，讓 pm2 決定要起幾個 child instance，預設個數會跟電腦的 logical cpu cores 個數一樣 

> logical 跟 physical core 不一樣，假設電腦有兩個 physical core，每個 core 可以同時跑 2 個 threads，則有 2*2 = 4 個 logical cores
> #locigal cores = #physical cores * #thread can process on per physical at the same time
> 
- 一些command
```shell=
pm2 list # 列出正在運行的 cluster 的狀態
pm2 show projectName # 這個 project 的詳細狀況
pm2 monit # 開啟一個 monitor 來看 cluster 跟 logs
pm2 delete projectName #關閉某個專案
```
- pm2 會自動 restart 那些 crashed 的 cluster

## Worker thread
- 除了 cluster 之外的另一種提升 performance 的方式，但是還在實驗階段，通常不會用在 production
- npm i [webworker-threads](https://www.npmjs.com/package/webworker-threads)

![](https://i.imgur.com/zre3ebd.png)
- apps thread 就是 node 的 event loop's single thread
- Worker thread 是透過 webworker-threads 創造出來的 thread（電腦裡的其他 thread），可以用來做那些 blocking work 、 heavy calculations work 來避免 event loop 被 block
- 要記得的是某些 js 的 function 已經是透過 libuv 的 thread pool 執行（如pbkdf2、networking），所以無法再透過這個 worker thread 變更快。worker 可以用在code 需要處理的一些heavy商業邏輯上
- Worker 裡面的 thread 無法直接取得外面的變數，而是要透過 postMessage 跟 onmessage 溝通。
- onmessage 是要寫 function 的地方，這個 function 會在對向執行 postMessage 的時候被呼叫。例如在 server 端（ worker interface）呼叫 postMessage，則 Worker 端的 onmessage 就會被執行

# Blog Project
![](https://i.imgur.com/yVvPqpx.png)
![](https://i.imgur.com/WZO1M52.png)
# Data Caching with Redis
## Why
- Caching 可以提高 DB 的 read 效率
- 以這個專案來說
    - 每次使用者 refresh page，前端都會送兩個 request
        - 一個是 /api/current_user 來驗證身份
        - 一個是 /api/blogs 來拿到所文章
    - 而且這兩個 request 都會分別 query MongoDB



![](https://i.imgur.com/NpRPvk9.png)

## Index
- 預設每筆資料 primary key 會建立 index
- index 就像 hash table 一樣，可以直接用 key-value pair 查找
    - 如果我們直接透過 primary key id 來查找時，速度是非常快速的
![](https://i.imgur.com/IYsWtKk.png)

- 當我們要查找的欄位沒有建立 index 時
    - 如我們想用 blog 的 title 來查找
    - 則 db 就**只能用 scan** 的方式來查找，其效率很低，為 O(n)
    - 其中一個解決方式是**對該欄位也建立 index**
    - 如此查找 title 就能夠很快速
![](https://i.imgur.com/xvUmEpr.png)

- 然而，建立 Index 會帶來一些壞處
    - 由於要維護 index，因此 db 的**寫入速度會變慢** (寫完資料後還要去修改 index)
    - 並且會使用**更多空間及 memory**
    - 也常發生我們要搜尋的 query 無法找到適合的 index 來建立

## 使用 cache
- 另外起一個 caching server
![](https://i.imgur.com/B750p2l.png)
- redis: In memory tiny data store
    - 因為 data 存在 memory 裡，所以 server 重啟之後 data 會消失
    - 因為在 memory 裡，所以 redis 操作讀寫很快

![](https://i.imgur.com/BDYBBKf.png)
## 使用 redis
- `redis-server` 啟動 server
- 在 node 中，安裝 `redis` 或 `ioredis` 套件
    - `redis` 中的 function 都還是用 callback
    - 可以使用 `utils` 中的 `promisify` 將 redis 套件的功能包成 promise
    - 或直接使用 `ioredis`

```javascript=
 const redis = require('redis');
 const redisURI = 'redis://127.0.0.1:6379' // localhost
 const util = require('util');
 const client = redis.createClient(redisURL);
 client.get = util.promisify(client.get); // 使 get function 變成 promise 樣式
```

- 基本使用
    - set 跟 get
        - 直接在 redis 中加入一對 key, value
        - `client.set('key', 'value')`
        - 相等於 `{'key': 'value'}`
        - ![](https://i.imgur.com/Ly4QAGJ.png)
    - hset 跟 hget (較好用)
        - 在 redis 中加入一組 object
        - `client.hset('obj_name', 'key', 'value')`
        - 相較於 `{obj_name: {'key': 'value'}}`
        - ![](https://i.imgur.com/eiNI408.png)
    - set 跟 hset 可以並存在 redis 中
        - ![](https://i.imgur.com/rIh50cp.png)
    - 清除所有 cache
        - `client.flushall()`
    - 清除特定 key 的 cache
        - `client.del(key);`
    - 加上 TTL 
        - `client.set('key', 'value', 'EX', time_in_second)`
        - 如 `client.set('color', 'red', 'EX', 5)` 會在 redis 中存活 5 秒
        - EX = expired
- redis 裡面只能存 string 或 數值
    - 無法直接把 js object 存進去
    - 通常會先 `JSON.stringify(obj)` 再儲存
    - 取回時，再用 `JSON.parse(string)` 變回 JSON

### 如何 cache query
![](https://i.imgur.com/2zLD9An.png)

- key 要是有規律且唯一的
- value 是 mongoDB 回傳的值，是一個 Array

### 在 route 中加入 redis （需要大幅改進的版本）
- 一是以下的 code 並沒有 update cache 的機制，因此當 user 新增一篇 blog， express 只會拿到 cache 中舊的資料
- 二是以下的 code 無法重複利用，需要 refactor
- 三是以下的 caching 機制無法擴充，因為我們只用 user_id 當 key

```javascript=
app.get('/api/blogs', requireLogin, async (req, res) => {
    const redis = require('redis');
    const redisURI = 'redis://127.0.0.1:6379';
    const client = redis.createClient(redisURI);
    const util = require('util');
    client.get = util.promisify(client.get); // 把使用 callback 的 get function 變成 promise

    // 先檢查  redis 中有沒有這個 query 的結果
    const cachedBlogs = await client.get(req.user.id);

    // 有的話，就直接 response
    if (cachedBlogs) {
      console.log('serving from cache');
      return res.send(JSON.parse(cachedBlogs));
    }

    // 沒有的話，向 mongo DB query 再 return，並且把結果 cache 進 redis 中
    const blogs = await Blog.find({ _user: req.user.id });
    console.log('from mongodb');
    res.send(blogs); // 先回覆
    client.set(req.user.id, JSON.stringify(blogs)); // 再 cache
  });
```

### 在 route 中加入 redis （正確版）
![](https://i.imgur.com/PT3Arz4.png)

- 讓 redis code 變的 reuable
    - 因為所有的 redis code 都跟 mongoose 有關
    - 我們希望把這兩個動作綁在一起
    - mongoose 本身的 query 有一個特性，假設 query 如下，**其實 query 需要加上 exec() 才會真正被執行**
    - 不論是用 `.then` 或用 `async/await`，當下 query 都有執行 exec() 指令
    - 因此我們可以**複寫 exec()** 函式，讓每次 exec 時都會先去檢查 redis
```javascript=
// 定義一個 query ，尚未執行
const query = Person.
  find({ occupation: /host/ }).
  where('name.last').equals('Ghost').
  where('age').gt(17).lt(66).
  where('likes').in(['vaporizing', 'talking']).
  limit(10).
  sort('-occupation').
  select('name occupation');
  
 // 執行此 query
 // 方法一
 query.exec((err, result)=> console.log(result));
 // 方法二, 會自動呼叫 exec
 query.then(result => console.log(result));
 // 方法三, 會自動呼叫 exec
 const resut = await query
```

- 讓 cache 會 expire
    - 可以**使用 TTL**
    - 對於某些特定的動作，需要 **reset 我們的 cache**
        - 例如 post，當 user 新增一篇 blog 應該要馬上更新 cache 中的內容

- 讓 cache 機制更有擴充性
    - 不同的 query 要對應到不同的 result
    - 例如 findOne 如果搭配上不同的 where 條件，則在 redis 中的 key:value 也不同
    - 使用 mongoose 中的 `query.getOptions()`
    - query.getOptions() 會返回一個 object，其內容都是我們query 中的條件，如 `{ find: {field: 'value'}, where: 'some_condition'}`
    - 我們**可以把 query.getOptions() 的結果 JSON.stringify 之後，當作 redis 的 key**
        - 然而 query.getOtipns 中不會寫出這個 query 是對哪個 collection 下的
        - 例如 blog.find(id) 跟 user.find(id) 如果 id 依樣，則 query.getOptions() 的值一樣
        - **因此我們要把 collection 也加進去 key 中**
        - ![](https://i.imgur.com/ui8fXoU.png)


#### 複寫 query.exec()
- 因為這是一個**被應用到 app 中許多部分的基本功能**，所以可以寫在 `services/` 這個資料夾底下
    - 就跟 passport ㄧ樣
- 建立 `services/cache.js`，嘗試 override exec()，使他在執行前都會 console.log
```javascript=
const mongoose = require('mongoose');

const { exec } = mongoose.Query.prototype;
// exec is a reference to the original exec function
// 原本的 mongoose.Query.prototype.exec 是存一個地址，指向原始的 exec function 的位置
// 現在將變數 exec 也指向這個原始 exec functioni 的位置
// 所以現在有兩個 pointer 指向同一個 function

mongoose.Query.prototype.exec = function () {
  // 複寫 mongoose.Query.prototype.exec 指向的位置，將他指向一個新的 function
  // 而變數 exec 仍然指向原始 exec function 的位置
  // 使用 function 而不是 arrow function，因為我們需要使用到 this
  console.log('IM about to run a query');
  console.log(this.mongooseCollection.name); // 列出這個 query 是對哪個 collection 下的
  console.log(this.getQuery()); // 列出 query 的細節
  
  // function 中的 this 會指向呼叫這個 function 的 Query
  // 呼叫原始 exec function，並將 this 綁定在現在的 Query 上
  return exec.apply(this, arguments);
};
```

#### 找出 unique 的 redis query key
- 使用**query.getOptions() 的結果加上 collection_name 後再 JSON.stringify ，當作 redis 的 key**
```javascript=
mongoose.Query.prototype.exec = function () {
    // 列出這個 query 是對哪個 collection 下的
  console.log(this.mongooseCollection.name); 
  // 列出 query 的細節
  console.log(this.getQuery()); 

  // 組合出 redis key - 使用 getQuery() + Collection name 組合
  // 不能直接把 collection name 加進 getQuery 中，因為這樣會真正影響到這個 query 中的參數
  // 所以要把 getQuery 的結果複製一份起來再修改
  // 把 this.getQUery() 的結果跟 {collection: value} 都 assign 給一個新的 object， Object.assign 會把第二個參數起的值都複製進第一個參數中
  const key = Object.assign({}, this.getQuery(), {
    collection: this.mongooseCollection.name,
  });

  console.log(JSON.stringify(key)); // redis key

  // 呼叫原始 exec function，並將 this 綁定在現在的 Query 上
  // arguments 是所有被傳進這個 function 的參數
  return exec.apply(this, arguments);
};
```

#### 加入 redis
- 在 exec 中加入 redis 的邏輯跟之前依樣
    - 在實際去 mongodb 中 query 之前，先檢查看看 redis 中有沒有 cached
    - 如果有就返回 cache 的值
    - 如果沒有就去 query db，把結果存在 redis 中，再返回 query 的結果
- 加入 redis 時要注意的一個問題是
    - exec 預設應該要回傳一個 mongoose document object，這個 object 中有很多可以使用於 db 操作的函數
    - ![](https://i.imgur.com/TNoq2UB.png)
    - 但 redis 中儲存跟取出的是 JSON（JSON-string）
    - ![](https://i.imgur.com/8c260lZ.png)
    - 所以要把 redis 返回的 JSON 變成 mongoose document type
        - code 中使用的 `new this.model(JSON.parse(vale))` 就跟我們之前建立新的資料是一樣的，如同 `new Blog({title: "Hi", content: "there"})`
        - 然而，要注意 redis 中的 value 可能有兩種形式，因為 mongoose 針對不同 query 可能會返回
            - single Object: {}
            - array of Objects: [{}, {}, {}] 
            - 如 findById() return only one document. While find() return multiple document in an array
            - 如此會導致存在 redis 中的值也有兩種，因此我們不能直接把 redis 取出的值變成 mongoose document，因為在 array of object 時會出錯
        - 所以要將 redis 的結果轉換成 array of document 或 a document 再返回
```javascript=
mongoose.Query.prototype.exec = async function () {

  const key = JSON.stringify(Object.assign({}, this.getQuery(), {
    collection: this.mongooseCollection.name,
  }));

  // 先檢查  redis 中有沒有這個 query 的結果
  const cacheValue = await client.get(key);

  // 有的話，就直接 return
  if (cacheValue) {
    // exec 應該要返回 mongoose document type
    // 但從 redis 拿出來(再 parse)的是 JSON，而不是 mongoose document type

    const result = JSON.parse(cacheValue);

    // 檢查 result 是不是 array of object
    // 是個話要將 array 中的每個 object 轉換成 document。
    // 否則 result 就是 JSON object，直接轉換成 document
    return Array.isArray(result)
      ? result.map((d) => new this.model(d)) // 回傳 array of mongoose documents
      : new this.model(result); // 回傳一個 mongoose document
      
    // new this.model(json_obj)  可將 json 轉換成 mongoose document
    // 等同於 new Blog({
    //   title: 'Hi',
    //   content: 'There'
    // })
  }

  // 沒有的話，向 mongo DB query，並且把結果 cache 進 redis 中

  const result = await exec.apply(this, arguments);

  // 要把 result 存進 redis 之前，要先 stringify
  client.set(key, JSON.stringify(result));
  return result;
};

```

#### 決定哪些 query 要被 cache
- 上述的做法，會讓所有的 query 都被 cache
- 讓 query 中可以 chain 一個新的 `cache` function
    - 例如 `const query = Blog.find({ title: Hi}).cache()`
    - 有加上 cache() 的代表該 query 想要被我們 cache 住
    - 且該 cache() function 後面要可以再 chain 上其他 function
- 作法
    - 在 Query.prototype 中加上一個 cache 的 property function
    - 如果有執行這個 property function，就會在該 query 上加上一個 property (如 useCache) 並設為 true
        - `this.useCache = true`
        - 由於使用 `this`，因此會指向呼叫該 property 的 query
    - 因此在 exec 時，只要檢查該 query 上的 useCache property 是否為 true，就知道使否要使用 cache
    - 為了讓該 cache() function 後面要可以再 chain 上其他 function
        - 最後要 `return this`

```javascript=
mongoose.Query.prototype.cache = function () {
  this.useCache = true;
  return this;
};

mongoose.Query.prototype.exec = async function () {
// 檢查該 query 有沒有要用 cache，若沒有就直接去 mongoDB 中查找
  if (!this.useCache) {
    return exec.apply(this, arguments);
  }
// 使用 redis，以下略
}
```

#### Cache Expiration
- 某些情況下，我們希望 cache 會強制過期
    - 例如某人新增一篇 blog，則希望他原本 cache 住的 blog list 會過期
- **cache 的資料結構因專案而不同**，在這個專案下我們可以透過下面的結構輕易達到 cache expiration
    - - key 中記憶 user_id，每個 user_id 對應的 value 是一個 object，每個 object 都是 query 指令對應到 query 結果
    - ![](https://i.imgur.com/C4AiWnJ.png)

- 我們希望 cache() function 中可以讓使用者輸入最頂層的 key
    - 如 `Blog.find({ _user: req.user.id }).cache({
      key: req.user.id, 
    });`
    - redis 結構變成 {key: {query1: value1, query2: value2}}
```javascript=
mongoose.Query.prototype.cache = function (options = {}) {
  this.useCache = true;
  // 如果有指定 key 就用，沒有就用 '' 當 key
  this.hashKey = JSON.stringify(options.key || ''); 
  return this;
};

mongoose.Query.prototype.exec = async function () {

  if (!this.useCache) {
    return exec.apply(this, arguments);
  }

  const key = JSON.stringify(Object.assign({}, this.getQuery(), {
    collection: this.mongooseCollection.name,
  }));

    // 取得 hashkey 中的 key 中的值 {hashKey: {key: value}}
  const cacheValue = await client.hget(this.hashKey, key); 
  if (cacheValue) {
    const result = JSON.parse(cacheValue);

    return Array.isArray(result)
      ? result.map((d) => new this.model(d)) 
      : new this.model(result); 
  }


  const result = await exec.apply(this, arguments);
  
  // 使用 hset, 結構變成 {hashKey: {query1: value1, query2: value2}}
  client.hset(this.hashKey, key, JSON.stringify(result));
  return result;
};

```

- 並在 `/services/cache.js` 中 export 一個 function
```javascript=
module.exports = {
// 刪除特定的 cache
  clearHash(hashKey) {
    client.del(hashKey);
  },
};
```
- 於是當某個時候需要清掉 cache 時，可以直接 import 這個 function 並使用
    - 如在 blog.js 中，當使用者新增 blog 成功後，就使用 `clearHash(req.user.id)` 來把以該使用者為 id 的 cache 清掉，使得新的 blog 可以馬上顯示
    - **我們會在確定 blog 新增成功後再清 cache**

- 比起在不同 route 底下呼叫 `clearHash` function，使用 `middleware` 會更加的方便且直觀 
    - 實現 `middleware/cleanCache.js`
```javascript=
const { clearHash } = require('../services/cache');
module.exports = async (req, res, next) => {
  clearHash(req.user.id); // 清理 cache
};
```
- 並且我們希望要在 blog 實際被 save 進 mongoDB 之後，再 clean cache
- 因為 route handler 本身也就是一個 middleware，所以我們可以在 blog 成功被儲存進 db 後呼叫 next 來觸發下一個 middleware (cleanCache)
    - 如果 blog 儲存失敗，則不會清 cache

```javascript=
// blogRoute.js

app.post('/api/blogs', requireLogin, async (req, res, next) => {
    const { title, content } = req.body;

    const blog = new Blog({
      title,
      content,
      _user: req.user.id,
    });

    try {
      await blog.save();
      res.send(blog);
      next(); // 如果儲存成功，就進到下一個 middleware (cleanCache)
    } catch (err) {
      res.send(400, err);
    }
  }, cleanCache); // 串起下一個 middleware
```

## Testing
### Unit test VS Integration test
![](https://i.imgur.com/Ah1ofys.png)
![](https://i.imgur.com/NHsOIbL.png)
- unit test: 單元測試，測試 code 的一小的部分，如一個 function
- integration test: 整合測試，測試 code 中的功能，及測試多個 unit 是否能正確地一起運作
- 這次我們使用 Jest 這個測試框架，其運作跟 mocha 非常像
### headless browser testing

![](https://i.imgur.com/xif9g2h.png)

- Chromium 是一個開源的 browser
    - google chrome 是基於 chromium 的發展
- headless browser
    - 指的是沒有畫面渲染的瀏覽器
    - 操作及指令等等都是透過 program
    - 因為沒有渲染，所以執行起來更快速，適合在測試時使用
- headless browser testing
    - 一般的測試大多是打 api 或某個 function 並 assert 結果
    - browser testing 是模仿使用者的使用情況，透過真正操作 browser 來測試功能是否正確運作
    - 我們透過 code 來操作 chromium，並 assert 在 chromium 上的 content 是否正確
