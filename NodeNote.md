Node Essential
===
###### tags: `Node`

- Node 是**事件驅動、非阻塞 I/O (異步 I/O)**
    - browser 透過 event driven 來實現介面上的交互
    - Node 透過 event driven 來服務 I/O
- Node 透過 V8 引擎，讓 JS 可以離開 browser 端
    - 層級高 > 低：JS > V8 > 中間層（libuv）> 底層 (網卡、硬碟..)

# 同步與非同步（異步）
- 同步 (Synchronous) 與 非同步 (Asynchronous)
- 同步
    - 先完成 A 才能做 B、C、D ...
    - 處理事件的流程會被「卡住」
- 非同步
    - 不用等待 A 做完才做 B、C，而是這三個事情可以同時發送出去
    - 處理事件的流程不會被「卡住」
> Synchronous 被翻譯成「同步」，光看字面上就可能把它想成是「所有動作同時進行」，但事實上剛好相反。
> 

# Data Type
- 共六種 Type （ES6 後加為七種）
    - 基本型別 (Primitives)
        - **string、number、boolean、null、undefined**
    - 物件型別 (Object) 
        - 上述之外的，都歸類在 **Object**
        - 包含 Object, function, array, 正規表達式 等等
        - Number, String, Bool 看起來像 Object 實質不是，因為他們擁有方法(property)，但他們是**不可變的**
    - ES6 加上一種新的 type: [Symbol](http://es6.ruanyifeng.com/#docs/symbol)
- typeof 可以用來確定型別
    - 共有 'number', 'string', 'boolean', 'object', 'function', 'undefined' 六個值 （或多一個 Symbol）
    - 注意：typeof 會回傳一個 **String**
    - 但是有一些例外，包含
        - typeof Null = 'object' 而不是 null
        - typeof some_function = 'function' 而不是 object
    - ![](https://i.imgur.com/nGvR3DO.png)

## Number
- JavaScript 只有一種數值的型別，就是 number，不管整數或帶有小數點的數字都是
- 此外還有三個特殊的存在：Infinity (無限大) 、 -Infinity (負無限大) ，以及 NaN (不是數值，Not a Number)
- NaN
    - 0 / 0 結果會得到 NaN
    - `typeof NaN // Number`
    - NaN 與任何數字作數學運算，結果都是 NaN。 
        - 也就是說， NaN 並不等於任何的數字，**甚至是自己。**
        - `NaN === NaN;    // false`
        - 所以一定要透過 `isNaN(value)` 才能確定一個值是否為 NaN
```javascript=
isNaN(NaN);       // true

isNaN(123);       // false
isNaN("123");     // false, 因為字串 "123" 可以透過隱含的 Number() 轉型成數字

isNaN("NaN");     // true, 因為字串 "NaN" 無法轉成數字
```
## Bool
- 下列值為 false
    - false
    - null
    - undefined
    - '' (空字串)
    - 0
    - NaN
- 其餘值都是 true
    - 例如 'false' (字串) 及所有 object (如空 object)
## Null and Undefined
- undefined 代表的是「(此變數) 還沒有給值，所以不知道是什麼」
    - undefined 類型也只有一種值，就是 undefined。
- null 代表的是「(此變數可能曾經有值，可能沒有值) 現在沒有值」
    - null 型別只有一種值，就是 null 
```javascript=
var a;         // undefined, 尚未給值，未定義
var b = null;  // null, 明確代表此變數沒有值

Number( null );         // 0
Number( undefined );    // NaN
```
- typeof: `typeof null;  // 'object'`
    - 其實這只是一個 Bug !
    - 但如果修掉會讓很多 code crack
## Object
- 所有基本型別 (Primitives) 以外的值都是物件
- 包含 Array, function，正規表達式 等都是物件
- Object 都是用**引用** (reference)
    - 也就是宣告時 `const obj = {key: value}` obj 中的值只是 **指向實體 object 的 pointer**
    - 當使用 `const obj2 = obj` 時，只是將 obj2 也指向 obj 的實體，**永遠不會被複製**
- 一個物件可以是個零至多種**屬性**的集合
    - 一種屬性 = 一組 key + vale
    - value 可以是任何型態，也可以是另一個 object、函數...等
- 物件宣告
```javascript=
const obj = new Object(); // 等同於下者
const obj = { // 物件實字 (Object literal)
 // some key:value pair
}

// 要是想刪除屬性，則是透過 delete 關鍵字來刪除
delete obj.some_key;
```

- 判斷屬性是否存在
    - 當我們試著去存取物件中不存在的屬性，此時會回傳 undefined
        - 儘管可以透過檢查該屬性是不是 undefined 來判斷是否存在，但該方法不優雅也有可能出錯（例如某個 value 真的是 undefined）
    - `in`：in 運算子則會繼續往物件原型鏈上檢查
    - `hasOwnProperty()`: 不會往上檢查物件的原型鏈 (prototype chain)，只會檢查物件本身是否存在這個屬性

- loop through
    - for (變數 in obj)
        - for...in 迴圈只迭代**可列舉屬性**。由內建建構式(如：Array、Object) 製造的物件，從 Object.prototype 和 String.prototype 繼承了不可列舉屬性，如： String的indexOf 方法，或 Object的 toString 方法。 
        - 迴圈將**迭代全部可列舉屬性**，包括了物件自身的和物件**繼承自它的建構式之原型**的可列舉屬性。(原型鏈上較接近物件的屬性覆蓋原型的屬性)
    - 較佳的方法
        - Object.keys：creates an array that contains the properties of an object. 
        - Object.values： creates an array that contains the values of every property in an object
        - **Object.entries**：creates an array of arrays. Each inner array has two item. The first item is the property; the second item is the value.
        - `Object.entries(myObj).forEach([key, val] => console.log(key, val));`

- Object 中的 key 
    - 如果是合法的 JS 名稱，則不需要用括號包住 key
        - 如 `first_name`
    - 如果是不合法的 JS 名稱，則需要用括號包住 key
        - 如 `first-name`
- 取值
    - 如果可以就用 `.` 取值，比較易讀
        - 合法的 key，非變數 key 等
    - 再來使用 `[]` 取值
    - 可以善用 `||` 來給默認值
        - 如 `const status = flight.status || 'unknown'`
    - 如果要深入一層，但不確定上一層是否存在
        - 例如 `flight.status` 是 `undefined`
        - 當我們直接取 `flight.status.now` 就會直接 throw TypeError
        - 所以可以透過 `flight.status && flight.status.now` 來避免錯誤
- 原型 Prototype
    - 當我們對 obj 取值時，如果該 obj 沒有這個屬性，就會依照該 obj 的原型對象尋找，一路往上查找到終點 Object.prototype
    - 如果一路上都沒有查找到該屬性，就會回傳 `undefined`
    - 當我們添加一個新的屬性到 prototype 中，則所有基於該 prototype 創造的 obj 也會 **動態的** 獲得該屬性
    - 父 (prototype) 會影響子，但子的新增刪除不會影響父
    - 刪除子的屬性，可能會讓父的屬性顯露出來 （因為會往上查找）
```javascript=
// 透過 in 檢查屬性
console.log( 'name' in obj );     // true
console.log( 'value' in obj );    // false

// 透過 hasOwnProperty() 方法檢查
obj.hasOwnProperty('name');       // true
obj.hasOwnProperty('value');      // false

// 差別
obj.hasOwnProperty('hasOwnProperty');    // false
'hasOwnProperty' in obj;                 // true


// loop through 
// 不推薦的方式：使用 for...in 來循環 object 中的 property，則會獲得該 object 的對象及可列舉的 prototype 的對象
for (myvar in obj) {
    if (obj.hasOwnProperty(myvar)) {
        // do something
    }
}

// 推薦的方式：使用 Object.entries
const fruits = { apple: 28, orange: 17, pear: 54 }
const entries = Object.entries(fruits) // 得到一個 array
console.log(entries)
// [
//   [apple, 28],
//   [orange, 17],
//   [pear, 54]
// ]

for (const [fruit, count] of entries) {
  console.log(`There are ${count} ${fruit}s`)
}

// 上述等於
Object.entries(fruits).forEach([fruit, count] => console.log(key, val));

```

### Array
- 陣列可以看作是一種特別的 Object
- Array 宣告
```javascript=
const arr = new Array(); // 等同於下者
const arr = [ // 陣列實字 (Array literal)：
 // some value
]

// arr.length
var a = ["apple", "boy", "cat"];
a.length;         // 3

a.length = 1;
console.log(a);   // ["apple"]

a.length = 3;
console.log(a);   // ["apple", undefined, undefined]

// 指定索引元素時也不一定要連續指定
var array = ['a', 'b', 'c'];
array.length;         // 3

array[7] = 'z';
console.log(array);   // ["a", "b", "c", undefined, undefined, undefined, undefined, "z"]

// isArray()
Array.isArray([]);            // true
Array.isArray([1]);           // true
Array.isArray(new Array());   // true

Array.isArray();              // false
Array.isArray({});            // false
Array.isArray(null);          // false
Array.isArray(undefined);     // false

// loop through
some_arr.forEach(x => console.log(x));
```
- length
    - 透過 some_arr.length 來取得，是該 object 的一個屬性
    - 而且 length 這個屬性的值是**可以被覆寫**的。

- 新增 & 刪除
    - push: 從末端新增
    - pop: 從末端刪除
    - **unshift: 從首端新增**
    - shift: 從首端刪除
- typeof
    - `typeof some_array; // 'object'`
    - 所以要透過 `isArray()` 方法才知道是不是 array
        - isArray 是屬於 Array 這個 type 底下的 static function
        - `Array.isArray(array_name)`
- for...in
    - 當我們在處理 Array 及 Object 時，為了避免呼叫出不必要的屬性（屬於其 Prototype 的屬性），應該儘可能**不要使用 for...in 的用法來處理迴圈**。
    - 建議使用 **forEach**
    - 建議可以使用一般的 for 迴圈來輸出陣列就可以了
### function
- function 也可以看作是一種特別的 Object
- typeof
    - `typeof window.alert;  // 'function'` 而不是 object
    - 雖然 typeof function(){ } 回傳的是 "function"，但實際上仍然是屬於「物件」的型別。
## Note

- 確認 NaN 一定要用函數 isNaN(value)，不能用等號去比較。isNaN 會把字串嘗試轉換成數值，如 isNaN("123") 是 true
 
- typeof value 回傳的是字串，typeof 不需要用括號包住
 
- 需要特別注意以下：
    - typeof null 是 'object'
    - typeof some_function 是 'function'
 
- 檢查 object 是否有某個屬性時，通常是使用 hasOwnProperty()，該方式會檢查**物件本身**是否存在這個屬性
 
- 要確定一個 object 是否為 array，要使用 isArray(value)
 
- 當我們在處理 Array 及 Object 時，為了避免呼叫出不必要的屬性（屬於其 Prototype 的屬性），應該儘可能**不要使用 for...in 的用法來處理迴圈**。
    - Object 可以用 `Object.entries` 來 loop through
    - `Object.entries(myObj).forEach([key, val] => console.log(key, val));`
    - 如果處理 Object 要用 for..in 的話，在裡面要記得再加上一層 hasOwnProperty 的檢查，以免不小心輸出 prototype 的屬性
    - Array 可以用 `forEach` 來 loop through

- Object 取值
    - 可以善用 `||` 來給默認值
        - 如 `const status = flight.status || 'unknown'`

# Pass by value
- 基本型別 (非 Object 類)
    - 創造一個新的變數，會直接存放`值`
    - 如 `const x = 10, y = 'test';`
    - 當我們判斷這兩個變數是否相等，看的是裡面的內容，也就是「值」
- 物件型別（Object）
    - 創造一個新的變數，會在 memory 中 allocate 一塊空間存放該 Object （稱為實體），該變數會指向該`實體`，即該變數會存放`實體的地址`
    - 因此若兩個 object 指向的實體不同，儘管兩個實體值相同，在做比較時也一定不會相等（地址不同）

```javascript=
var a = 10;
var b = 10;
a === b // true

const obj1 = { value: 10 };
const obj2 = { value: 10 };
obj1 === obj2 // false, 指向不同 instance

obj1 = obj2
obj1 === obj2 // true, 指向相同 instance
```


- 函數呼叫時
    - **pass by value** 
    - 不論哪種型別，都是會將該變數中**存放的內容，複製一份傳入 function 中**
        - 基本型別會傳入存放的`值`
        - Object 會傳入`實體的地址`
    - 若對傳入的值進行**更改**或**重新賦值**
        - 基本型別
            - 完全不會影響到原本的變數，影響到的只是傳入的那一份 copy
        - Object
            - 如果是對其中的**屬性更改**，會影響到原本的變數，因為內部變數指向同一個 instance
            - 如果是重新賦值，則完全不影響到外部變數，因為只會讓內部變數指向一個新的 instance，但外部變數仍然指向原本的 instance
    - 因為這種方式跟傳統的 pass by value, pass by reference 有點不同，因此也可以稱為 pass by sharing。但其實就是一種 pass by value 的實現

```javascript=
// 基本型態
var num = 10;

function changeValue(x) {
    x = 100;
}

changeValue(num);
console.log(num); // 仍是 10


// Object (包含 array, function 等)
// 重新賦值
var obj = { value: 10 };

function reAssignObj(obj) {
  obj = { value: 100 }; // 賦值另一個實體
}

reAssignObj(obj);
console.log(obj);   // 此時 obj 仍是 { value: 10 }


// 更改屬性

function changeObj(obj) {
  obj.value = 100; // 僅更新 obj.value，並未重新賦值
}

changeObj(obj);
console.log(obj);   // 此時 obj 變成 { value: 100 }
```

# 運算
## 加號
- 特殊型態相加
    - NaN 運算
        - 只要有其中一個是 NaN，那麼結果就必定是 NaN
    - 字串
        - 當加號 + 兩側的其中一方是字串的情況下，加號 + 會將兩者都視為「字串」連接在一起。
        - 也就是說，其中一方是字串，另一端會被「自動轉型」為字串後，連接在一起。
```javascript=
Infinity + Infinity      // Infinity
-Infinity + -Infinity    // -Infinity
-Infinity + Infinity     // NaN

10 + NaN            // NaN
Infinity + NaN      // NaN
-Infinity + NaN     // NaN

100 + "100"      // "100100"
100 + "ABC"      // "100ABC"
"ABC" + "XYZ"    // "ABCXYZ"

10 + 'abc'          // "10abc"
true + 'abc'        // "trueabc"

100 + {}            // "100[object Object]"

// 當數字與 undefined 相加時，undefined 會被嘗試轉成數字，也就是 NaN
123 + undefined     // NaN
"abc" + undefined   // "abcundefined"

// 當數字與 null 相加時，null 會被嘗試轉成數字，也就是 0
123 + null          // 123
"123" + null        // "123null"
```

## 減號
```javascript=
Infinity - Infinity      // NaN
-Infinity - -Infinity    // NaN

-Infinity - Infinity     // -Infinity
Infinity - -Infinity     // Infinity

100 - "50"        // 50
100 - "abc"       // NaN

// false 經過 Number() 轉型成數字後，會變成 0
100 - false       // 100
// true 經過 Number() 轉型成數字後，會變成 1
100 - true        // 99

100 - undefined   // NaN
100 - null        // 100

```

## 除法
- JS 中 Number 沒有分 int 跟 float
    - 2 跟 2.0 是一樣的
    - **如果整數除以整數（沒有整除），得到的就會是小數，而不是整數！！！**

## ++ 與 --
```javascript=
var a = 10;
var b = 10;

console.log(a++);       // 10
console.log(++b);       // 11

console.log(a);         // 11
console.log(b);         // 11
```

## 比值
## 等號
- 三個等號 `===` **不會替數值做自動轉型**
    - 只會在雙方的數值與型態都相等的狀況下回傳 true
    - **盡量用三個等號！**
- 同理，`!==` 則不會做自動轉型

### 等號之自動轉型的規則
- 如果其中有一個值為「Boolean」的情況下，會將 true 轉型為「數字」的 1， false 則會變成數字的 0
- 如果遇到字串與數字做比較的情況下，則會將字串透過 Number() 嘗試轉型為數字後，再進行比較。
- 如果其中一方是「物件」型別，而另一方是基本型別的情況下，則會先透過物件的 valueOf() 方法取得對應的基本型別的值，再進行比較。
- 其他
    - NaN 不等於 NaN，不管是兩個等號或三個等號都一樣。
    - 當兩個物件進行比較時，要看兩者是否指向同一個「實體」，只有在指向同一個「實體」時才會回傳 true。

## 大於、小於之自動轉型的規則
- 兩者都是數字，則單純就其字面大小比較。
- 如果其中一個是數字，而另一個不是，則會嘗試在背後將另一個先轉為數字後再比較。
- 如果兩者都是字串，則會依照字母的順序 (standard lexicographical ordering) 來進行比較。 [註1]
- 如果其中一者是 boolean 的情況，則會把 true 看成 1， false 看成 0 再進行比較。
- 如果是物件的情況下，則是會先透過物件的 valueOf() 方法先求得對應的數值，若物件沒有 valueOf() 方法的話，則會透過 toString() 轉型再進行比較。




# What's "this" in JavaScript?

- 這個 function 的呼叫，是透過 new 進行的嗎？ 如果是，那 this 就是被建構出來的物件。
- 這個 function 是以 .call() 或 .apply() 的方式呼叫的嗎？ 或是 function 透過 .bind() 指定？ 如果是，那 this 就是被指定的物件。
- 這個 function 被呼叫時，是否存在於某個物件？ 如果是，那 this 就是那個物件。
- 如果沒有滿足以上條件，則此 function 裡的 this 就一定是全域物件，在嚴格模式下則是 undefined。
