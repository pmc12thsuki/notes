Nodejs Basic
===
###### tags: `Node`, `Learning`

## node basic 
browser (web) =>  global (node)
document => process

## require and export
module.export.function_name = () => {}
module.export = {exportVar1, exportVar2}

### core modules
- fs (file system)

fs.readFile(filename), 
fs.writeFile(filename, content), 
fs.appendFile(filename, context)
fs.readFileSync()....
- os (operating system)

## Node Debug Mode
avaliable above node 8 +
### command line
用 node inspect app.js
會開啟 debug mode
用 n(next), c(continue) 來控制node執行的行數
在code中加入 debugger 加入中斷點（breakpoint），
在 debug mode 中使用 c 到中斷點，並用 repl 指令進行變數的操作
也可以搭配 nodemon inspect app.js 開啟debug mode
### dev tools (google chrome)
用 node --inspect-brk app.js
然後開啟chrome 進入 chrome://inspect , view 
會開啟一個可以debug的編輯器
也可搭配 nodemon使用

NPM 
===
npm install packageName --save (save to package.json)
npm install packangeName -g (global)
npm install packageName --save-dev (only use in development)

## useful package
- lodash (module of utility function)


some useage:
1. use pick to pick keys we want from a object
```javascript=
app.post('/users',(req, res)=>{

    const body = _.pick(req.body,['email','password']); //pick up the field that we allows user to post, then if user send _id in request body, we wont and should not save it in db.
    const user = new User(body) // use valid body to initial a object

    user.save().then(user=>{ // save to db
        res.send({user});
    },e =>{
        res.status(400).send(e);
    })
})
```

- nodemon

hot reload module , use in development
to start a file with nodemon
```shell=
nodemon app.js
```


- yargs

easily parse argument from command line

-  request

send http request easily, **DO NOT SUPPORT PROMISE**, need to wrapt request in promise by user himself

```javascript=
request({
            url:`url`,
            json: true // will receive body in json
        },(error, response, body)=>{
            // handle response
        });
```
-  axios

send http request easily, which **SUPPORT PROMISE** by default
```javascript=
axios.get('url') // will receive result in json by default
.then(result=>{
    console.log(result.data)// body of response
})
.catch(err=>{})
```

-  Handlebars

一個常見的 view engine，方便 render 帶有參數的 html page

-  mocha (test library)

-  expect (a assertions library)

-  supertest(a test library which can test express)

 - validator : can test input validation, eg: email
 - crypto-js：有很多加密法的套件，如SHA256
 
 -  jsonwebtoken：可以產生 JWT 的套件
 
 -  bcryptjs: hash user password
 

## Get argument from command line
### process.argv
process.argv is a list of variable
eg: node app.js arg
then process.argv[2] = arg (first two is other object)

### yargs module
better choice: use yargs
eg: node app.js add --key value 
or node app.js add --key "value in string"
or node app.js add --key1=value1 --key2=value2
then yargs.argv = {\_:['add'], title: value }
ort yargs.argv = {\_:['add'], title: value in string }
(parse key-value pair for us)

yargs advanced：input validation
yards.command('command_name', 'description', {argument}).help()
help will show help if someone type --help in command line

## JSON
JSON.stringify() : obj -> string
JSON.parse(): string -> obj

JSON 的key 一定是 string，且 string 要用雙括號
value 可以是 stirng, 數字 , bool

### pretty printing json response in terminal
```javascript=
console.log(JSON.stringify(obj,undefined,indent_number))
//console.log(JSON.stringify(obj,undefined,2))
//will pretty print JSON obj in indent of 2 space
```



Arrow function
===
## function in object (arrow function V.S method )
```javascript=
const user = {
    name: 'my name',
    
    // arrow function
    sayHi: () =>{
        console.log(argument)//can't access argument
        console.log(`hi, this is ${this.name}`)//this do not bind to user in arrow function
    }
    
    //method
    sayHiMethod(){
        console.log(argument)// can access argument
        console.log(`hi, this is ${this.name}`)// this is binding in user when use method
    }
}

user.sayHi()
// hi , this is undifined 
user.sayHi(1,2,3)
// can not access (1,2,3) in sayHi by using argument

user.sayHiMethod()
// hi, this is my name
user.sayHiMethod(1,2,3)
// can access (1,2,3) in sayHiMethod by using argument
```
Encode and Decode uri address in node
===
use node default method
- encodeURIComponent('string')
will encode string to uri
- decodeURIComponet('string')
will decode uri to string

