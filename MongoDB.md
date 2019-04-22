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

- DB, Collection 都會被隱式的創造
    - 在 insert 第一筆 Document 時會被一起創造
    - `db.<collection>.insertOne({property})`
![](https://i.imgur.com/cHpUngL.png)

> JSON 中的 key-value pairt 中，key 可以不加上引號、而 value 如果是 string，則單引號、雙引號都可以
## MongoDB Ecosystem
![](https://i.imgur.com/EXvartR.jpg)

## Working with MongoDB
![](https://i.imgur.com/IEUcycA.jpg)
![](https://i.imgur.com/vsI7HBX.png)

## Basic command
`show dbs`
`use <db_name>`
`db.<collection>.insertOne({property})`
`db.<collection>.find()` // will return all document in collection
`db.<collection>.find().pretty()` // return a prettier result

## JSON and BSON
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

### Read
- find({找出所有符合條件的 document})
    - `db.collentions.find()` 不帶參數的話會回傳所有 data
    - 可以對於某個 field 下更明確的查找指令，寫在該 field 的 object 中
    - 如 `$gt: greater than`
    - `db.flightData.find({distance: {$gt: 1000}})`
- findOne({找出**第一個**符合條件的 document})

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
> 如果帶上 operator 那效果就跟 updateMany ㄧ樣
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
![](https://i.imgur.com/yZ4XOG8.jpg)
### Create Validation
- 最簡單的是最一開始創造 collection 時就加上 validation
    - 此時要用顯式的創造 collection
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
    - 透過 `db.runCommand()` 來重設 validator 跟 validationAction
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
});

```
### Data types
![](https://i.imgur.com/b8ca1rp.jpg)
- 除了上述之外還有很多其他 advanced 的 datatype
- 可以使用 `new Date()` 跟 `new Timestamp()` 來創造新的 ISODate 或 Timestamp data
- Timestamp 的格式是 Timestamp(stamp, order)
    - stamp 就是 timestamp
    - order 可以確保如果有多個 document 在同一個 stamp 存入，則可以透過 order 來區分之間的順序 

- int 有分 NumberInt (int32) 跟 NumberLong (int64)
- double 也有分 NumberDouble 跟 NumberDecimal 兩種
    - numberDecimal 是 high-precision double value
- 如果沒有指定的話，如 `insertOne({a: 1})` 則會使用 **NumberDouble**!
    - 因為 shell 是 js，且 js 中只有 float/double 的區別、但沒有 int/float 的區別
- text 沒有大小限制，只要不會使該 document 總共超過 16 MB 就好

## Relations
- 對於各種 relations，我們都可以選擇要用 Embedd document 或是 Referenct 的方式來實現
    - 端看該狀況下哪個比較好

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

### Many-To-Many
#### Embedded
- 某些 app 中，many to many 可以用 embedded 紀錄
    - 如比較在意 snapshot 的 app，因此其多對多並不是真正的「多對多」（關聯到某個特定的物品）
    - duplicate 可以被接受，或是需要 duplicate 時
- 例如 product(many) -- customer(many)
    - 可以在 customer 中紀錄一個 order 欄位，並直接紀錄 array of product  來直接紀錄他購買了哪些東西
    - 因為我們在意的是購買當下那個產品的名稱、購買數量、價錢
    - 如果事後這個產品漲價了，該 customer 已經購買的物品價格 **也不會跟著改變**
#### Reference
- Many to Many 大多會選擇使用 Reference
- 如果在 SQL 中， many to many 會需要三張表格來紀錄
- 在 Mongo 中，many to many 透過 referenct 可以用兩張表格紀錄就好
    - 因為其中一個關聯者可以用 array of id 的方式紀錄關係
- 當資訊較常改變，且希望關聯到的資訊都是最新的、相同的那一份時，會使用 reference
- 例如 book(many) -- author(many)
    - 我們希望當作者的資訊改變時，所有書上的作者資訊也都跟著改變 (反向也是)

### 整理
![](https://i.imgur.com/KzADAzx.jpg)


### 使用 Lookup
- 我們可以使用 aggregate 來將有一次查詢到關聯的 data
- 如 books -- authors，假設 book 中有個 field 叫 authors 為一個 array 紀錄關聯的 author id
    - 則可以使用 `db.books.aggregate([{$lookup: {from: "authors", "localField": "authors", foreignField: "_id", as: "creators"}}])`
    - from 是指被關聯的 collection 名稱
    - localField 是關聯到別人的 field 名稱
    - foreignField 是被關聯到的 field 名稱
    - as 是該資料之後要被命名的方式 (原先的 authors fiedl 仍會保持)

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
- `--fork`: run mongodb as a backgroud service
    - 就不用再開兩個 terminal 來操作
    - 因為原本的 terminal 無法再顯示 log 資訊，因此使用 fork 時必須要帶上 `--logpath` 的參數
    - 要關閉 server 的話，就透過 cli 輸入 `use admin` 先切換到 admin DB，再用 `db.shutdownServer()` 來關閉在背景執行的 server

- 使用 config file
    - config file 可以寫在任何地方
    - 在啟動 server 時使用 `-f` 或 `--config` 來設定 config file 的位置

## Shell (cli) option
- `--host`：預設在 localhost
- `--port`
- `--quiet`
- 進入 cli 之後，可以使用 `help` 來查詢相關指令

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
        - default 為 1，可以想像 server 在 memory 中創建好之後，有實體物件後再回覆成功，會拿到 `acknowledge = true` 以及該物件 _id
        - 如果 w 為 0 代表 write 送到 server 後馬上回復成功，所以我們不會拿到被 insert 的物件的 _id，因為該物件還沒有創建好。雖然速度極快但也沒有保障。會拿到 `acknowledge = false` 的回覆。可以用在一些遺失也不重要的資料上，如 log
    - j 為 **是否要寫入 journal**
        - defaut 為 undefined (或 false) ，代表當 server 回報成功給我們時，我們無法確認資料已經被寫入 journal 中。有可能資料沒有被寫進 journal 中，且在要寫入 disk 時發生錯誤，此時我們會誤以為資料已經 write 成功，但實際沒有
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
    - **如果要找到完全相等的 array 結果，則直接用 array 的形式來表達 value**，如 `db.collection.find({someArrayField: [value]})`，就會回傳有完全相等 array 的 document，**且 array 的 element 順序也需要一樣**，否則要用 $all

- `$in`: 尋找某幾個特定的值
    - value 要給一個 array
    - 如 `db.movies.find({runtime: {$in: [42, 50]}})` 會回傳所有 runtime 等於 42 或 50 的 document
- `$nin`: not in，跟 in 相反，找到「不等於某幾個特定值」的 document

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
    - 最有效的還是使用 text index
    - 如果欄位沒有 index，可以用 regex 做字串的 pattern 搜尋，但效率比較差
    - regex 要用 `//` 包住頭尾
    - `db.movies.find({summary: {$regex: /musical/}})` 會返回所有 summary 包含 musical 的 document

- `$expr`: expression, 可以用來比較 document 中的兩個 field，並返回符合條件的
    - **常用**
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

- `$all`: 找到包含所有指定欄位的 array 的 document ，但不在意 array 中的順序
    - `db.moviestarts.find({genre: {$all: ["action", "thriller"]}})`，會找出所有 genre 包含 action, thriller 的 document，順序不影響
    - 若不用 all，則變成找到 array **包含順序完全等於[action, thriller]** 的 document