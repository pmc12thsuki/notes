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

> JSON 中的 key-value pairt 中，key 可以不加上引號、而 value 如果是 string，則單引號、雙引號都可以
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
        - 如 ObjectId type 在 JSON 中市不合法的，但在 BSON 中就可以
        - 或是其他 Number Types


![](https://i.imgur.com/vIpT1Fb.jpg)

# Basic
## CRUD & Mongo
- 不同工作性質的專案都會透過 CRUD 跟 MongoDB 互動
    - 包含 app
    - 資料分析
    - admin 管理等等
![](https://i.imgur.com/DhemkqY.jpg)

## Basic Operations
![](https://i.imgur.com/sek6vMr.jpg)

### Create
- create document 時 Mongo DB 會主動 assign 一個獨特的 _id 欄位
    - value type 是 `ObjectId()`
    - JSON 跟 JS 中是沒有 ObjectId 這個 type 的，可見 Mongo 雖然輸入的格式是 JSON 的樣式，但真正儲存的不是 JSON
- 我們可以使用自己的 _id，且不一定要是 `ObjectId` type
    - 只要在 insert 時帶上 `_id` 欄位就行
    - 我們要自己保證 _id 不會重複，如果重複就會 insert 失敗

- insertOne({要被 insert 的資料})
- insertMany(**array of 要 insert 的資料**)
    - **裡面放 array**
    - mongo 在 insert 時會依照 array 的順序去 insert
- 使用 mongo shell 時，insert 的 document 中，key 可以不需要用 `''` 包起來。如 `db.collection.insertOne({name: 'myName'})`
### Read
- find({找出所有符合條件的 document})
    - `db.collentions.find()` 不帶參數的話會回傳所有 data
    - 可以對於某個 field 下更明確的查找指令，寫在該 field 的 object 中
    - 如 `$gt: greater than`
    - `db.flightData.find({distance: {$gt: 1000}})`
- findOne({找出**第一個**符合條件的 document})
- 可以加上 pretty() 讓結果顯示較易閱讀。 `db.collection.find().pretty()`

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
- find 這個操作，其實並不會回傳所有符合的 data，
    - 為了效率，find() 其實是回傳一個 **cursor object**
    - **而不是 list of document**
- 透過這個 cursor object，我們可以輕易 cycle through the result
    - 在 shell 中，預設會先回傳 20 筆資料
        - 我們可以用 `it` 這個指令，來獲得下一批 result data （下 20 筆資料）
- 如果我們使用 `db.collection.find().toArray()`，則 cursor 會 loop 所有資料並裝進一個 array 中，再回傳**所有的data**
    - 當 data 很多時效率會很差
- 我們也可以在 shell 中使用 `db.collection.find().forEach(data => doSomething)` 來操作 cursor 並對每一筆 data 做操作
    - 如 `db.datas.find().forEach( data => printjson(data))`
    - 效率比直接 `toArray` 還好，因為不會一次把所有 data load 進 memory 中，而是一筆一筆操作 （一批一批）

- 其他操作（Create, update, delete）就沒有 cursor


![](https://i.imgur.com/G7qvYBG.png)

#### pretty 
- pretty 是一個操作在 cursor 上的 function
- 所以可以用 `db.collection.find().pretty()` 來 print 資料
    - 因為 find 會回傳 cursor
- 但不可以用 `db.collection.findOne().pretty()`
    - 因為 findOne 會直接回傳一筆 document，而不是一個 cursor


### Update
- updateOne({找出要被 update 的 document}, {描述要做的改變})
- 在第二個參數中，可以給很多指令
    - 第二個參數是一個 object
        - key 為指令 (operators)
        - value 為另一個要賦予的 key-value pair
    - operators 如 $set：修改已有的欄位值、如果沒有就加上這個欄位
```bash=
db.collection.updateOne(
    {name: 'suki'}, // 要update的條件
    {$set: {age: 18}}) // 要進行的 update 指令
```

- updateMany({找出要被 update 的 document}, {描述要做的改變})
    - 如果沒有指定條件 (為空 object)，就會全部 update 
    - 如 `db.collection.updateMany({}, {$set: {marker: 'toDelete'}})` 則所有 document 都會加上 marker 這個欄位
    - **要慎用！**
    - updateOne 跟 updateMany，第二個參數都需要搭配 $set 之類的 operator 來使用
    
- update({找出要被 update 的 document}, {描述要做的改變})
    - **應該要直接避免使用**
    - 第二個參數如果帶上 operator，效果就跟 updateMany ㄧ樣
        - 如 `db.collection.update({_id:someId}, {$set: {isUpdate: true}})`
        - 會將所有符合條件的 document 都 set `isUpdate` 的欄位與值
    - **第二個參數如果沒有帶上operator，則會直接 overwrite document**
        - 以第二個參數來 overwrite 所有符合條件的 object
        - 如 `db.collection.update({_id:someId}, {isUpdate: true})`
        - **會將所有符合條件的 document 都 overwrite 成為只剩 _id 跟 isUpdate 的 document，原有的其他欄位都會消失！！！**
        - _id 欄位一定會被保留
        - **如果想要達成 replace，則應該直接使用 `replaceOne 之類的 function`**
> updateOne 跟 updateMany 都必須要帶上 operator（如 $set）來準確描述對資料的操作，否則會 error
> 
> update 則可以帶上 operator、也可以不要。
> 如果帶上 operator 那效果就跟 updateMany ㄧ樣。
> 如果沒有帶上 operator，則會直接 **overwrite 原先有的 document**，使用時應該要避免使用 update 以免誤用
### Delete
- deleteOne({找出要被 delete 的 document})
- deleteMany({找出要被 delete 的 document})
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
// 如下指令，因為使用 status.description 是 key，所以要用雙引號包住
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
    - List of Data
    - List of Document 都行
    - **在 query 時，如果使用 find，則只要 array 中有該資料就符合條件**
```javascript=
// 如下指令，因為 hobbies 是一個 array，所以只要包含 sport 的都會符合
db.passengers.find({hobbies: 'sport'})

// 則下列符合條件，所以會回傳
{
    "_id": someId,
    "name": "suki",
    "age": 18,
    "hobbies": [
        'sports',
        'cooking',
        // ... more
    ]
}
```

### Resetting DB
- drop db
    - `use databaseName` -> `db.dropDatabase()`
- drop collection
    - `db.myCollection.drop()`

## 整理
![](https://i.imgur.com/3SEYWjh.jpg)

# Schemas and Relations
## Schema
- 雖然 Mongo DB 完全是 schemaless 的
    - 每個 documet 中的 field 可以完全不同
- 但在現實使用中，我們在使用上仍然有 schema 的需求
    - 因此我們還是會定義 mongodb 的 collection schema

![](https://i.imgur.com/FIAiLVu.jpg)

## Schema Validation
- 我們可以設定 validation，使得在 insert 時會檢查
![](https://i.imgur.com/KtakIRN.png)

- validation 也有很多不同的 level
    - 可以只發出警告、或是會給出 error
    - 或是是否允許在設定 schema 後，再去 update 那些曾經不符合 schema 的 document... 等等
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
### Data types
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

## Relations
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

### 整理
![](https://i.imgur.com/KzADAzx.jpg)


### 使用 Lookup
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

## 整理
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
### 整理
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
    - The positional $ operator limits the contents of an <array> from the query results to **contain only the first element matching the query document**
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

### 整理
![](https://i.imgur.com/TaJbL6h.jpg)

## Delete
`db.collectionName.deleteMany({})` 會刪光 documents

- 如果用 mongo shell
`db.collectionName.drop()` 會 drop 掉 collection
`db.dropDatabase()` 會 drop 掉 db

## Indexes

- `db.collectionName.getIndexes()` 可以列出 collection 中所有 index
    - _id 是 default index


### COLLSCAN
- 沒有 index 時，會對 collection 中的所有 document 做遍尋

### IXSCAN
- index 就是對我們建 index 的欄位額外維護一個 **order list**
    - 這些欄位有 pointer 指向我們原本的 document
- 如果 query 有用到 index，就會使用 IXSACAN，比 COLLSCAN 快很多
- index 可以讓 find 變快，但 create, update, delete 都會變慢
    - 因為要花時間維護 document 跟 index

### Explain
- mongo 提供的工具，可以寫在 find, update, delete 的指令前面
    - insert 無法使用（因為沒有查詢的動作）
    - 如 `db.collection.explain().find(query)`
- 加上 `executionStats` 可以拿到更詳細的執行數據
    - `db.collection.explain("executionStats").find(query)`
- plan：mongodb 的查詢策略們
    - winning-plan：最後使用的方法
    - rejected-plan：沒採用的方法

#### Stages
- IXSCAN 中的 nReturned 是指 **index key** 的 return 個數，而不是 document 本身
- IXSCAN 之後會再搭配 FETCH stage，用 index key 找到 document 本身。這邊的 nReturned 才是 document 本身。
- totalKeysExamined 代表整個查詢過程檢查了多少個 indexKey
### createIndex
- `<fieldName>: order`
    - order 是 1 或 -1 
    - order 通常對查詢效果影響不大。就算我們查詢時方向相反，mongo 都還是能吃到 index

### Restrictions
- index 可以幫助我們快速找到 collection 中的「特定小集合」
    - 但有時 index 可能**會讓 query 變慢**，通常發生在我們下的 query 會拿回 **`很大集合的資料`** 時
    - 例如當我們對 age 做 index，並下了 `find({age: {$gt: 20}})` 時
    - query 執行時，會先執行一次 IXSCAN，再執行 FETCH。由於資料中符合 age > 20 的 document 非常多(幾乎全部都符合)，所以執行這兩階段所花的時間，會比直接對 collection 做 COLLSCAN 所需的時間還更久
    - 相反的，如果執行 `find({age: {$gt: 60}})` 由於符合的 document 是小集合，因此可以得到用 index 加速的好處

- 如果不確定 query 要使用什麼 index，最好先不要加並觀察數據，才能確保加了 index 有提升速度
> 使用 index 會讓查詢變成兩階段 (IXSCAN + FETCH)。使用 index 加速的前提時這兩階段的所花時間 < 直接去做 COLLSCAN 的時間。當 query 所返回的 document 是 collections 中的大集合時，使用 index 可能會變慢
> 

### Compound Indexed
- compound index 宣告的順序很重要
- `{age: 1, gender: 1}` 的 compound index 會先依照 age 排序，同樣 age 的再依照 gender 排序
- 而使用 query 時，只搜尋 age 或搜尋 age + gender 都可以使用 IXSCAN 的加速。
    - 下查詢 query 時，順序是 `{age: 1, gender: 1}` 或 `{gender: 1, age: 1}` 都可以，不影響 mongo 是否用 index 

### Sorting
- index 可以加速 sorting
- 能否吃到 index 跟上面 compound index 的邏輯一樣
- index 宣告時的排序 asc/desc 跟 sorting 要的排序不同沒關係
- 如果沒有用 index ，且對大量資料做 sorting 的話，有可能造成 timeout
    - 因為 mongo 要把所有 document load 到 memory 中，在 memory 中做排序

### Unique Index
- `db.collectionName.createIndex({fieldName: 1}, {unique: true})`
- _id 就是一個 unique index
- 如果某 document 不存在該欄位 or 該欄位值是 null，unique index 會確保不能有兩個 null 存在。如果只想對存在該 field 的 document 做 unique ，要搭配 partial index
    - `db.user.createIndex({email: 1}, {unique: true, partialFilterExpression: {email: {$exists: true}}})`
    - 只對 email 存在的 document 做 unique index
    - 如果有多個 email 存在但值是 null 仍然會有這個問題


### Partial Index
- 只對符合條件的 document 做 index
- 如果 query 有落在 index 條件裡，mongo 才會使用這個 index
    - 如 `db.collectionName.createIndex({age: 1}, {partialFilterExpression: {"gender": male}})` 只會對 gender 是 male 的 document 做 age 的 index
    - 如果我們 query `db.find({age: {$gt: 60}})` 就不會使用這個 index，因為他沒有指定 gender 是 male

- 如果我們使用的 query 會針對某個條件做搜尋，那使用 partial index 會比使用 compound index 好
    - 因為 partial index 建出來的 index 數量比較少。效率也會比較高
    - 如我們每次都只對 gender: male 做搜尋

### TTL Index (Time-To-Live)
- 對 self-destroying document 很有用
- 只能加在 **date field** 上
- 如 session
    - `db.sessions.insertOne({data: "someRandomData", createdAt: new Date()})`
    - `db.sessions.createIndex({createdAt: 1}, {expireAfterSeconds: 10})`
    - 有了 TTL index 後 ，mongo 會檢查是否有 document 已經超過 TTL 的 expireTime，有的話就會把這些 document 刪除
    - 不確定 TTL index 被 delete 的機制，可能是 60 seconds interval 或 inset document 時會 trigger mondo 檢查並刪除超過 TTL 的 document
    - 只能在單一 date field 上運作，不支援 compound

### Query Diagnosis

#### Explain
- 透過 explain 我們可以看到 mongo 怎麼選 plan
![](https://i.imgur.com/kUwZsZL.png)

#### Efficient Queries
- 幾個指標
    - Millisecond Process Time：總共花多少時間
    - key examined: 去 index 中查訊的 index key 數量
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
        - 如果我們要查找的資訊在 index 中就有儲存，則不需要回到 document 中查訊！可以直接透過 IXSCAN 查找而省略 FETCH 就直接返回 document
        - 如針對以下資料做 `{name: 1}` 的 index
        - [{name: "Max", age: 29}, {name: "suki", age: 24}]
        - 則 `db.users.find({name: "Max"}, {name: 1, _id: 0})` 就是 cover query，因為他只需要 return name 而不需要 return _id 或其他資訊，所以透過 index 本身就可以回傳 document，省去了 FETCH 的階段
        - 超級高效率
![](https://i.imgur.com/rAzdHQX.png)

### Chossing plan

- mondo 怎麼選擇 plan 呢
- 假設我們有 name 跟 age_name 的 index
- 在搜尋 {name, age} 時，mongo 會先列出所有可用的 index
    - 有兩個 approach： name / age_name index
    - 針對該 query，mongo 會先用小量的 dataSet 讓每個 approach 都跑跑看，看誰勝出（如誰先最快找到 100 個 document），這個 approach 就是 winning plan
    - 之後再用這個 winning plan 跑整個 collection

- cache winning plan
    - 如果每個 qeury 都要先找出 winning plan 會很耗時
    - 因此 mongo 會 cache winning plan，當有相同的 query 時就直接用這個 winning plan


![](https://i.imgur.com/331AyN3.png)

- expire winning plan
    - 某些情境下 winning plan 的 cache 會過期

![](https://i.imgur.com/ErOgLrH.png)


### MultiKey Index
- 針對 array 中的 element 做 index
    - index 的數量會很大，假如一個 document 的 array 中有 4 個 element，有 1000 個 document，那 index 就會有 4000 個 value

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