Promise
===
- promise 中的 then 可以接兩個callback，第一個處理result, 第二個處理error
- promise 中的 catch 可以接到所有先前的錯誤（只需要chain一個 catch 就好）
- 所以可以用 then 處理 resolve，用catch處理 reject
- resolve : 成功
- reject：失敗
- pending：尚未成功或失敗
- settled：已經成功或失敗
- promise 的串接：在 promise 中呼叫另一個 promise，可以不用分層寫 then，可以直接串兩個 then + 一個 catch，如：
```javascript=
axios.get(firstURL) // first promise
.then(firstResponse=>{
    // check some situation
    if(response.data.info.statuscode !== 0){
        throw new Error('some error');
        // will throw to catch block
    }
    // handle data
    
    return axios.get(secondURL)// return second promise
}).then(secondResponse=>{
    // handle data
})
.catch(err=>{
    // handle error for two promise
    if(err.code === 'some error'){
        console.log('some error')
    }
    else{
        console.log(err.message)
    }
})
```

# Express

## basic
```javascript=
const express = require('express');
const app = express();

app.get('/',(req, res)=>{
    res.send('Hello'); // the response's content type will be text/html by default
});

app.get('/about', (req, res)=>{
    res.render('about.hbs'); // 回傳html file (or other type)
})

app.get('/error', (req, res)=>{
    res.status(404).send({
        error: 'Page not found'
    })
})

app.listen(3000);
```

## middleware
- 利用 app.use()來註冊 middleware
- 每個進來的 request 都會先經過 middleware 處理
- app.use() 是**依序處理**的！
```javascript=
app.use((req, res, next)=>{
//middleware 結束一定要用 next()，否則不會離開middleware，則 requust 不會被回覆
})
```

### use middleware to create server log
```javascript=
app.use((req, res, next)=>{
    const now = new Date().toString();
    const log = `${now}: ${req.method}, from: ${req.url}`
    
    fs.appendFile('server.log', log+'\n' , err=>{
        if(err){
            console.log(err)
        }
    })
    
    next();
});

```
### use middleware to show web maintanence
```javascript=
app.use((req, res, next)=>{
    res.render('maintenance.hbs');
    // Will rirect all url to maintenance page
})
```

則其他頁面都不會顯示，因為這個 app.use() 裡面沒有呼叫 next()


## get public file (asset like html, template)
- app.use(express.static("absolute path"));
- use __dirname to get absolute path of current project
- eg: 
```javascript=
app.use(express.static(__dirname +'/public'));
```
如在 public 資料夾中放了 about.html
則可以直接在瀏覽器輸入 "url:port/about.html" 來顯示網頁

## hbs
- 生成帶有參數的 html file
- 檔案副檔名為 .hbs
- 在 server.js 中設定 hbs
```javascript=
// partials 指可以重複使用的 html code snippet
// 指定放 partial 的位置
hbs.registerPartials(__dirname +'/views/partials') 
// 告訴 express 要使用的 view engine
app.set('view engine', hbs)
```
- eg: 在server端傳送參數給 about.hbs
```javascript=
app.get('/about', (req, res)=>{
    res.render('about.hbs',{ // hbs file 放在 view 資料夾中
        pageTitle: 'About Page',
        currentYear: new Date().getFullYear()
    });
})
```
在 about.hbs 中，透過 {{key}} 取得參數
```htmlmixed=
<body>
    <h1>{{pageTitle}}</h1>
    <p>text here</p>
    <footer>
        cpoyright {{currentYear}}
    </footer>
</body>
```

- 使用 partial （reuse code: like footer, header...）
在需要插入 code 的地方使用 {{> "partial_name"}}
如上述的 about.hbs將變成

```htmlmixed=
<body>
    <h1>{{pageTitle}}</h1>
    <p>text here</p>
    
    {{> footer}} 
</body>
```
並新增一個 partial file (footer.hbs)
```htmlmixed=
<footer>
    cpoyright {{currentYear}}
</footer>
```
- 註冊一個 html 可以用的 funciton：
在server.js
```javascript=
hbs.registerHelper('getCurrentYear',() => {return new Date().getFullYear()});
```
在 hbs file 中就可以使用這個function
```htmlmixed=
<footer>
    <p> cpoyright {{getCurrentYear}} </p>
</footer>
```
也可以註冊帶有參數的 function
```javascript=
hbs.registerHelper('toUpper',(text) => 
{return text.toUpperCase()});
```

