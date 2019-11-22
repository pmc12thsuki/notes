MongoDB
===
###### tags: `Database`

# Introduction
- 層級
    - Database
    - Collection (table)
    - Document (data, row)
        - Schemaless
        - JSON (BSON) data format
            - 在儲存時 JSON 會轉成 BSON
            - BSON: Binary of json
            - 可以存 nested document
    - Field (column)

- 優點：
    - 不需要 schema
    - 資料不像 SQL 那樣，需要透過很多 relation (JOIN) 才可以找到所需要的資料。關聯的資料在 mongo 中常常會被儲存在同一個 document 中
- DB, Collection 都會被隱式的創造
    - 在 insert 第一筆 Document 時會被一起創造
    - `db.<collection>.insertOne({property})`
![](https://i.imgur.com/cHpUngL.png)

> JSON 中的 key-value pair 中，key 可以不加上引號、而 value 如果是 string，則單引號、雙引號都可以
## MongoDB Ecosystem
![](https://i.imgur.com/EXvartR.jpg)

## Working with MongoDB
![](https://i.imgur.com/IEUcycA.jpg)
- 不同語言（App）透過 drivers 跟 mongodb server 溝通。
    - 我們也可以透過 mongo shell (如 cli, compass) 跟 mongodb server 溝通
- MongoDB server 要讀寫資料時，會透過 storage Engine 操作資料。
    - default 的 storage engine 叫做 Wired Tiger
    - storage engine 會實際操作 file 及 memory


![](https://i.imgur.com/vsI7HBX.png)

## Basic command
- `show dbs`
- `use <db_name>`
- `db.<collection>.insertOne({property})`
- `db.<collection>.find()` // will return all document in collection
- `db.<collection>.find().pretty()` // return a prettier result
- `cls` // clear the window

## JSON and BSON
- BSON = binary JSON
    - MondoDB 儲存的是 BSON
    - 一個是空間較有效率
    - 另一個是 BSON 可以有更多的 type。
        - 如 ObjectId type 在 JSON 中是不合法的，但在 BSON 中就可以
        - 或是其他 Number Types


![](https://i.imgur.com/vIpT1Fb.jpg)

# Basic Operations
![](https://i.imgur.com/sek6vMr.jpg)

## CRUD
- 不同工作性質的專案都會透過 CRUD 跟 MongoDB 互動
    - 包含 app
    - 資料分析
    - admin 管理等等


![](https://i.imgur.com/DhemkqY.jpg)


### Create
- create document 時 Mongo DB 會主動 assign 一個獨特的 _id 欄位
    - value type 是 `ObjectId()`
    - JSON 跟 JS 中是沒有 ObjectId 這個 type 的，可見 Mongo 雖然輸入的格式是 JSON 的樣式，但真正儲存的不是 JSON
- 我們可以使用自己的 _id，且不一定要是 `ObjectId` type
    - 只要在 insert 時帶上 `_id` 欄位就行
    - 我們要自己保證 _id 不會重複，如果重複就會 insert 失敗

- `insertOne({要被 insert 的資料})`
- `insertMany(array of 要 insert 的資料)`
    - **裡面放 array**
    - mongo 在 insert 時**會依照 array 的順序去 insert**

### Read
- `find(query)`
    - `db.collentions.find()` 不帶參數的話會回傳所有 data
    - 可以對於某個 field 下更明確的查找指令，寫在該 field 的 object 中
    - 如 `$gt: greater than`：`db.flightData.find({distance: {$gt: 1000}})`
- findOne(query)：找出**第一個**符合條件的 document
- find 指令可以加上 pretty() 讓結果顯示較易閱讀。 `db.collection.find().pretty()`

#### Projection
- projection 可以讓我們只拿到需要的欄位
    - **因為在 Mongodb server 端就會被過濾掉，可以大大提升效率**
- 就像 SQL 中的 SELECT
- 可以在 find 中加上第二個參數
    - 該參數是一個 object
    - key 是欄位名稱
    - value 是要不要保留該欄位
        - 1 代表要 include
        - 0 代表要 exclude
    - _id 欄位預設會被 include，除非特別有指定 `{_id: 0}`
    - 除了 _id 以外的欄位預設都會被 exclude
- 如 `db.collection.find({}, {name:1, _id:0})`
    - 拿回所有 document，只保留 name 欄位
#### Cursor Object
- find 這個操作，其實並不會回傳所有符合的 data
    - 為了效率，find() 其實是回傳一個 **cursor object**
    - **而不是 list of document**
- 透過這個 cursor object，我們可以輕易 cycle through the result
    - 在 shell 中，預設會先回傳 20 筆資料與 cursor
        - 我們可以用 `it` 這個指令，來獲得下一批 result data （下 20 筆資料）
- 如果我們使用 `db.collection.find().toArray()`，則 cursor 會 loop 所有資料並裝進一個 array 中，再回傳**所有的data**
    - 當 data 很多時效率會很差
- 我們也可以在 shell 中使用 `db.collection.find().forEach(data => doSomething)` 來操作 cursor 並對每一筆 data 做操作
    - 如 `db.datas.find().forEach( data => printjson(data))`
    - 效率比直接 `toArray` 還好，因為不會一次把所有 data load 進 memory 中，而是一筆一筆操作 （一批一批）

- 其他操作（Create, Update, Delete）就沒有 cursor


![](https://i.imgur.com/G7qvYBG.png)

#### pretty 
- pretty 是一個操作在 cursor 上的 function
- 所以可以用 `db.collection.find().pretty()` 來 print 資料
    - 因為 find 會回傳 cursor
- 但不可以用 `db.collection.findOne().pretty()`
    - 因為 findOne 會直接回傳一筆 document，而不是一個 cursor


### Update
- `updateOne(query, document)`
- 在第二個參數中描述要做的 update
    - 第二個參數是一個 object
        - key 為指令 (operators)
        - value 為另一個要賦予的 key-value pair
    - operators 如 $set：修改已有的欄位值、如果欄位不存在就加上這個欄位
```bash=
db.collection.updateOne(
    {name: 'myName'}, // 要update的條件
    {$set: {age: 18}}) // 要進行的 update 指令
```

- `updateMany(query, document)`
    - 如果沒有指定條件，就會全部 update 
    - 如 `db.collection.updateMany({}, {$set: {marker: 'toDelete'}})` 則所有 document 都會加上 marker 這個欄位
    - **要慎用！**
    - updateOne 跟 updateMany，**第二個參數都需要搭配 $set 之類的 operator 來使用**
        - mongoose 中才有省略 $set 的功能
    
- `update(query, document)`
    - **應該要直接避免使用**
    - 第二個參數如果帶上 operator，效果就跟 updateMany ㄧ樣
        - 如 `db.collection.update({_id:someId}, {$set: {isUpdated: true}})`
        - 會將所有符合條件的 document 都 set `isUpdate` 的欄位與值
    - **第二個參數如果沒有帶上operator，則會直接 overwrite document**
        - 以第二個參數來 overwrite 所有符合條件的 object
        - 如 `db.collection.update({_id:someId}, {isUpdate: true})`
        - **會將所有符合條件的 document 都 overwrite 成為只剩 _id 跟 isUpdate 的 document，原有的其他欄位都會消失**
        - _id 欄位一定會被保留
        - **如果想要達成 replace，則應該直接使用 `replaceOne 之類的 function`**
> updateOne 跟 updateMany 都必須要帶上 operator（如 $set）來準確描述對資料的操作，否則會 error
> 
> update 則可以帶上 operator、也可以不要。
> 如果帶上 operator 那效果就跟 updateMany ㄧ樣。
> 如果沒有帶上 operator，則會直接 **overwrite 原先有的 document**，使用時應該要避免使用 update 以免誤用
### Delete
- `deleteOne(query)`
- `deleteMany(query)`
    - 如果沒有指定條件 (為空 object)，就會全部 delete 掉
    - `db.collection.deleteMany({})` **就會清空整個 collection**
    - **要慎用！**

## Embedded Document
- Document 中可以裝有 document
    - 也稱為 nested document
    - 最多 100 層
    - 且每個 document 最多 16 MB
    - **在 query 時，如果要指令 nested document 中的欄位，就需要用 "" 把 key 包起來**
```javascript=
// 如下指令，因為 status.description 是 key，所以要用雙引號包住
db.flightData.find({"status.description": "on-time"})
db.filghtData.find({"status.details.someDetails": "detail"})

// 則下列就符合
{
    _id: someId,
    distance: 2000,
    airport: "TPE",
    status: {
        description: "on-time",
        lastUpdated: "20 min ago",
        details: {
            someDetails: "detail"
        }
    }
}

```
- Document 中可以有 List
    - List of Data or List of Document 都行
    - **在 query 時，如果使用 find，則只要 array 中有該資料就符合條件**
```javascript=
// 如下指令，因為 hobbies 是一個 array，所以只要包含 sport 的都會符合
db.passengers.find({hobbies: 'sport'})

// 則下列符合條件，所以會回傳
{
    "_id": someId,
    "name": "myName",
    "age": 18,
    "hobbies": [
        'sports',
        'cooking',
        // ... more
    ]
}
```

## Resetting DB
- drop db
    - `use databaseName` -> `db.dropDatabase()`
- drop collection
    - `db.myCollection.drop()`

## Basic Wrap Up
![](https://i.imgur.com/3SEYWjh.jpg)

# Schemas and Relations
## Schema
- 雖然 Mongo DB 完全是 schemaless 的
    - 每個 documet 中的 field 可以完全不同
- 但在現實使用中，我們在使用上仍然有 schema 的需求
    - 因此我們還是會定義 mongodb 的 collection schema

![](https://i.imgur.com/FIAiLVu.jpg)

## Schema Validation
- 我們可以設定 validation，在 insert 時會檢查
![](https://i.imgur.com/KtakIRN.png)

- validation action
    - 只發出警告 or throw error
- validation level
    - 是否允許在設定 schema 後，再去 update 那些曾經不符合 schema 的 document... 等等
    - moderate validationLevel ：會檢查所有 insert，但允許「在加入 validation 前就已經存在的不合法資料」存在，並在他們 update 成 correct document 之後才去 validate 他們。


![](https://i.imgur.com/yZ4XOG8.jpg)
### Create Validation
- 最簡單的是最一開始創造 collection 時就加上 validation
    - 此時要用**顯式**的創造 collection
    - 預設的 validationAction 是當有不符合 validation 時，會直接顯示 error 且拒絕該 create
    - `db.createCollection(collectionName, objectOfOption)`
```javascript=
db.createCollection('posts', { // 顯式的創造一個 collection
  validator: {
    $jsonSchema: { // 建議使用 jsonSchema
      bsonType: 'object',
      required: ['title', 'text', 'creator', 'comments'], // document 中 required 的 field
      properties: { // 針對每個 field 設定其 property
        title: {
          bsonType: 'string',
          description: 'must be a string and is required',
        },
        text: {
          bsonType: 'string',
          description: 'must be a string and is required',
        },
        creator: {
          bsonType: 'objectId',
          description: 'must be an objectid and is required',
        },
        comments: { // comment 是一個 array
          bsonType: 'array',
          description: 'must be an array and is required',
          items: { // comment 裡的 item 設定
            bsonType: 'object', // comment 是一個 array of object (document)
            required: ['text', 'author'], // document 中的 required 欄位
            properties: { // 針對每個 field 設定其 property
              text: {
                bsonType: 'string',
                description: 'must be a string and is required',
              },
              author: {
                bsonType: 'objectId',
                description: 'must be an objectid and is required',
              },
            },
          },
        },
      },
    },
  },
});

```

- 如果想要事後修改設定的 validation
    - 如將 validationAction 從 error 改為 warn
    - 此時如果想加入一筆不符合 validation 的資料，系統會寫一筆 warn log 進 log output 中，且**允許該資料插入**
    - 透過 `db.runCommand()` 來重設 validator 跟 validationAction / validationLevel
```javascript=
db.runCommand({ // 跑 admin 指令
  collMod: 'posts', // collMod = collection modifier，value 是要修改的 collection
  validator: { // 重寫 validator 的內容
    $jsonSchema: {
      bsonType: 'object',
      required: ['title', 'text', 'creator', 'comments'],
      properties: {
        title: {
          bsonType: 'string',
          description: 'must be a string and is required',
        },
        text: {
          bsonType: 'string',
          description: 'must be a string and is required',
        },
        creator: {
          bsonType: 'objectId',
          description: 'must be an objectid and is required',
        },
        comments: {
          bsonType: 'array',
          description: 'must be an array and is required',
          items: {
            bsonType: 'object',
            required: ['text', 'author'],
            properties: {
              text: {
                bsonType: 'string',
                description: 'must be a string and is required',
              },
              author: {
                bsonType: 'objectId',
                description: 'must be an objectid and is required',
              },
            },
          },
        },
      },
    },
  },
  validationAction: 'warn', // 加上 validationAction, 設定為 warn
  // validationLevel: strict || moderate
});

```
## Data types
![](https://i.imgur.com/b8ca1rp.jpg)
- 除了上述之外還有很多其他 advanced 的 datatype
- 可以使用 `new Date()` 跟 `new Timestamp()` 來創造新的 ISODate 或 Timestamp data
- Timestamp 的格式是 Timestamp(stamp, order)
    - stamp 就是 timestamp
    - order 可以確保如果有多個 document 在同一個 stamp 存入，則可以透過 order 來區分之間的順序 
    - 所以 **timestamp 會是 unique 的**！就算同一個時間 insert 的 document 也會有不同的 timestamp

- int 有分 NumberInt (int32) 跟 NumberLong (int64)
- double 也有分 NumberDouble(64) 跟 NumberDecimal(128) 兩種
    - numberDecimal 是 high-precision double value
- 如果沒有指定的話，如 `insertOne({a: 1})` 則會使用 **NumberDouble**!
    - 因為 shell 是 js，且 js 中沒有 int/float 的區別，都是 float
    - The mongo shell treats all numbers as 64-bit floating-point double values by default.
    - 可以透過指定number 的 type 來節省空間
        - 如 `db.numbers.insertOne({a: NumberInt(1)})` 會存成一個 int32，但如果沒有指定會存成 float 64
- text 沒有大小限制，只要不會使該 document 總共超過 16 MB 就好

### Data type and limits note

MongoDB has a couple of hard limits - most importantly, a single document in a collection (including all embedded documents it might have) must be <= **16mb**. Additionally, you may only have **100 levels of embedded documents**.

You can find all limits (in great detail) here: <https://docs.mongodb.com/manual/reference/limits/>

For the data types, MongoDB supports, you find a **detailed overview** on this page: <https://docs.mongodb.com/manual/reference/bson-types/>

**Important data type limits are:**

- Normal integers (int32) can hold a maximum value of +-2,147,483,647
- Long integers (int64) can hold a maximum value of +-9,223,372,036,854,775,807
- Text can be as long as you want - the limit is the 16mb restriction for the overall document

It's also important to understand the difference between int32 (NumberInt), int64 (NumberLong) and a normal number as you can enter it in the shell. The same goes for a normal double and NumberDecimal.

**NumberInt** creates a **int32** value => `NumberInt(55)`

**NumberLong** creates a **int64** value => `NumberLong(7489729384792)`

If you just use a number (e.g. `insertOne({a: 1}`), this will get added as a **normal double** into the database. The reason for this is that the shell is based on JS which only knows float/ double values and doesn't differ between integers and floats.

**NumberDecimal** creates a high-precision double value => `NumberDecimal("12.99")` => This can be helpful for cases where you need (many) exact decimal places for calculations.

When not working with the shell but a MongoDB driver for your app programming language (e.g. PHP, .NET, Node.js, ...), you can use the driver to create these specific numbers.

Example for Node.js: <http://mongodb.github.io/node-mongodb-native/3.1/api/Long.html>

This will allow you to build a NumberLong value like this:

```javascript=
const Long = require('mongodb').Long; 
db.collection('wealth').insert( {    
    value: Long.fromString("121949898291"
)});
```

## Data Schemas & Data Modeling
- 設計 schema 前可以考慮幾點
![](https://i.imgur.com/5qccSX9.jpg)

### Relations
- 對於各種 relations，我們都可以選擇要用 Embedd document 或是 Referenct 的方式來實現
    - 端看該狀況下哪個比較好
    - 資料有沒有大量重複？
    - 讀/寫的方便性

![](https://i.imgur.com/kXbvR9I.jpg)

### One-To-One
#### Embedded
- One to one 通常會選擇使用 Embeded 裝
- 如果該應用中，這兩種資料常常需要一起出現
    - 例如病人 -- 病歷
        - 要 query 時，就可以一次 query 到想要的資料

#### Reference

- 根據應用程式的需求，如果這兩種資料比較多「分開」的應用的話，就可以選擇用 reference 的方式

### One-To-Many
#### Embedded
- One to Many 通常會選擇使用 Embeded 
    - 要注意不要超過 16 MB 的上限

- 如果該應用中，這兩種資料常常需要一起出現
    - 例如 question(one) -- answer(many)
        - 要 query 時，就可以一次 query 到想要的資料

#### Reference
- 依樣是要根據應用程式的需求
- 如果可能超過 16 MB 的限制，就需要把兩個 collection 用 reference 的方式關聯
    - 如 city(one) - citizens(many)
    - 如果拿 city 資料時，通常不需要所有 citizens 的資料，且這樣存可能會達到 16MB 限制。因此會考慮分成兩個 collection 存

### Many-To-Many
#### Embedded
- 某些 app 中，many to many 可以用 embedded 紀錄
    - 如比較在意 snapshot 的 app，因此其多對多並不是真正的「多對多」（關聯到某個特定的物品）
    - duplicate 可以被接受，或是需要 duplicate 時
- 例如 product(many) -- customer(many)
    - 可以在 customer 中紀錄一個 order 欄位，並直接紀錄 array of product  來直接紀錄他購買了哪些東西 (snapshot)
    - 因為我們在意的是購買當下那個產品的名稱、購買數量、價錢
    - 如果事後這個產品漲價了，該 customer 已經購買的物品價格 **也不會跟著改變**
#### Reference
- Many to Many 大多會選擇使用 Reference
- 如果在 SQL 中， many to many 會需要三張表格來紀錄
- 在 Mongo 中，many to many 透過 reference 可以用兩張表格紀錄就好
    - 因為其中一個關聯者可以用 array of id 的方式紀錄關係
- 當資訊較常改變，且希望關聯到的資訊都是最新的、**相同的那一份**時，會使用 reference
- 例如 book(many) -- author(many)
    - 我們希望當作者的資訊改變時，所有書上的作者資訊也都跟著改變 (反向也是)

### Relations Wrap up
![](https://i.imgur.com/KzADAzx.jpg)


### Using Lookup
- 我們可以使用 aggregate 來將有一次查詢到關聯的 data
- 如 books -- authors，假設 book 中有個 field 叫 authors 為一個 array 紀錄關聯的 author id
    - 則可以使用 `db.books.aggregate([{$lookup: {from: "authors", "localField": "authors", foreignField: "_id", as: "creators"}}])` 在 book document 中一起呈現 author 的資訊
    - from 是指**被關聯**的 collection 名稱
        - 這邊是 authors collection
    - localField 是**關聯到別人的 field** 名稱
        - 這邊是 book 中的 authors 欄位（裡面記錄 array of author id）
    - foreignField 是被關聯到的 field 名稱
        - 這邊是 author collection 的 _id 欄位
    - as 是該資料之後要被命名的方式 
        - 在 books 中會加上 creators 這個欄位，顯示從 author 關聯來的資料 (原先的 authors field 仍會保持)
    - `從 books collection  中的 authors 欄位（localField）關聯到 authors collection(from) 中的 _id 欄位 (foreignField)，並且命名為 creators(as)`

## Schema Wrap Up
- 資料 model 與 structure 時，應該考慮
    - 應用的 App、或分析人員會怎麼獲取資料
        - 大多的應用場景是會分別的取用關聯式的資料、還是都會一起取出資料來使用呢
    - 是 read 導向還是 write 導向的 app 呢
        - 大多是 read 導向 -> 是否考慮把資料存在一起，獲取速度較快
        - 如果是 write 導向 -> 是否是需要避免 duplicate 的類型（大多是）
    - 資料量多大
        - 避免超過 document 的大小上限（16 MB）
    - 資料是怎麼關聯的
        - 1-1, 1-N, N-M
    - App 需不需要 duplicate 的資料
        - 是否關聯到的是「同一個個體」、還是儲存的是 snapshot
    - 整體資料量有多大
![](https://i.imgur.com/6ZBhq89.jpg)

- Wrap up
![](https://i.imgur.com/Tw10Kq3.jpg)


# Shell and Server option
## Server option
- `--quite`: 讓 db server output 出來的資訊少一點
- `--port`：設定 port
- `--dbpath`: mongo server 使用的 db 資料位置
- `--logpath`: mongo server 使用的 log output 位置
    - 如果有設定的話，consolge 中原本 output 的資訊都會改成寫入 log file 中
    - 要指定被寫入的 log file，如 `log.log` 之類
- `--fork`: run mongodb **as a backgroud service**
    - 就不用再開兩個 terminal 來操作
    - 因為原本的 terminal 無法再顯示 log 資訊，因此使用 fork 時必須要帶上 `--logpath` 的參數
    - 要關閉 server 的話，就透過 cli 輸入 `use admin` 先切換到 admin DB，再用 `db.shutdownServer()` 來關閉在背景執行的 server

- 使用 config file
    - config file 可以寫在任何地方
    - 在啟動 server 時使用 `-f` 或 `--config` 來設定 config file 的位置
```
storage:
  dbPath: "/your/path/to/the/db/folder"
systemLog:
  destination:  file
  path: "/your/path/to/the/logs.log"
```

## Shell (cli) option
- `--nodb` 啟動 shell without db，可以執行 js code
- `--host`：預設在 localhost
- `--port`
- `--quiet`
- 進入 cli 之後，可以使用 `help` 來查詢相關指令
    - 如 show dbs
    -  `db.help()` 、`db.collectionName.help()`查更多指令

## Write JS Scripts For Mongo Shell
- 我們可以寫好 js 腳本，然後用 mongo shell 執行
- 用 `mongo script.js` 就行
- [Write JS Scripts For Mongo Shell](https://docs.mongodb.com/manual/tutorial/write-scripts-for-the-mongo-shell/)
# Advanced CRUD
## Create
![](https://i.imgur.com/ZXr4mGG.jpg)

- insertOne：參數是一個 document
- insertMany: 參數是 array of document
- insert: 可以放一個 document 或 array of document
    - 不建議使用
    - 降低 code 的易讀性、提高錯誤機率
    - 使用 insert 的話 mongo 並不會回傳被 insert 的 document 的 id
        - 非常不方便
        - 使用 insertOne 或 insertMany 則會回傳 _id

### Ordered Inserts
- Mongo 中 insertMany 時是依照 array 的順序 insert 的
- 如果 array 中有一筆資料是 invalid 的（例如 _id 欄位重複了）
    - mongo 會顯示 error
    - 但**在該筆資料之前的 insert 都會成功**，mongo 並不會 rollback
    - 稱為 order insert
    - 預設為 {ordered: true}
- 我們可以設定加入`{ordered: false}` 這個參數，來改變行為
    - 如 `db.collection.insertMany([{name: 1}, {name: 2}], {ordered: false})`
    - 此時當 mongod 在 insertMany 時，如果碰到某筆資料失敗，**仍會繼續 insert 剩下的資料**，並再最後回報哪些資料成功、哪些失敗

- 也就是說，對於已經 insert 的 document，Mongo 是不會 rollback 的
    - 但我們可以透過 `ordered` 來設定，當遇到某筆 insert 失敗的資料時，mongo 要不要繼續 insert 剩下資料

### Write Concern

![](https://i.imgur.com/TWGLtnT.jpg)

- 在寫入資料時 (insert, update) 等等
    - storage engine 會先寫進 memory 中，因為比較快速
    - 再寫入 disk 中
    - 在這兩個步驟之中，我們可以選擇要不要插入一個 **write to journal 的步驟**
- write to journal 可以防止 server 在寫進 memory 後、但在寫進 disk 之前發生錯誤
    - 因為 journal 的寫入比起真正寫進 db 中還輕量許多，就像寫一行字進 txt
    - 如果 server shut down 了，那就可以透過 journal 來查看是否有未完成的工作，就像 Todo list ㄧ樣
    
- write Cocern 有幾個參數
    - w 是 how many instance you want this write to be acknowledge
        - 一個 server 上可以有 mutiple instances 
        - default 為 1 (1個instance)，可以想像 server 在 memory 中創建好之後，在一個 instance 上實際創建成功後再回覆。會拿到 `acknowledge = true` 以及該物件 _id
        - 如果 w 為 0 代表 一送出整令之後不等待 server response，所以我們不會拿到被 insert 的物件的 _id，因為該物件還沒有創建好。雖然速度極快但也沒有保障。會拿到 `acknowledge = false` 的回覆。可以用在一些遺失也不重要的資料上，如 log
    - j 為 **是否要寫入 journal**
        - defaut 為 undefined(or false) ，代表當 server 回報成功給我們時，我們無法確認資料是否已經被寫入 journal 中。有可能資料沒有被寫進 journal 中，且在要寫入 disk 時發生錯誤，此時我們會誤以為資料已經 write 成功，但實際沒有
        - 如果 j 為 1 代表 server 只有在 saved to journal 之後才會回報成功，因此就算在寫進 disk 時發生錯誤，我們可以確保 server 可以透過 journal **來回覆，好處是可以保有更高的資料掌握及 security、壞處是每次 success 的時間會拉長**
    - wtimeout 是指該筆 write 指令回覆成功的 timeout 時間 (milisecond)
        - 也就是說如果時間已經到，但 server 還不能回覆 success，則 cancel
        - 可以 handle 一些網路不佳的狀況
    - 實際範例
        - `db.collection.insertOne({name: 'suki'}, {writeConcern: {w:1, j:true, wtimeout: 200}})`
        - insertMany 、update 及 delete 等等的寫入動作也都適用

### Atomicity
- Mongo 保證 **Document Level** 的 Atomicity
    - 不會有某個 document 只被寫入一半的情形發生
    - 包含 embeded document
- 要注意 mongo 不保證 inserMany 的完整性，而是保證其中每筆 document 的完整性

![](https://i.imgur.com/ZtkIRR7.png)

### Importing data
- 假如已有一個 dump file 叫 dump.json
- `mongoimport dump.json -d wantedDB -c wantedCollection --jsonArray --drop`
    - mongoimport 後接上要被 import 進去的資料位置
    - -d 設定要被 import 的 db
    - -c 設定要被 import 的 collection
    - `--jsonArray` 告訴 server 我們要 import 的是 array of document，否則預設指 import 一個 document
    - `--drop` 告訴 server 如果該 collection 已經存在，則清空後再 import data。預設是會直接把資料加入已有的 collection 中

#### export and import (JSON/CSV)
- mongoexport is a command-line tool that produces a JSON or CSV export of data stored in a MongoDB instance.
- The mongoimport tool imports content from an Extended JSON, CSV, or TSV export created by mongoexport, or potentially, another third-party export tool.

#### dump and restore (Binary file)
- mongodump is a utility for creating a binary export of the contents of a database. mongodump can export data from either mongod or mongos instances; i.e. can export data from standalone, replica set, and sharded cluster deployments.
- The mongorestore program loads data from either a binary database dump created by mongodump or the standard input (starting in version 3.0.0) into a mongod or mongos instance.
### Create Wrap up
![](https://i.imgur.com/6dJQ5Bm.jpg)

## Read
### find and findOne
- findOne() 只會回傳符合條件的第一個 document
    - 如果不帶 filter，則會回傳第一個 document


### Method, Filters, and Operators
![](https://i.imgur.com/1MSzBqh.jpg)

### Operators
![](https://i.imgur.com/uJafseM.jpg)
![](https://i.imgur.com/nsssklk.png)



### Query operator
#### Comparision Operators
- `$eq`: equal
    - `db.collection.find({name: {$eq: 'suki'}})`
    - 等同於 `db.collection.find({name: 'suki'})`
- `$ne`: not equal
- `$lt`: lower than
- `$lte`: equal or lower than
- `$gt`: greater than
- `$gte`: equal or greater than

- 對於 embeded document 的 field，要用 `"field.innerfield"` 等方法來取得
- 對於 array field
    - 如果該欄位是 array，則 `db.collection.find({someArrayField: value})` 會回傳所有**array 有包含 value** 的結果
        - 如 `db.tvs.find({genres: "Drama"})` (genres 是一個 array)，則只要 genres 中有包含 Drama 的 Document 都會被回覆
    - **如果要找到完全相等的 array 結果，則直接用 array 的形式來表達 value**，如 `db.collection.find({someArrayField: [value]})`，就會回傳有完全相等 array 的 document，**且 array 的 element 順序也需要一樣**，否則要用 $all
        - 如 `db.tvs.find({genres: ["Drama"]})` (genres 是一個 array)，則只有 genres 欄位完全等於 ["Drama"] 的 Document 會被回覆

- `$in`: 尋找某幾個特定的值
    - value 要給一個 array
    - 如 `db.movies.find({runtime: {$in: [42, 50]}})` 會回傳所有 runtime 等於 42 或 50 的 document
- `$nin`: not in，跟 in 相反，找到「不等於某幾個特定值」的 document

- **在做 `a = i or a = j` 時要用 `in` 而不是用 `or`，`in` 會有更高的效率**
    - 如 `db.movies.find({runtime: {$in: [42, 50]}})` 而不是 `db.movies.find($or: [{runtime: 42}, {runtime: 50}]})`
    - 對不同欄位做條件、或對同一個欄位做不同條件才用 `or`
#### Logical Operators
- `$or`: 最常用，找出符合某個條件的 documents
    - filter 中直接用 or 開頭，並用 array 表達所有條件
```javascript=
// 找 rating.average 欄位值 > 9 或 < 5 的 document
db.movies.find(
    {
        $or: [ // filter begin with $or
        {"rating.average": {$lt: 5}}, // array of condition
        {"rating.average": {$gt: 9}}
        ]
    }
)

```
- `$nor`: 跟 or 相反，列出不符合所有條件的 document
- `$and`: 找出符合所有條件的 documents
    - 可以用麻煩的樣式寫，但一般會直接用簡潔的樣式
    - **簡潔的樣式是直接在 find 中用逗號隔開多個 condition**, shell 會將多個 condition 合併成一個 object 並執行
    - 必須使用 and 的場合：**當多個 condition 的 field 相同時，JS 本身的 object key 是會互相複寫的，因此必須使用 `$and`**
```javascript=
// 找出符合Drama類型且 rating > 9 的 document
db.movies.find({
    $and: [
    {
    "rating.average": {$gt:9}
    },
    {
    genres: "Drama"
    }]
})
// 等同於
db.movies.find({"rating.average": {$gt:9}}, genres: "Drama")

// 必須使用 and 的場合
// 找出同時包含 Drama 跟 Horror 類別的 document 

// 錯誤範例
// 因為 object key field 相同，所以其實只要包含 genres: Horror 的 Document 都會被回傳
db.movies.find({genres: "Drama"}, {genres: "Horror"})

// 正確
// 當多個 condition 的 field 相同時，要使用 and
db.movies.find({$and: [{genres: "Drama"}, {genres: "Horror"}]})
```
- `$not`: **使用的方式跟其他 logic opetarots 不同**
    - 先指定 field 再寫 `$not`，用法比較像 Comparision Operators
    - 較少用，因為可以直接被其他相反 operator 取代
    - 如 not + equal = ne, not + gt = lte ... 等等
```javascript=
db.movies.find({runtime: {$not: {$eq: 60}}})
// 可以被 ne 取代
db.movies.find({runtime: {$ne: 60}})
```

#### Element Operators
- `$exists`: 找出擁有某些 field 的 document
    - 時常搭配 `$ne: null` 使用，如果不希望該 field 為 null 的話
    - `db.users.find({age: {$exists: true, $ne: null}})` 找出有 age 欄位的 user document 且其值不為 null
- `$type`: 找出 field 的 value 為特定 type 的 document
    - `db.users.find({phone: {$type: "double"}})` 找出 phone 是 double 的 document
    - `db.users.find({phone: {$type: ["double", "string"]}})` 找出 phone 是 double 或 string 的 document


#### Evaluation Operators
- `$regex`
    - **最有效的還是使用 text index**，會比用 regex 的效率好
    - 如果欄位沒有 index，可以用 regex 做字串的 pattern 搜尋，但效率比較差
    - regex 要用 `//` 包住頭尾
    - `db.movies.find({summary: {$regex: /musical/}})` 會返回所有 summary 包含 musical 的 document

- `$expr`: expression, 可以用來比較 document 中的兩個 field，並返回符合條件的
    - [**常用**](https://docs.mongodb.com/manual/reference/operator/query/expr/index.html)
    - 可搭配 `$cond`(condition) 使用
    - **想要動態的取得某個欄位的值，要用 "$fieldName" 的形式**
    - `db.collection.find({$expr: {$gt: ["$field1", "$field2"]}})`
    - 會找到所有 field1 值 > field2 值的 document
    
    - 可以寫出很複雜的指令，如下
```javascript=
// 如果 price > 190，檢查是否 price-10 > target 
// 否則檢查是否 price > target
// 返回符合的所有 document
db.sales.find({
    $expr: { // expression
        $gt: [ // 要比較 greater than
        $cond: { // greater than 的第一個參數，透過 condition 來取得
            if: {$gte: ["$price", 190]},  // if price > 190
            then: {$subtract: ["$price", 10]},  // then price-10
            else: "$price" // else price
        },
        "$target" // greater than 的第二個參數為 target 欄位的值
        ]
    }
})
```

> 如果想要動態的取得某個欄位的值，要用 "$fieldName" 的形式
> 
#### Array Query Selectors
- 如果想要獲取單一 subdocument 中的欄位，可以用 `"outerField.innerField"` 來做到
- 除此之外，如果是 Array of subdocument, 也可以用 `"outerField.innerField"` 來對**array 中的每一個 document 做操作**
    - **非常實用**
```javascript=
[
    {
    _id: 'someId',
    hobbies: [
        { title: 'sport1', frequency: 3},
        { title: 'sport2', frequency: 2},
        ]
    },
    {
    _id: 'someId2',
    hobbies: [
        { title: 'sport3', frequency: 3},
        { title: 'sport4', frequency: 2},
        ]
    },
]

可以用
db.users.find({"hobbies.title":"sport1"})
會回覆 someId 那個 document，因為它的 hobbies 的 title 包含了 sport1

```

- `$size`: 獲取 array 的 size
    - `db.users.find({hobbies: {$size: 3}})`
    - 以上會找到 hobbies array 中有三個元素的 document
        - 不論其中的元素是 subdocument 或不是
        - 這個寫法只能用在 exact match (size == 3)，不能搭配 $gt, $lt 等等（需要別的寫法）

- `$all`: 找到包含**所有指定欄位**的 array 的 document ，但不在意 array 中的順序
    - `db.moviestarts.find({genre: {$all: ["action", "thriller"]}})`，會找出所有 genre 包含(但不限於) action **及** thriller 的 document，順序不影響
    - 若不用 all，則變成找到 array **完全等於[action, thriller]** 的 document
    - 跟 `db.moviestars.find({$or: [{genre: 'action'},{genre: 'thriller'} ]})` 不同，這個是會找到包含 action **或** 包含 thriller 的
    - 跟 `db.moviestarts.find({$and: [{genre: 'action'},{genre: 'thriller'} ]})` 相同
```javascript=
{ tags: { $all: [ "ssl" , "security" ] } }
// 等於
{ $and: [ { tags: "ssl" }, { tags: "security" } ] }
```

- `$elemMatch`
    - 針對 array 中的每一個 subdocument 或每個 element，找出有符合條件的
- 針對以下 documents，如果想找到某個人，他的 hobby 包含 sport 且頻率大於 2 的話
    - 若使用 `db.sports.find({"hobbies.title": sport, "hobbies.frequency": {$gt: 2}})`，會連 a 也一起找到，因為 a 有 sport 且他的 cooking frequency 大於 2
    - 所以用 elemMatch 來針對 array 中的每個 element 檢查是否有 match condition
    - `db.sports.find({hobbies: {$elemMatch: {title: "sport", frequency: {$gt: 2}}}})`
    - elemMatch 是在描述 array 中的每一個 element，所以從該 element 層級描述起就好（所以用 title 而不是 'hobbies.title'）
    - 如果 array 中不是 subdocument 也可以用
    - 如 `db.movies.find({ratings: {$elemMatch: {$gt: 8, $lt: 10}}})`
```javascript=
[
  {
    name: 'a',
    hobbies: [
      { title: 'sport', frequency: 2 },
      { title: 'cooking', frequency: 3 },
    ],
  },
  {
    name: 'b',
    hobbies: [
      { title: 'sport', frequency: 3 },
      { title: 'swimming', frequency: 1 },
    ],
  },
];

```

### Cursors
- cursor 就是 pointer 的概念
- 例如我們常常一 query 就是幾千筆的資料，很消耗頻寬跟 memory
    - 使用 cursor 可以批次拿
    - 是一個做 pagination 的方法

- `count` 是一個 cursor 提供的方法之一
    - 如 `db.collectionName.find().count()`
    - 因為 find 回傳 cursor，所以 count 可以接在 find 後面使用

- `next` 會返回 cursor 的下一個 element
```javascript=
const dataCursor = db.collectionName.find() 
//因為 mongodb shell 是 js-based，所以指令跟 js 差不多
dataCursor.next() // 會拿到第一個 element
dataCursor.next() // 會拿到第二個 element
...
```

- `forEach` 會 loop through cursor 剩下的所有 document (直到結束)
    - 前面已經拿過的 document 就不會出現了
```javascript=
dataCursor.forEach(doc => {printjson(doc)})
// printjson 是一個 mongo 方法
// 所有 element 都被拿光了
dataCursor.next() // 此時會噴 error, 因為已經沒有下一個 element 了
dataCursor.hasNext() // false
```
- `hasNext` 檢查 cursor 還有沒有下一個 element

- `sort`: 也是 apply 在 cursor 的 method
    -  method: `1 = asc (小到大)`, `-1 = desc (大到小)`
    - `db.collectionName.find().sort({"rating.average": 1, runtime: -1})`
    - 先依照 average 由小到大，如果 rating 依樣再依照 runtime 由大到小排序
- `skip` & `limit`：也是 apply 在 cursor 的 method
    - skip 略過的數量
    - limit 限制一次拿回來的數量
    - 常常跟 sort 一起用

- mongo cursor 執行的順序，一定是 **`先 sort，再 skip，再 limit`**
    - 跟我們寫的順序無關

### Array Project
- 把 project option 放在 find 的第二個參數
    - db.collection.find({}, {wantedField: 1, anotherField: 1})
    - _id 預設會帶回來，除非有特別寫 _id: 0
- 一般欄位的 projection 較單純，給 1/0 就行。但要針對 array element 做 projection 的話，就需要倚靠 `$`, `$elemMatch`, `$slice` 等 operator


- `$`: 找到 array 中第一個符合指定條件的 element
    - The positional $ operator limits the contents of an `<array>` from the query results to **contain only the first element matching the query document**
    - 如下
        - `"grades.$": 1` 代表要 project (include) `grades array` 中第一個符合條件的 element

```javascript=
{ "_id" : 1, "semester" : 1, "grades" : [ 70, 87, 90 ] }
{ "_id" : 2, "semester" : 1, "grades" : [ 90, 88, 92 ] }
{ "_id" : 3, "semester" : 1, "grades" : [ 85, 100, 90 ] }
{ "_id" : 4, "semester" : 2, "grades" : [ 79, 85, 80 ] }
{ "_id" : 5, "semester" : 2, "grades" : [ 88, 88, 92 ] }
{ "_id" : 6, "semester" : 2, "grades" : [ 95, 90, 96 ] }

// query
db.students.find( { semester: 1, grades: { $gte: 85 } },
                  { "grades.$": 1 } )
                  
// 會先找出 smester = 1 且 grades 中有 element >= 85 的 document
// 再從 grades 中找到第一個符合條件（>=85）的 element

// answer
{ "_id" : 1, "grades" : [ 87 ] }
{ "_id" : 2, "grades" : [ 90 ] }
{ "_id" : 3, "grades" : [ 85 ] }
```

- 如果 `$` 搭配 $all 用，則結果跟 $all array 裡面的 element 順序有關
    - 答案一定是 $all array 中的最後一個 element
    - `db.movies.find({genres: "Drama"}, {"genres.$": 1})`
        - 回傳的 genres 中的 element 一定是 Drama，因為電腦跑 find 時去 match genres 中有沒有 Drama 存在在 genres 中，再從 genres 找出符合條件（genres: Drama) 的第一個 element，即為 Drama
    - `db.movies.find({genres: $all: ["Drama", "Horror"]}, {"genres.$": 1})`
        - 回傳的 genres 中的 element 一定是 Horror，因為電腦跑 find 時先 match genres 中有沒有 Drama、有的話再看看有沒有 Horror，如果都有 match 到條件時的第一個 element 會是 Horror（單單只有 Drama 是不行的）
    - `db.movies.find({genres: $all: ["Horror", "Drama"]}, {"genres.$": 1})`
        - 同上，回傳的 genres 中的 element 一定是 Drama
- `$elemMatch`
    - 我們也可以在 project 的階段用 elemMatch 來 project 我們想要的 array element，會回傳符合 elemMatch 條件的第一個 element
    - 如 `db.movies.find({genres: "drama"}, {genres: {$elemMatch: {$eq: "Horror"}}})`
    - 先找到所有 genres array 中友 drama 的 array
    - 再對這些 document 做 projection。我們只想要顯示 genres 中符合條件的 elem，所以用 elemMatch，並找到值等於 'Horror' 的 element
    - 所以如果回傳的 document 的 genres 中本身沒有 Horror，就只會回傳 _id；有的話就會回傳 `{_id: "", genres: ["Horror"]}`


- 在 project 用 `$` 跟 `$elemMatch` 的不同在於
    - `$` 是找在**符合 query 中對 array 條件** 的第一個 element，所以 query 中**一定要有對該 array 的條件**
    - `$elemMatch` 則跟 query 中的條件完全沒有關係。而是指在符合 query 的條件下，再去對這些 document 中的 array 去做 project 的條件篩選，依樣會找到符合 elemMatch 條件的第一個 element
    - Both the $ operator and the $elemMatch operator project the first matching element from an array based on a condition.
    - The $ operator projects the first matching array element from each document in a collection **based on some condition from the query statement.**
    - The $elemMatch projection operator takes an **explicit condition argument.** This allows you to project based on a condition not in the query, or if you need to project based on multiple fields in the array’s embedded documents.

- `$slice`
    - 拿到 array 中的前 n 個 element
    - 如 `db.movies.find({rating: {$gt: 9}}, {genres: {$slice: 2}, name: 1})`
    - 則回傳的 document ，只會顯示 name, _id, 及 genres array 中的前兩個 element
    - `$slice: n` 拿前n個 element
    - `$slice: [n, m ] ` skip 前 n 個 element 後拿 m 個 element

## Update
- `db.collection.updateOne()`
- `db.collection.updateMany()`
- update 可以帶上 upsert 參數
    - 如果 update 中 query 的 document 不存在，就 create 
- 如果 mongoDB 發現要 update 的值跟原本一樣，**就不會再去寫入**
    - 會回 {ackonwledged: true, matchedCount: 1, modifiedCount: 0}
    - 其中的 modifiedCount 代表真正有被 update 的 document
- `$set`: update 指定的 field。如果該 field 不存在就會加上去
    - `db.collection.updateMany({}, {$set: {newField: value}})` 會修改所有 documents 中的 newField，如果 newField 不存在就會加上去
- `$unset`: 拿掉某些欄位
    - `db.collection.update(query, {$unset: {fieldName: ''}})`
    - 重點是 fieldName，而 fieldName 對應到的值則完全不重要。但通常會提供空字串
- `$rename` : rename 欄位
    - `db.collection.update(query, {$rename: {oldFieldName: newFieldName}})`
    - 如果舊的欄位不存在，則不會修改到該 document
- `$inc` 增加某欄位的值：
    - `db.collections.updateOne(query, {$inc: {field: value}})`
    - value 可以是負數 （即為減法）

- `$min`: 把值設成 min(原本的值, min 的值)
    - 如原本 age 是 35，則 `update({}, {$min: {age: 38}})` 則 age 還是 35。假如原本是 40 則 age 會變成 38
-  `$max`: 把值設成 max(原本的值, max 的值)
-  `$mul`: multiply 乘法
    -  `update({}, {$mul: {age: 1.1}})` 則 age 會變成原本的 1.1 倍

- `$`：在 update 中，我們可以用 `$` 來代表 `在 array 中符合 query 條件的`**`第一個 element`**
    - `db.users.find({hobbies: {$elemMatch: {title: "Sports", frequency: ${gte: 3}}}})` 可以找到在 hobbies array 中存在某個 element 的 title 是 sports 且該 element 的 frequency >=3 的 document
    - `db.users.updateMany({hobbies: {$elemMatch: {title: "Sports", frequency: ${gte: 3}}}}, {$set: {'hobbies.$.highFrequency': true}})` 其中 `'hobbies.$'` 指的就是符合前面條件的 array 中的 **element**。這邊會修改這個 element 的 highFrequency 為 true (如果該 element 原本沒有 highFrequency 就會加上去)

- `$[]` 在 update 中，我們可以用 `$[]` 來代表某個 array 中的**所有 document** (有 forEach 的感覺)
    - `db.user.updateMany({age: {$gt: 30}}, {$inc: {"hobbies.$[].frequency": -1}})`
    - 會把所有符合條件的 document 中的 hobbies array 中的 **所有 elements** 的 frequency 都 -1 

- `$[identifier]` 可以更新所有符合 identifier (condifion) 的 array element

```javascript=
db.users.updateMany(
  {
    'hobbies.frequency': { $gt: 2 },
    // 先找出 hobbies array 中有任一 frequency > 2 的 documents
  },
  {
    $set: {
      'hobbies.$[el].good': true,
      // 針對每個 document 中 hobbies array 裡，
      // 某些 elements (這裡取名叫做 el) 加上 good 欄位
      // el 是變數，可以自己取名
    },
  },
  {
    arrayFilters: [{'el.frequency': { $gt: 5 }}],
    // 這邊要指定 el 的條件
    // el 是那些 frequency 有 > 5 的 elements
  }
);

// 假如原始資料
[
  {
    name: 'a',
    hobbies: [
      { title: 'sport', frequency: 1 },
      { title: 'cooking', frequency: 6 },
    ],
  },
  {
    name: 'b',
    hobbies: [
      { title: 'sport', frequency: 6 },
      { title: 'swimming', frequency: 7 },
    ],
  },
];

// update 過後
[
  {
    name: 'a',
    hobbies: [
      { title: 'sport', frequency: 1 },
      { title: 'cooking', frequency: 6, good: true },
    ],
  },
  {
    name: 'b',
    hobbies: [
      { title: 'sport', frequency: 6, good: true },
      { title: 'swimming', frequency: 7, good: true },
    ],
  },
];
```

`$push`: push 數個 element 到 array 中
- 加一個 element：`db.collections.updateOne(query, {$push: {arrName: {someField: someValue}}})`
- 加數個 element 到 array 中
  - 用 $push 時，可以再加上 `each`, `sort` 跟 `slice` 指令
  - each 是列出數個要 push 的 element
  - sort 會 sort **整個 array** (不是只有 each 中的 element)
  - slice 會調整 array 的長度
  - slice 一定要搭配 each 使用，each 可以是個空 array

```javascript=
// 原本的資料
{
   "_id" : 5,
   "quizzes" : [
      { "wk": 1, "score" : 10 },
      { "wk": 2, "score" : 8 },
      { "wk": 3, "score" : 5 },
      { "wk": 4, "score" : 6 }
   ]
}

// 指令
db.students.update(
   { _id: 5 },
   {
     $push: {
       quizzes: {
          $each: [ { wk: 5, score: 8 }, { wk: 6, score: 7 }, { wk: 7, score: 6 } ],
          $sort: { score: -1 }, // 整個 array 按分數大到小排序
          $slice: 3 // 只取前三個。負數的話就是只取最後 n 個
       }
     }
   }
)
// 結果
{
  "_id" : 5,
  "quizzes" : [
     { "wk" : 1, "score" : 10 },
     { "wk" : 2, "score" : 8 },
     { "wk" : 5, "score" : 8 }
  ]
}
```

- `$pop`: 從 array 中移除最後一個 (或第一個) element
    - `{$pop: {arrayName: 1}}`：移除 array 最後一個 element
    - `{$pop: {arrayName: -1}}`：移除 array 第一個 element


- `$pull` 從 array 中移除 element
    - `db.users.updateOne(query, {$pull: {hobbies: {title: "Hiking"}}})`
    - 從 hobbies array 中，移除所有 title 是 Hiking 的 elements

- `$addToSet`: 跟 push 很像，一樣是把 element 加到 array 中。**但是如果 array 中已經有一樣的 element 就不會執行**
    - `db.users.updateOne(query, {$addToSet: {hobbies: {title: "Hiking", frequency: 2}}})` 如果 array 中已經存在這個 element 就不會加進去

### Update Wrap Up
![](https://i.imgur.com/TaJbL6h.jpg)

## Delete
`db.collectionName.deleteMany({})` 會刪光 documents

- 如果用 mongo shell
`db.collectionName.drop()` 會 drop 掉 collection
`db.dropDatabase()` 會 drop 掉 db

# Indexes

- `db.collectionName.getIndexes()` 可以列出 collection 中所有 index
    - _id 是 default index


## COLLSCAN
- 沒有 index 時，會對 collection 中的所有 document 做遍尋

## IXSCAN
- index 就是針對我們的 index key額外維護一個 **order list**
    - 這些欄位有 pointer 指向我們原本的 document
- 如果 query 有用到 index，就會使用 IXSCAN，比 COLLSCAN 快很多
- index 可以讓 find 變快，但 create, update, delete 都會變慢
    - 因為要花時間維護 document 跟 index

## Explain
- mongo 提供的工具，可以寫在 find, update, delete 的指令前面
    - insert 無法使用（因為沒有查詢的動作）
    - 如 `db.collection.explain().find(query)`
- 加上 `executionStats` 可以拿到更詳細的執行數據
    - `db.collection.explain("executionStats").find(query)`
- plan：mongodb 的查詢策略
    - winning-plan：最後使用的方法
    - rejected-plan：沒採用的方法

### Stages
- IXSCAN 中的 nReturned 是指 **index key** 的 return 個數，而不是 document 本身
- IXSCAN 之後通常會再搭配 FETCH stage 
    - 用符合 query 的 index pointer 找到 document 本身。
    - 這邊的 nReturned 才是 document 本身。
- totalKeysExamined 代表整個查詢過程針對 indexKey 檢查了多少個 value
## CreateIndex
- `db.collectionName.createIndex(<fieldName>: order)`
    - order 是 1 或 -1 
    - order 通常對查詢效果影響不大。就算我們查詢時方向相反，mongo 都還是能吃到 index

## Restrictions
- index 可以幫助我們快速找到 collection 中的「特定小集合」
    - 但有時 index 可能**會讓 query 變慢**，通常發生在我們下的 query 會拿回 **`很大集合的資料`** 時
    - 例如當我們對 age 做 index，並下了 `find({age: {$gt: 20}})` 
    - query 執行時，會先執行一次 IXSCAN，再執行 FETCH。由於資料中符合 age > 20 的 document 非常多(幾乎全部都符合)，所以執行這兩階段所花的時間，會比直接對 collection 做 COLLSCAN 所需的時間還更久
    - 相反的，如果執行 `find({age: {$gt: 60}})` 由於符合的 document 是小集合，因此可以得到用 index 加速的好處

- 如果不確定 query 要使用什麼 index，最好先不要加並觀察數據，才能確保加了 index 有提升速度
> 使用 index 會讓查詢變成兩階段 (IXSCAN + FETCH)。使用 index 加速的前提時這兩階段的所花時間 < 直接去做 COLLSCAN 的時間。當 query 所返回的 document 是 collections 中的大集合時，使用 index 可能會變慢


## Compound Indexed
- compound index 宣告的順序很重要
- `{age: 1, gender: 1}` 的 compound index 會先依照 age 排序，同樣 age 的再依照 gender 排序
- 而使用 query 時，只搜尋 age 或搜尋 age + gender 都可以使用 IXSCAN 的加速。
    - 查詢 query 時，順序是 `{age: 1, gender: 1}` 或 `{gender: 1, age: 1}` 都可以，不影響 mongo 是否用 index 

## Sorting
- index 可以加速 sorting
- 能否吃到 index 跟上面 compound index 的邏輯一樣
- index 宣告時的排序 asc/desc 跟 sorting 要的排序不同沒關係
- 如果沒有用 index ，且對大量資料做 sorting 的話，有可能造成 timeout
    - 因為 mongo 要把所有 document load 到 memory 中，在 memory 中做排序

## Unique Index
- `db.collectionName.createIndex({fieldName: 1}, {unique: true})`
- _id 就是一個 unique index
- 如果某 document 不存在該欄位的話建立 unique index 時會把該欄位當作 null
    - unique index 會確保不能有兩個 null 存在。
    - 如果只想對存在該 field 的 document 做 unique index ，要搭配 partial index
    - `db.user.createIndex({email: 1}, {unique: true, partialFilterExpression: {email: {$exists: true}}})`
    - 只對 email 存在的 document 做 unique index
    - 如果有多個 email 存在但值是 null 仍然會有這個問題


## Partial Index
- 只對符合條件的 document 做 index
- 如果 query 有落在 index 條件裡，mongo 才會使用這個 index
    - 如 `db.collectionName.createIndex({age: 1}, {partialFilterExpression: {"gender": male}})` 只會對 gender 是 male 的 document 做 age 的 index
    - 如果我們 query `db.find({age: {$gt: 60}})` 就不會使用這個 index，因為他沒有指定 gender 是 male

- 如果我們使用的 query 會針對某個條件做搜尋，那使用 partial index 會比使用 compound index 好
    - 因為 partial index 建出來的 index 數量比較少。效率也會比較高
    - 如我們每次都只對 gender: male 做搜尋

## TTL (Time-To-Live) Index 
- 對 self-destroying document 很有用
- 只能加在 **date field** 上
- 如 session
    - `db.sessions.insertOne({data: "someRandomData", createdAt: new Date()})`
    - `db.sessions.createIndex({createdAt: 1}, {expireAfterSeconds: 10})`
    - 有了 TTL index 後 ，mongo 會檢查是否有 document 已經超過 TTL 的 expireTime，有的話就會把這些 document 刪除
    - 不確定 TTL index 被 delete 的機制，可能是 60 seconds interval 或 insert document 時會 trigger mondo 檢查並刪除超過 TTL 的 document
    - 只能在單一 date field 上運作，不支援 compound

## Query Diagnosis

### Explain
- 透過 explain 我們可以看到 mongo 怎麼選 plan
![](https://i.imgur.com/kUwZsZL.png)

### Efficient Queries
- 幾個指標
    - Millisecond Process Time：總共花多少時間
    - key examined: 去 index key 中查訊的數量
    - documents exmained：在 collection 中查詢的 document 數量
    - document returned：最終返回的 document 數量
- 好的 query 
    - Millisecond Process Time 越小越好
    - `# keys examined` 跟 `# Documents Examined` 越接近越好
    - `# Documents Examined` 跟 `# Documents Returend` 越接近越好
    - 或 `# Documents Examined` 是 0
        - 又稱 **Coverd Query**
    

- Coverd Query
    - 有 `# keys examined` 跟 `# Documents Returend`  且  `# Documents Examined` 是 0 
    - 因為 index 中有儲存 `index value 本身` 及 `指向 document 的 pointer`
        - 如果我們要查找的資訊在 index 中就有儲存，則不需要回到 document 中查訊
        - **可以直接透過 IXSCAN 查找而省略 FETCH 就直接返回 document**
        - 如針對以下資料做 `{name: 1}` 的 index
        - `[{name: "Max", age: 29}, {name: "suki", age: 24}]`
        - 則 `db.users.find({name: "Max"}, {name: 1, _id: 0})` 就是 cover query，因為他只需要 return name 而不需要 return _id 或其他資訊，所以透過 index 本身就可以回傳 document，省去了 FETCH 的階段
        - 超級高效率

![](https://i.imgur.com/rAzdHQX.png)

## Chossing plan

- 假設我們有 name 跟 age_name 的 index
- 在搜尋 {name, age} 時，mongo 會先列出所有可用的 index
    - 有兩個 approach： name / age_name index
    - 針對該 query，mongo 會先用小量的 dataSet 讓每個 approach 都跑跑看，看誰勝出（如誰先最快找到 100 個 document），這個 approach 就是 winning plan
    - 之後再用這個 winning plan 跑整個 collection

### Cache winning plan
- 如果每個 qeury 都要先找出 winning plan 會很耗時
- 因此 mongo 會 cache winning plan，當有相同的 query 時就直接用這個 winning plan


![](https://i.imgur.com/331AyN3.png)

### expire winning plan
- 某些情境下 winning plan 的 cache 會過期

![](https://i.imgur.com/ErOgLrH.png)


## MultiKey Index
### Array
- 針對 array 中的 element 做 index
    - index 的數量會很大，假如一個 document 的 array 中有 4 個 element，有 1000 個 document，那 index 就會有 4000 個 value

- may lead to performance issue

假設資料
```javascript=
[{
    name: "123",
    hobbiesA: ["Sports", "Cooking", "Hiking"],
    hobbiesB: [
        {
            value: "Sports"
        },
        {
            value: "Cooking"
        },
        {
            value: "Hiking"
        }
    ]
}]
```

- 建立 multiIndex
    - createIndex({hobbiesA: 1})
        - hobbiesA 中的每個 element 都是 index key 的 value
        - index key 是 hobbiesA
        - index value 是 `["Sports", "Cooking", "Hiking"]`
    - createIndex({hobbiesB: 1})
        - hobbiesB 中的每個 element 都是 index key 的 value
        - index key 是 hobbiesB
        - index value 是 `[{value: "Sports"},{value: "Cooking"},{value: "Hiking"}]`
        - 查詢的時候需要查整個 object 才能吃到 index
        - find({hobbiesB: {value: "Sports"}})
    - createIndex({'hobbiesB.value': 1})
        - hobbiesB 中的每個 element 的 value 都是 index key 的 value
        - index key 是 hobbiesB.value
        - index value 是 `["Sports", "Cooking", "Hiking"]`
        - 查詢的時候 `find({'hobbiesB.value': "Sports"})` 可以吃到 index

- 限制：
    - 不能在 compound 中使用兩個以上的 multikey index
    - 可以一個 multikey index 搭配其他單一 field index 組合成 compound index

### Text Index
- 是一種特殊的 multiKey Index
- `db.collection.createIndex({someTextField: 'text'})`
    - 如果寫 `someTextField: 1|-1` 只會創出一般的 index
- 每個 collection 中**只能有一個 text index**
    - 建立 text index 很 expensive (占空間)
    - 比起使用 regex 在 text 中搜尋的效率要高很多
- 如果有多個 field 都想做 text index，可以把多個 field 結合在一起變成新的 field，再對這個 field 做 text index
    - 方法很簡單，在宣告 text index 時把所有 field 列出來就好
    - `db.collection.createIndex({TextField1: 'text', TextField2: 'text'})`
    - 如 `db.collection.createIndex({title: 'text', description: 'text'})`

- 搜尋時 `db.collectionName.find({$text: {$search: "some string"}})`
    - 因為一個 collection 只能有一個 text index，所以不用指定 field
    - `db.collectionName.find({$text: {$search: "red book"}})`
        - 搜尋的 string 是 "red", "book"，則有包含 'red' 或 'book' 的都會被搜尋到
    - 如果想要搜尋整個 pharse "red book"，要把它包在**雙引號**中
        - `db.collectionName.find({$text: {$search: '"red book"'}})`
    - 預設 `caseSensitive: false` 搜尋時用大小寫都沒關係
    - 或可以設定 `find({$text: {$search: "someText", caseSensitive: true}})`

- mongo 會把 string 做基本處理之後變成 array of words
    - 包含要做 index 的 string 跟搜尋時下的 string
    - 先斷詞、移除 stop words、stemming word、轉成小寫
    - 變成 array of meaning words
    
#### Exclude words
- 除了正面列出想搜尋的字，還可以列出要排除的字
- `db.collectionName.find({$text: {$search: "red -book"}})`
    - 會找到包含 red 但不包含 book 的 documents
#### Sorting Test Index
- mongo 在 search text 的時候會給 score
    - 放在 `{$meta: textScore}` 中
- 我們希望 result 根據 score 來排序
    - `db.products.find({$text: {$search: "some text"}}).sort({score: {$meta: "textScore"}})`

#### Languages and Weight

`db.collection.createIndex({title: 'text', description: 'text'}, { default_language: 'someLanguage', weights: { title:1, description: 10}})`

- text index 的 defalut languages 是 english
- language 會影響 word 的處理邏輯
    - stemmed 是處理字根的邏輯
    - 會有不同的 stop words
- weights 可以設定 field 之間的比重來影響 score
    - 如我們希望出現在 description 中的分數 : 出現在 title 的分數是 10: 1

## Building Indexes
### Foregroud vs Background
![](https://i.imgur.com/0sd1baF.png)
- 在 Production 環境要用 Backgroud !
    - `db.collectionName.createIndex(index, {background: true})`

## Index Wrap Up
![](https://i.imgur.com/bw9neNW.jpg)


# Aggregation
## Aggregation Pipeline
- 每一個 stage 的 input 都是上一個 stage 的 output
    - `db.collectionName.aggregate([stage1, stage2, ....])`
- 第一個 stage 的 input 是 collection 中的所有 documents
    - mongo 不會真正把所有 documents 都 load 出來再進行 aggregate
    - aggregate 也是回傳 cursor
    - aggregate 也可以使用 index

### $match
- 就跟 query filter 一樣

### $group

```javascript=
db.persons.aggregate([
    {$group: {
        _id: {myState: "$location.state", myAge: "$age"},
        totalPerson: { $sum: 1}
    }}
])
```
- _id：接收一個 object，列出要 group 在一起的欄位
    - _id: {自定義的 fieldName: 原本的 fieldName }
    - 其中 "$location.state" 跟 "$age" 是指原本 document 中的 field
    - 會把這兩個 field group 起來，回傳所有 group 的可能結果

- 其他 field
    - { 自定義的 fieldName: {產生 value 的 aggregation} }
    - 可以在 $group stage 使用的 aggregation 有列在 document 中
    - 如 `$sum: number` 會針對每一筆資料，在 group 的結果中加上 number
    - [doc](https://docs.mongodb.com/manual/reference/operator/aggregation/group/index.html)

- 回傳的 document 只會有 _id 跟我們新加的其他 field
    - _id 會是 document 的格式
    - 原本的其他 field 都會消失

### $sort 
- 可以被放在任何 stage
```javascript=
db.persons.aggregate([
    { $match: { gender: 'female' } },
    { $group: { _id: { state: "$location.state" }, totalPersons: { $sum: 1 } } },
    { $sort: { totalPersons: -1 } } // 針對上一個 stage 的 output 來 sort
]).pretty();

```

### $project
- $project
    - 跟 find 的 projection 很像，但可以有更強大的操作
    - 是 aggregation 中**很重要、常用的功能**

#### 拼出名字
- $concat: [string]
    - 會把 array 中的 string join 起來
- $toUpper：string
    - 會把 string 變成 uppercase
- $substrCP： [string, startedIndex, length]
    - substring
- $subtract: [a, b] 
    - 回傳 a-b
- $strLenCP: string
    - 回傳 string 的長度

```javascript=
db.persons.aggregate([
    {
      $project: {
        _id: 0, // 不要 _id
        gender: 1, // 要 gender
        fullName: { // 產生一個新的 field 叫 fullName
          $concat: [
            { $toUpper: { $substrCP: ['$name.first', 0, 1] } },
            {
              $substrCP: [
                '$name.first',
                1,
                { $subtract: [{ $strLenCP: '$name.first' }, 1] }
              ]
            },
            ' ',
            { $toUpper: { $substrCP: ['$name.last', 0, 1] } },
            {
              $substrCP: [
                '$name.last',
                1,
                { $subtract: [{ $strLenCP: '$name.last' }, 1] }
              ]
            }
          ]
        }
      }
    }
  ]).pretty();

```

#### 轉換資料類別
- $convert：
    - 把 input 轉換成某個 type。
    - 可以指定 onError 或 onNull 時要給的 default 值

- $convert 的 shortCut
    - mongo 文件中有提供例如 `$toDate`, `$toString` 之類的方法
    - 其實就跟 $convert 是一樣的，但 $convert 可以提供 onError 跟 onNull
    - 如下面 `birthdate: { $convert: { input: '$dob.date', to: 'date' } }`
    - 等同於 `birthdate: { $toDate: '$dob.date' } `

- $isoWeekYear: 從 date 中取出年份
```javascript=
db.persons.aggregate([
    {
      $project: {
//       轉換成 date 格式
        birthdate: { $convert: { input: '$dob.date', to: 'date' } },
        age: "$dob.age",
        location: {
          type: 'Point',
          coordinates: [ // 直接定義 coordinates 是一個 array of point
            {
//             轉換成 double 格式，error/null 的話給 0.0 當 default
              $convert: {
                input: '$location.coordinates.longitude',
                to: 'double',
                onError: 0.0,
                onNull: 0.0
              }
            },
            {
              $convert: {
                input: '$location.coordinates.latitude',
                to: 'double',
                onError: 0.0,
                onNull: 0.0
              }
            }
          ]
        }
      }
    }
  ]).pretty();

```

### $out
- 通常在 aggregation 的最後面
- 可以把 aggregation 的結果寫入另一個 collection
    - 已存在或新的 collection 都行

- `{$out: 'collectionName'}`

## Working with Array
### $push
- pushing elements into arrays
- 如下
    - 會把被 age 相同的 group 在一起
    - 被 group 在一起的 document 的 hobbies 都被 push 進 `aNewArrayOfHobbies` 這個 array 中
    - 假如原本 `hobbies` 就是一個 array，則 aNewArrayOfHobbies 就會是 **array of array**
```javascript=
db.collections.aggregate([
    {
        $group: {
            _id: {age: "$age"}, 
            aNewArrayOfHobbies: {$push: 'hobbies'}
        }
    }
])
```
### addToSet
- 跟 push 很像，但可以保證 array 中不會有重複的 value

### $unwind
- `{$unwind: "$arrayPath"}`
- 把原本是 array 的 document 攤平
    - group 是把多個 doucment  -> 一個 document
    - unwind 會把一個 document -> 多個 document
- 也就是會 flatten array
- 拆成多個 document 之後其他欄位的值都保持不變， _id 也仍會一樣
```javascript=
//原本的 document
[{
    _id: ObjectId('111'),
    name: 'mark',
    hobbies: ['sports', 'cooking', 'climbing']
}]

// db.collection.aggregate([ {$unwind: "$hobbies"} ])
[
    {
        _id: ObjectId('111'),
        name: 'mark',
        hobbies: 'sports'
    },
    {
        _id: ObjectId('111'),
        name: 'mark',
        hobbies: 'cooking'
    },
    {
        _id: ObjectId('111'),
        name: 'mark',
        hobbies: 'climbing'
    }
]
```

### $slice
- 兩種形式
    - `{ $slice: [ <array>, <n> ] }`
        - n > 0，找頭 n 個 element
        - n <0 ，找最後 n 個 element
    - `{ $slice: [ <array>, <position>, <n> ] }`
        - 從 position 開始找 n 個 element

```javascript=
db.collectionName.aggregate([
    {
        $project: { 
            _id: 0, 
            examScore: {$slice: ["$examScores", 2, 1]}
        }
    }
])
```

### $size
- 拿到 array 的長度
```javascript=
db.collectionName.aggregate([
    {
        $project: { 
            _id: 0, 
            numOfExam: {$size: "$examScores"}
        }
    }
])
```

### $filter
- 針對 array 中的 element 做 filter
- 在 filter 中常常使用到 `"$$"` 來指令 array 中的每一個 element
    - 下面範例中，如果用 $sc 會去 document 的那一層找名為 sc 的欄位
    - 要用 $$ 才是指 array 中的每個 element
- 邏輯跟 js 中的 filter 一樣

```javascript=

// data
[{
    _id: "someId",
    examScores: [
        {
            difficulty: 3,
            score: 70
        },
        {
            difficulty: 4,
            score: 65
        }
    ]
}]

// 
db.collectionName.aggregate([
    {
        $project: {
            _id: 0,
            filteredScores: {
                $filter: {
                    input: "$examScores", // 要做 filter 的 array
                    as: 'sc', // 幫 array 中的每個 element 命名
                    cond: { $gt: ["$$sc.score", 60]}
                    // 這邊要用 $$ 才能指到剛剛命名的 sc
                    // $$sc 是 array 中的每個 element
                    // 如果用 $sc 會去 document 的那一層找名為 sc 的欄位
                }
            }
        }
    }
])
```

### $bucket 跟 $bucketAuto
- 把資料分群（放在不同 bucket 的感覺）
- $bucket
    - groupBy：要用來分群的資料欄位
    - boundaries：每個 bucket 的 range
        - 18...30 是一個 bucket 
        - 以此類推
        - 60...120 是一個 bucket
    - output: 每個 bucket 中有什麼欄位，可以做一些統計的數據


- boundaries
```
An array of [ 0, 5, 10 ] creates two buckets:
[0, 5) with inclusive lower bound 0 and exclusive upper bound 5.
[5, 10) with inclusive lower bound 5 and exclusive upper bound 10.
```
```javascript=

db.persons.aggregate([
    {
      $bucket: {
        groupBy: '$dob.age',
        boundaries: [18, 30, 40, 50, 60, 120],
        output: {
          numPersons: { $sum: 1 },
          averageAge: { $avg: '$dob.age' }
        }
      }
    }
  ]).pretty();
```

- $bucketAuto
    - 只需要指定 bucket 數量，讓 mongo 自己幫我們平均區分 bucket 的 boundary

```javascript=
db.persons.aggregate([
    {
      $bucketAuto: {
        groupBy: '$dob.age',
        buckets: 5,
        output: {
          numPersons: { $sum: 1 },
          averageAge: { $avg: '$dob.age' }
        }
      }
    }
  ]).pretty();
```

## Order of Pipeline
- 在 aggregation 中，pipeline 的順序是很重要的
- 每一個 stage 的 input 都是上一個 stage 的 output
- 在我們用 `db.find().sort().limit().skip()` 時
    - sort, limit, skip 的順序不重要
    - mongo 會幫我們先 sort -> skip -> limit
    - 在用 aggregate 時就需要注意這三個的順序

```javascript=
db.persons.aggregate([
    { $match: { gender: "male" } },
    { $project: { _id: 0, gender: 1, name: { $concat: ["$name.first", " ", "$name.last"] }, birthdate: { $toDate: "$dob.date" } } },
    { $sort: { birthdate: 1 } },
    { $skip: 10 },
    { $limit: 10 }
  ]).pretty();
```

## Optimize Aggregation Pipeline
- mongo 會在不影響邏輯的情況下，幫我們 optimize pipeline
- [doc](https://docs.mongodb.com/manual/core/aggregation-pipeline-optimization/)

## Aggregate Wrap Up
![](https://i.imgur.com/hO8k9Sv.png)

# Performance, Fault Tolerance & Deployment
## What Influences Performance
![](https://i.imgur.com/M8tmyMV.png)

## Capped Collection
- 要顯示創建的 collection
- 定義這個 collection 的容量/document 數量
    - 當超過上限時 ，**old document** 會自動被刪除
    - 適合存 log、cache

`db.createCollection('collectionName', {capped: true, size: 10000, max: 5})`

- size: collection 的容量上限
    - If the size field is less than or equal to 4096, then the collection will have a cap of 4096 bytes
- max: collection 的 document 數量上限
    - optional

## Replica Sets
- client 端送指令給 mongoDB server 時，server 會直接跟 primary Node(a server, a instance) 溝通
- 寫入操作時
    - mongo server 直接對 primary node 做操作
    - 之後 primary node 會非同步的更新到 secondary Node


![](https://i.imgur.com/HNXiNcn.png)


- 好處是當 primary node 不小心掛點時，會有某個 secondary node 成為 primary node
    - 服務可以持續，replica set 可以自動 restore
    - 寫入操作依然針對（新的）primary node

![](https://i.imgur.com/TL98p42.png)


- 透過良好的設定
    - read 操作也可以直接針對 secondary node 讀取
    - 分散流量
    - write 操作 always 只對 primary node 操作

![](https://i.imgur.com/R7pvIDZ.png)

## Sharding (Horizontal Scaling)
- 這些水平擴容的 server **並不會 backup data**
    - 而是分散資料
    - 跟 replica set 不同，replica set 的不同 node 存著相同的 data
- 這些 server 需要**一起** 存在才能支援整個 app
- 當進行 query 時（不論讀寫）
    - query 需要知道要去 **哪個 server**
    - 或是 query 需要進到 **每個 server**
    - 看 data 怎麼切的


![](https://i.imgur.com/Ou82KlU.png)

- 每一個 shard 可以（且通常）是一組 replica sets
### shard key
- 透過 mongodb 的 mongos (Router) 透過 shard key 把 query 指向正確的 shard
    - 我們需要定義 shard key 來 split data
    - document 上會加上 shard key，判斷 document 屬於哪個 shard
    - shard key 的定義需要技巧，因為資料分布的 **越平均** 越好

![](https://i.imgur.com/FvTUPDa.png)

- mongos 有兩種執行 query 的可能
    - 如果 query 中沒有 shardKey
        - mongos 不知道要找哪個 shard
        - 只能 BroadCast 到每個 shard，再 merge 所有 return 結果
    - query 中有 shardKey
        - mogos 直接找到負責的 shard
        - 效率高

![](https://i.imgur.com/dhOCHmu.png)

- 開發人員需要
    - 協助定義 shard key，看 app 中哪個 shard key 有平均分散
    - 在 query 中盡量使用到 shard key 提高效能（跟index的感覺有點像）

## Deployment
![](https://i.imgur.com/zbosKVT.png)

## Module Wrap Up
![](https://i.imgur.com/5MPpgOy.png)

# Transaction
- 4.0 以上的功能
- mongodb atlas 的免費版 M0 目前可以支援
    - Atlas locks the MongoDB version and storage engine for M0/M2/M5 clusters to MongoDB 4.0 with WiredTiger.
- **需要在 replica set 環境下才能使用**

- 原本的 mongo 只有 documetn level 的 atomic
    - 只能保證一個 documet 的寫入是完全成功/失敗
    - 就連 **insertMany, deleteMany** 這類的操作，都可能成功失敗交雜

- 我們可以用 transaction 保證 multiple document level 的 atomic
    - 可以跨 collection
    - 甚至可以只用在 **insertMany, deleteMany** 這類的操作
## How to use
- 要定義 session 
    - 如此 mongo server 才能 identify 這些 request 是同一組的
- 在 transaction commit 之前，所有的操作都不會真的生效
    - 像是被擺在一個 todos 中
- 在 mongo shell 中
```javascript=
const session = db.getMongo().startSession(); // 開始一個 session
// 把指向 collection 的指標存起來
const postsColl = session.getDatabase('blog').posts; 
const usersColl = session.getDatabase('blog').users;

// 開始一個 transtion 及操作
session.startTransaction()
usersColl.deleteOne({_id: 'someUserId'});
postsColl.deleteMany({userId: 'someUserId'})

// commit or abort 一個 transaction
session.commitTransaction()
// session.abortTransaction()

// 此時資料已經被一起刪除
// 如果操作失敗，則資料都不會被刪除

```

### Node
```javascript=
// For a replica set, include the replica set name and a seedlist of the members in the URI string; e.g.
  // const uri = 'mongodb://mongodb0.example.com:27017,mongodb1.example.com:27017/?replicaSet=myRepl'
  // For a sharded cluster, connect to the mongos instances; e.g.
  // const uri = 'mongodb://mongos0.example.com:27017,mongos1.example.com:27017/'

  const client = new MongoClient(uri);
  await client.connect();

  // Prereq: Create collections. CRUD operations in transactions must be on existing collections.

  await client
    .db('mydb1')
    .collection('foo')
    .insertOne({ abc: 0 }, { w: 'majority' });

  await client
    .db('mydb2')
    .collection('bar')
    .insertOne({ xyz: 0 }, { w: 'majority' });

  // Step 1: Start a Client Session
  const session = client.startSession();

  // Step 2: Optional. Define options to use for the transaction
  const transactionOptions = {
    readPreference: 'primary',
    readConcern: { level: 'local' },
    writeConcern: { w: 'majority' }
  };

  // Step 3: Use withTransaction to start a transaction, execute the callback, and commit (or abort on error)
  // Note: The callback for withTransaction MUST be async and/or return a Promise.
  try {
    await session.withTransaction(async () => {
      const coll1 = client.db('mydb1').collection('foo');
      const coll2 = client.db('mydb2').collection('bar');

      // Important:: You must pass the session to the operations

      await coll1.insertOne({ abc: 1 }, { session });
      await coll2.insertOne({ xyz: 999 }, { session });
    }, transactionOptions);
  } finally {
    await session.endSession();
    await client.close();
  }
```