則在 hbs file 中可以傳送 some_text 給這個 toUpper function
```htmlmixed=
<p>{{toUpper some_text}}</p> 
```


Git
===
- git 設定好 local ，首次要 push 到 remote 時，要先做 remote branch 的設定
```shell=
git remote add origin git@git_project_url
git push -u origin master
```

- gitignore
 直接在 folder 中加入一個 .gitignore 的檔案，在其中寫下要 ignore 的檔案名稱


Heroku
===
instead of using heroku web, we use terminal to deploy apps.

in terminal
first login
```shell=
heroku login
```

- deploy app to heroku: need two setup
1. add script in package.json
```json=
{"start": "node server.js"}
```


2. set port by environment variable
```javascript=
const port = process.env.PORT || 3000 ;
```
3. run 'heroku create' in terminal inside the project
```shell=
heroku create
```
this command will create a app in heroku, and add a heroku remote which point to our heroku repository (but it is empty)

4. run 
```shell=
git push heroku
```
push the source to heroku remote brance

5. paste the url or run "heroku open" to open the app in browser
```shell=
heroku open
```
- set our self-defined env variable on heroku
1. heroku config : list all env avriable
2. heroku config:set KEY=VALUE: set a key-value pair env_variable
3. heroku config:get NAME: get a value by key
4. heroku config:unset NAME

# use free mongoDB on heroku
- in app , first run
```shell=
heroku create
```
- then setup mongodb, choose sandbox (free server)
```shell=
heroku addons:create mongolab:sandbox
```
- get MONGODB_URI and set up in server
```shell=
heroku config
```
- deploy (update) our app on heroku
```shell=
git push heroku master
```
- see heroku logs
```shell=
heroku logs
```

- get connection info from MONGODB_URI:
use heroku config to see MONGODB_URI
```javascript=
/*
mongodb://heroku_zt8w52cd:upan0jg2s1opbbls1khjve40ku@ds239682.mlab.com:39682/heroku_zt8w52cd
account: heroku_zt8w52cd
password: upan0jg2s1opbbls1khjve40ku
host: ds239682.mlab.com
port: 39682
db: heroku_zt8w52cd
*/
```

Environment variable
===
## port
access environment variable in app.js :
```javascript=
const port = process.env.PORT || 3000 ;
```
pass variable in shell
```shell=
PORT=8787 node app.js
```

## environment
- development (local)
- test (local)
- production (online)
- set process.env.NODE_ENV to switch environment
- in package.json:
```json=
"scripts": {
    "start": "node server/server.js", // productin or development
    "test": "export NODE_ENV=test || SET \"NODE_ENV=test\" && mocha server/**/*.test.js", //test
    "test-watch": "export NODE_ENV=test || SET \"NODE_ENV=test\" && nodemon --exec npm test" //test
  }
  
// export NODE_ENV=test work on macOS and linux
// SET \"NODE_ENV=test\" work on windows
```

- set config.js and require it in server.js
```javascript=
const env = process.env.NODE_ENV || 'development' ;

if(env === 'development'){
    process.env.PORT = 'development port';
    process.env.MONGODB_URI = 'developmentDBURI';
}else if(env === 'test'){
    process.env.PORT = 'development port';
    process.env.MONGODB_URI = 'testDBURI';
}
```
server.js
```javascript=
require('./config/config');
```

TEST
===
# Mocha

 - npm install mocha --save-dev
only use mocha test in development

 - create fileName.test.js , write test in this file
 - **no need** to require mocha in file, run "mocha filename" to start a test
 - write a test case in fileName.test.js (sync funtion test)
```javascript=
it('shoud add two numbers', ()=>{
    const result = utils.add(33,44);
    if(result !== 77){
        throw new Error(`Expected 77, but got ${result}`);
    }
});
```
 - write test script in package.json
```json=
{"scripts" :{
    "test": "mocha **/utils.test.js"
}}
```

- run the test
```shell=
npm test
```
- run the muto restart test
```shell=
nodemon --exec 'npm test'
# --exec tell nodemon to run a command
```
can add this cammand into package.json script
```json=
{"test-watch": "nodemon --exec \"npm test\""}
```
- use describe("describe_text", callback) to seperate test cases
```javascript=
describe('file_name',()=>{
    describe('#function_name',()=>{
    // put test case here
    })
})
```

## test for asyn function
- declare a async function
```javascript=
module.exports.asyncAdd = (a, b, callback) => {
    setTimeout(()=>{
        callback(a+b)
    }, 1000);
};
```
- test for this function, need to add **done()** to tell moca this function is async
moca will see a function that takes more then 2sec as failed, no matter if it is async or not
```javascript=
it('should async add two number', (done)=>{
    utils.asyncAdd(4, 3, (sum)=>{
        expect(sum).toBe(7).toBeA('number');
        
        done(); // call done when the async function finished
    })
})
```

# expect(a assertions library)
- use expect assertion to simplify mocha test
```javascript=
expect(result).toBe(77); // equal to below code
    
// if(result !== 77){
//     throw new Error(`Expected 77, but got ${result}`);
// }
```
- assertion value and type
```javascript=
expect(result).toBe(77).toBeA('number');
```

- **toBe**, **toNotBe** will not work on array, object....
- use **toEqual** to check if two object are the same   **by reference** ( not checking content)
- use **toNotEqual** to check if two object are not the same  **by reference** (not checking content)
- use **toInclude** to check if object, array include something
- use **toExclude** to check if object, array exclude something


# supertest
- use supertest with moca to **test express** 
in server.js
```javascript=
const express = require('express');
const app = express();
const port = process.env.PORT || 3000;

app.get('/', (req,res)=>{
    res.send('Hello world');
})


app.listen(port, ()=>{
    console.log(`server listen on port ${port}`);
});

module.exports.app = app;
```
in server.test.js
```javascript=
const request = require('supertest');
const app = require('./server').app;

it('should return hello world', (done)=>{
    request(app)
    .get('/')
    .expect("Hello world") //this expect is build in supertest, not expect module
    .end(done)
})
```

### use supertest with expect module
```javascript=
const request = require('supertest');
const app = require('./server').app;
const expect = require('expect');

it('should return hello world', (done)=>{
    request(app)
    .get('/')
    .expect(200)// use expect from supertest
    .expect((res)=>{ // use expect from supertest, and take response as argument
        expect(res.body).toInclude({msg: 'Hello world'}) // use expect from expect module
    })
    .end(done)
```

# spy and rewire (test function)
- spy is a testing tools to test if **function been called correctly** in expect module
- rewire is another module to help us exchange our original function with spy function

**to be continue**

# Exercise: test a POST /todos route
```javascript=
'use strict';
const expect = require('expect');
const request = require('supertest');

const {app} = require('./../server');
const {Todo} = require('./../models/todo');

beforeEach((done)=>{ //setup environment before test , eg: clear our db
    Todo.remove({}).then(()=> done()); // remove all documents in Todo collection
})

describe('Post /todos',()=>{
    it('should create a new todo',(done)=>{
        const text = 'Test todo text';
        // first check http response is correct
        request(app)
        .post('/todos')
        .send({text}) //send data for post request
        .expect(200) //check status code correct
        .expect((res)=>{
            expect(res.body.text).toBe(text); // check response correct
        })
        .end((err, res)=>{  //not end yet, need to check db
            if(err) return done(err);

            //then check db operation is correct
            Todo.find().then((todos)=>{
                expect(todos.length).toBe(1);
                expect(todos[0].text).toBe(text); //check database correct
                done();
            }).catch(e=>done(e));
        })
    })

```
# Seeding database for testing
- 創造一個測試用的 database，每次跑測試前db都會以某個初始狀態開始
- Todo
```javascript=
const todos =[{
    _id: new ObjectID(),
    text: 'first todo'
},{
    _id: new ObjectID(),
    text: 'second todo',
    completed: true,
    completedAt: 333
}];

const populateTodos = done =>{ //a function to setup environment (default db) before test 
    Todo.remove({}).then(()=> { // clear our db, remove all documents in Todo collection
        return Todo.insertMany(todos); // add default todos
    }).then(()=>done()) 
}
```
- User
```javascript=
const userOneId = new ObjectID();
const userTwoId = new ObjectID();

const users = [{
    _id: userOneId,
    email: 'user1@gmail.com',
    password: 'userOnePass',
    tokens: [{
        access: 'auth',
        token: jwt.sign({_id: userOneId, access: 'auth'}, 'abc123').toString()
        }]
},{
    _id: userTwoId,
    email: 'user2@gmail.com',
    password: 'userTwoPass',
}]

const populateUsers = done =>{
    User.remove({}).then(()=>{
        // need call 'save' to fire out middleware
        const userOne = new User(users[0]).save();
        const userTwo = new User(users[1]).save();

        return Promise.all({userOne, userTwo}).then(()=>{ // wait for two user been saved
            done();
        })
    })
}
```
- 在 server.test.js 中，要在 run test 之前清空 db
```javascript=
beforeEach(populateUsers);
beforeEach(populateTodos);
```


Mongo DB
===
- SQL vs noSQL db:
SQL table = noSQL collection (a list of object)
SQL row/record = noSQL document (a object)
SQL column = noSQL field/property (each object have different field)

- start mongo server , inside mongo/bin, run
```shell=
./mongod --dbpath ~/mongo-data
```

- use mongo db with node: install mongodb package
connect to db server
```javascript=
'use strict';

const MongoClient = require('mongodb').MongoClient;

// dont need to create 'TodoApp' db first, mongo will auto add this db if we add data into it
MongoClient.connect('mongodb://localhost:27017/TodoApp',(err, db)=>{ // specify the db url
    if(err) return console.log(err);
    console.log("Connect to MongoDb server");

    // add a collection and insert a document into this collection in TodoApp db
    db.collection('Todos').insertOne({
        text: "Something",
        completed: false
    }, (err, result)=>{
        if(err) return console.log(err);
        console.log(JSON.stringify(result.ops, undefined, 2)) // result.ops is a array of document that we insert
    });

    db.close();
})


```

- ObjectId: mongo db 會自動assign 每個 document 一個 _id，這個 id 不是 incerment 的（跟 SQL 不一樣） ，而是一串隨機的數字。如：5b9e3ff040252928fc6a8eeb。這串數字的前四碼是 timestamp，剩餘的是 machine code, pid, 隨機數等，可以防止多個終端同時改寫 db 時打架。由於 id 中有紀錄 timestamp ，所以不需要另外再開一個 field 來記錄時間。另外 _id 是可以自己 assign，只要在 createOne 的時候賦予 _id值就好。
- 透過 _id 來取得 timestamp：_id.getTimeStamp()
```javascript=
result.ops[0]._id.getTimeStamp();
```

- 直接操作 ObjectID 來創造 objectId：
```javascript=
const {MongoClient, ObjectID}  = require('mongodb');
const obj = new ObjectID(); // get a unique objectId
console.log(obj);
console.log(obj.getTimestamp());
```

- find:
```javascript=
db.collection('Todos').find().toArray().then()//findAll
db.collection('Todos').find({completed: false}).toArray().then() //fetch with condition
db.collection('Todos').find({
        _id: new ObjectID("5b9e456fda4643cacb935618") //_id is not a string but ObjectID object
    }).toArray() //fetch with specify ObjectID
    .then()
db.collection('Todos').find().count().then() //count
```
- delete:
```javascript=
db.collection('Todos').deleteMany({text:'feed coddee'})
    .then()//deleteMany
db.collection('Todos').deleteOne({text:'feed coddee'})
    .then()//deleteOne
    
//most useful
db.collection('Todos').findOneAndDelete({completed: false}).then() //findOneAndDelete 
```

- update:
```javascript=
db.collection('Users').findOneAndUpdate({
        name: 'suki'
    },{
        $set:{ // update operators of set
            name: 'coffee'
        },
        $inc: { // update operators of increase
            age: 3
        }
    },{
        returnOriginal: false // return the update one, not the original one
    }).then(result=>{
        console.log(result);
    })

```

Mongoose (Mongo db ORM)
===

## basic
```javascript=
const mongoose = require('mongoose');

mongoose.Promise = global.Promise; // default use callback
mongoose.connect('mongodb://localhost:27017/TodoApp'); // not need to write operation in callback, mongoose will wait for connection complete

//create model
const Todo = mongoose.model('Todo',{ // collection name
    // add field
    text:{
        type: String,
        required: true,
        minlength: 1,
        trim : true // will remove leading and trailing whitespace
    },
    completed:{
        type: Boolean,
        default: false
    },
    completedAt:{
        type: Number,
        default: null
    }
});

const newTodo = new Todo({ // create a object
    text: "buy earphone",
    // bool and number will be cast to string type by mongoose 
})

newTodo.save().then(result=>{ // save into db
    console.log(result)
}, error=>{
    console.log(error)
});
```
## query
```javascript=
'use strict';
const {ObjectID} = require('mongodb');

const {mongoose} = require('./../server/db/mongoose');
const {Todo} = require('./../server/models/todo');

const id = '5b9e767ea960f5b237a6222f';

if(!ObjectID.isValid(id)){
    console.log('Id not valid');
}

//find(all)
Todo.find({
    _id: id //mongoose will transerform id(string) to id(objectID) automatically
}).then(todos=>{
    console.log(todos);
})

//findOne
Todo.findOne({
    _id: id //mongoose will transerform id(string) to id(objectID) automatically
}).then(todos=>{
    console.log(todos);
})

//most use
//findById
Todo.findById(id).then(todo=>{
    if(!todo){ //todo is empty because this id not exist
        return console.log('Id not found');
    }
    console.log(todo);
},e=>{
    console.log(e);
})
```

## exercise
```javascript=
app.get('/todos/:id', (req, res)=>{
    const id = req.params.id;
    if(!ObjectID.isValid(id)){ // valid id
        return res.status(404).send();
    }
    Todo.findById(id).then(todo=>{
        if(!todo){
            return res.status(404).send();
        }
        res.send({todo});
    },error=>{
        res.status(400).send();
    })
})
```


Build an api server
===

# update
- RESTful api 中的 update 對應到 http method 中的 patch
- 在寫update的 api 時（如 app.patch('/todos/:id')），可以用lodash 的 pick 從 req.body 中先抓出我們允許update的field，以免使用者送來不能被update的field。如：
```javascript=
//update
app.patch('/todos/:id', (req, res)=>{
    const id = req.params.id;
    const body = _.pick(req.body,['text','completed']); //pick up the field that we allows user to update, then if user send _id in request body, we wont and should not upadte it.
    
    if(!ObjectID.isValid(id)){
        return res.status(404).send();
    }
    if(_.isBoolean(body.completed) && body.completed){ //user completed this task
        body.completedAt = new Date().getTime();
    }else{
        body.completed = false,
        body.completedAt = null;
    }

    Todo.findByIdAndUpdate(id, {
        $set: body // use set operator to update field
    },{
        new: true // return new instead of return original document, same as returnOriginal
    })
    .then(todo=>{
        if(!todo){
            return res.status(404).send();
        }
        res.send({todo});
    }).catch(e=>{
        res.status(400).send();
    })
})
```


Hashing
===
- will get same result if input is the same
- **can not** get original string if you only have hashing string
- it's called **one-way algorithm**
- one may stored hashed password in db, if user want to log-in, we will hash that password and compared to db.

## Hash VS Encrypt
- Hash 是單向不可逆的，只能透過 hash 後的值是否相同來判斷原文是否相同
- Encrypt 是可逆的，使需要有鑰匙就能回推

## Hashing token
### crypto-js （用起來比較麻煩，用JWT取代）
- SHA256
```javascript=
const {SHA256} = require('crypto-js');

const msg = 'i am user';
const hash = SHA256(msg).toString();
console.log(hash.toString());
//1aaefa2110fd38321ab4af9a9a6ba3cc69c5b8fb09bbbe665f6a17f362486a94
```

### JWT (Json Web Token)
- [Json web token 是什麼](https://yami.io/jwt/)
- [官網](https://jwt.io/)
- JWT 的原理：要檢查使用者是否有授權時，會發給使用者一個 hash 過的 token。例如使用者的 id 是 1，則發給他 token 如下。其中的 somesecret 稱為 **salt** ，是只有server端知道的密碼，避免使用者偽造 token。（因為使用者可以偽造自己是 id 2）。以下模擬 jwt 的生成方式（不是完整的 jwt）
```javascript=
const data = {
    id: 1
}

const token = {
    data,
    hash: SHA256(JSON.stringify(data)+ 'somesecret').toString()
}
```

- 可以產生 JWT 的套件： jsonwebtoken
```javascript=
const data2 = {
    id: 10
}

const jwt_token = jwt.sign(data2,'123abc'); // jwt.sign()
const decoded = jwt.verify(jwt_token,'123abc'); //jwt.verify()
console.log(decoded)
//{ id: 10, iat: 1537189178 }

const error_ecoded = jwt.verify(jwt_token,'456');
// JsonWebTokenError
```

### create generate JWT method in user model (mongodb)
```javascript=
// if we want to add instance mothed on Model, we need to create model by Schema
const UserSchema = new mongoose.Schema(
    {
    email:{
        type: String,
        required: true,
        minlength: 1,
        trim: true,
        unique: true,
        validate:{
            validator: validator.isEmail,
            // return true or false, equal to 
            // value =>  validator.isEmail(value);
            message: '{VALUE} is not a valid email'
        }
    },
    password: {
        type: String,
        required: true,
        minlength: 6
    },
    tokens:[{  // Array type is valid in mongoDB, may not valid in others 
        access:{
            type: String,
            required: true
        },
        token:{
            type: String,
            required: true
        }
    }]
})
//add instances method on UserSchema. instances method is different with model methods, instances need some instances data inside
UserSchema.methods.generateAuthToken = function(){
    //method function can bind this, but arrow function cannot
    const user = this;
    const access = 'auth';
    const token = jwt.sign({
        _id: user._id.toHexString(),
        access
     }, 'abc123').toString();

     // push token into user.tokens, but push may cause error, so use concat instead
     user.tokens = user.tokens.concat([{access, token}]);

     // return a token (a promise that return a token) when server called this function
     return user.save().then(()=>{
         return token;
     });
}


const User = mongoose.model('User',UserSchema);

module.exports = {
    User
}
```

- set header
```javascript=
res.header('x-auth', token).send({user}); // user with token
// set key-value pair in header,
// header key start with "x-" means it is a custom header
```

- override method of User model
```javascript=
//override toJSON function, this function will called when convert mongoose model to JSON value
UserSchema.methods.toJSON = function(){ 
    // we dont want to send 'password' and 'tokens' to user
    const user = this;
    const userObject = user.toObject(); // a function that convert user into a regular object where only the properties avaliable on the document exist
    return _.pick(userObject,['_id','email']); // return only id and email
};
```
## Hashing password
### bcrypt
- how to use
```javascript=
// use bcrypt to hash password
let hashedPassword;
const password = '123abc';
bcrypt.genSalt(10,(err, salt)=> { //10 rounds. genSalt(rounds, callback_function
    bcrypt.hash(password, salt, (err, hash)=>{ //hash(password, salt, callback_function)
        console.log(hash);
        hashedPassword = hash;
    })
}) 

bcrypt.compare(password, hashedPassword, (err, res)=>{ //compare(password, hashedPassword, callback_function)
    console.log(res);
    // res is true if password is correct
})
```
- use bcrypt to hash user's password, then save to DB
> **do not** save raw password in db
- when user save or modify his password, we first hashed then save to db
```javascript=
// add middleware for user schema (hash password)
UserSchema.pre('save', function(next){ // a middleware called before save
    // before a document saved, we want to hash user password
    const user = this;
    if(user.isModified('password')){ // only hash password when user password is modify, otherwise the password will be hased again
        bcrypt.genSalt(10, (err, salt)=>{
            bcrypt.hash(user.password, salt, (err, hash)=>{
                user.password = hash;
                next();
            })
        })
    }else{
        next();
    }
})
```
- when user login, we check if his password is correct
```javascript=
UserSchema.statics.findByCredentials = function(email, password){
    const User = this;
    return User.findOne({email}).then(user=>{
        if(!user) return Promise.reject();

        return new Promise((resolve, reject)=>{
            bcrypt.compare(password, user.password, (err, result)=>{ // check if user's password is correct
                // res is true if password is correct
                if(result){
                    resolve(user); 
                }else{
                    reject();
                }
            })
        })
    })
};
```

Create a private route (with auth)
===
- create a route
```javascript=
// private route which check for auth(x-auth in header) first
app.get('/users/me',(req, res)=>{
    const token = req.header('x-auth');
    
    //custom model method
    User.findByToken(token).then(user=>{
        if(!user){
            return Promise.reject(); // instead of writing res.status(401).send(), we can return a reject so the catch block will be execute
        }
        res.send(user);
    })
    .catch(err=>{
        // 401 status : authentication is required
        res.status(401).send();
    })
})
```

- in User Model, we add Model method
```javascript=
// add a Model method
UserSchema.statics.findByToken = function(token){
    const User = this; // binding to model
    var decoded;

    try{
        decoded = jwt.verify(token, 'abc123')
    }catch(e){ 
        return Promise.reject(); // if not a valid token, then reject it
        // Promise.reject() is equal to below: 
        // new Promise((resolve, reject)=>{ 
        //     reject();
        // });
    }

    return User.findOne({ // return a promise
        _id: decoded._id,
        'tokens.token': token, //find in tokens array with token object that equal to the token argument passed in above
        'tokens.access': 'auth'
    });
}
```

- break upper router into reusable middlerware
>**middleware**
```javascript=
// a authenticate middleware
const authenticate = (req, res, next) =>{
    const token = req.header('x-auth');
    
    User.findByToken(token).then(user=>{ //custom model method
        if(!user){
            return Promise.reject(); // instead of writing res.status(401).send(), we can return a reject so the catch block will be execute
        }
        // attach user we found and token in req
        req.user = user;
        req.token = token;
        next();
    })
    .catch(err=>{
        // 401 status : authentication is required
        res.status(401).send();
    })
}
```
>**route**
```javascript=
// private route which check for auth(x-auth in header) in middleware
app.get('/users/me',authenticate, (req, res)=>{
    res.send(req.user);
})
```

Socket.io
===
## Add socket io in server and client side to handle events
- see socket.io for usage with express 
- can trigger events from server to client, and from client to server. express cant trigger event from server itself
- emit: create and send a event
- socket.on / io.on: listen for some event
> socket.emit emits an event to single connection (single client)
socket.broadcast.emit will send the event to everybody but this socket itself
io.emit emits an event to every single connection (every client)
### Emit and listen Events : email event (example)
- send 'newEmail' event from server side, and listen on client side

on server side
```javascript=
socket.emit('newEmail', { // create a custom event from server, then send to client. Not a handler, so do not pass a callback function in. can pass some data.
        from: 'test@gmail.com',
        text: 'hello world'
    }); 
```

on client side
```javascript=
socket.on('newEmail', (email)=>{ // listening on server side custom event and data
    console.log('new email', email);
})
```

- send 'createEmail' event from client side, and listen on server side

on client side
```javascript=
socket.emit('createEmail',{ // create a custom event from client, then send to server. Not a handler, so do not pass a callback function in. can pass some data.
        to: 'to@gmail.com',
        text: 'Hey, i want to send email'
    // })
```

on server side
```javascript=
socket.on('createEmail', newEmail=>{ // listening on client side event 'createEmail'
        console.log('create new email', newEmail);
    })
```
### server send event(message) to every single connection (client)
- use io.emit
```javascript=
socket.on('createMessage', msg=>{ // once we receive an createMessage events from server
        console.log('new message', msg);
        // socket.emit emits an event to single connection (single client)
        // io.emit emits an event to every single connection (every client)
        io.emit('newMessage',{
            from: msg.from,
            text: msg.text,
            createdAt: new Date().getTime()
        })
    })
```

### Broadcasting event (server send message to everybody but one specific user)
- if we dont want the client who send the message receive the message again
```javascript=
socket.on('createMessage', msg=>{ // once we receive an createMessage events from server
        console.log('new message', msg);
        // broadcast will send the event to everybody but this socket itself
        socket.broadcast.emit('newMessage',{
                from: msg.from,
                text: msg.text,
                createdAt: new Date().getTime()
            })
    })
```

- for welcoming message
```javascript=
io.on('connection', (socket)=>{
    console.log(`new user connected`);

    // welcome the user
    socket.emit('newMessage',{ // will send to the specific user
        from: 'Admin',
        text: 'Welcome to the chat app',
        createdAt: new Date().getTime()
    })

    // tell other people somebody joined the app
    socket.broadcast.emit('newMessage',{ // will send to everybody but this socket itself
        form: 'Admin',
        text: 'New user joined',
        createdAt: new Date().getTime()
    })
})
```

## Room
- join a specific room in by socketIO
```javascript=
socket.join(params.room);
```
> io.emit -> io.to(room_name).emit 
socket.broadcast.emit -> socket.broadcast.to(room_name).emit
socket.emit