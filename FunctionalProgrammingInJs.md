# Functional Programming in JS
###### tags: `functional programming`

## 定義
![](https://i.imgur.com/VpeVOdQ.png)

## Pure function and Side effect
![](https://i.imgur.com/0LbcSvh.png)
![](https://i.imgur.com/CrEfeYG.png)

- 可以發現，包含 throw exception 跟 logging /printing 都算是 side effect
- 在程式碼中，**一定會有 side effect 的存在**，但是我們應該要盡力地減少
- 減少 side effect 的好處，是程式的執行更加直觀，不用擔心其他函數會不會更改某個變數
- 在 funcional programming 中，會**集中管理有 side effect 的函數**，保持其他部分為 pure
    - 如此可以更加容易測試程式的正確性

## Shared state
- State 狀態（變數裡面儲存的就是狀態）
![](https://i.imgur.com/fMkWXut.png)
- JS 中的 state
![](https://i.imgur.com/TkmgGw8.png)

- Shared state
    - state 存在於 global / closure scope 中
    - 或是 object 的 property 在 scope 中被傳遞（因為 object 是 pass by reference）
    - 壞處是，我們需要知道使用 shared state 的每個 function 實作及順序，才能知道現在 state 長什麼樣子
    - **FP 則是 pass state through function, 而不是 shared state between function**
        
![](https://i.imgur.com/6B1FV5G.jpg)

## Avoid mutation
- 一般的變數使用 const 來避免被修改
    - 對於 object，可以用 `Object.freeze(obj/arr)` 來避免 obj 被事後修改，就像是 const 一樣，被修改時會噴 error
- object 類變數（如 array, obj），則**透過 clone 的方式來避免修改**
    - 因為 js 是 pass by reference
    - 方ㄧ（有問題）：`Object.assign()`，可以用來結合 obj 或 clone obj
        - `Object.assign({}, obj)`
        - 問題是**無法 `deep copy`**，只對一層 obj 有用
    - 方二（使用這個）：`JSON.parse(JSON.stringify(obj))` 來 deep clone obj，**對於 array 也可以使用**
- 糟糕的方法，如 arr.sort 會直接更改原本的 arr，應該要避免使用

## map, reduce/reduceRight, filter
- 是 JS 內建的 HOF (high order function)
    - 參數是一個 function
    - array 中的每個 element 都會被傳進這個 function 
    - map, reduce, filter 都不會修改到 array
- **應該常使用此類的 function，來避免 mutation array**


## About Function in JS
### First Class Function
- 函數在 JS 中是一等公民，其地位就跟 number 一樣
![](https://i.imgur.com/kgaNtAG.png)
- 例如：
    - function 可以被存在變數中
    - function 可以被放在 array 中
    - function 可以被放在 object 的 property 中
    - function 可以在創造時就使用，即為 IFFE
        - `console.log( 28 + (function(){return 10}()) ) // 38`
    - function 可以被當作參數 pass 進另一個 function 中
    - functinon 可以被當作另一個 function 的 return 值

### Higher Order Function
![](https://i.imgur.com/N6J5lFe.png)
- Higher Order function 其實就是指：
    - function 被當作參數 pass 進另一個 function 中，或被當作另一個 function 的 return 值
- First Class Function 是指 function 在 JS 中如同 number 被對待
- Higher Order Function 在講我們如何使用 function（因為 first class function 才能實現）
- Higher Order function 是 functional programming 的重要概念

- 常見的 HOF
    - callback
    - map/reduce/filter 等

### Closure
![](https://i.imgur.com/nYZkah1.png)
- 常見的例子
    - Callback function 在執行時還能讀到其 parent function 的變數
        - 下面 funct2 是 setTimeout 的 callback function
        - 當 funct2 真正被執行時，他的 parent function (funct) 已經執行完畢了
        - 但 funct2 還是能 access 到其 parent function 中的變數（a, b）
    ```javascript=
    const funct = function () {
        const a = 2, b = 3;

        const funct2 = function () {
            console.log(a+b)
        }

        setTimeout(funct2, 1000) // 一秒後 log 出 5
    }
    ```
    - Module Pattern
        - 在一個 IFFE scope 中所有變數、function。在最後只 return 出要 public 出去的方法
        - 這些 public 出去的方法，依然能夠 access 到原本定義域中的 private 變數/function 


## Function Composition
### Function in Functional programming
- 跟傳統 procedure function 不同
- Functional programming 中 每一個 function 都像是小積木
    - 是 pure 的
    - 可以方便 reuse
    - 可以透過 composition 來組合成大功能的 function
    - ![](https://i.imgur.com/buEU5ZS.png)

- ![](https://i.imgur.com/rvt0RYH.png)
- 上面的圖在程式中會寫成如下，但這樣非常難讀，因此有其他工具可以使用
```javascript=
check(splits(trim(string)))
```

![](https://i.imgur.com/KtKfYiE.png)

### Pipe / Compose
- 是用來組合 function 的工具
- Pipe 是**由左到右**，即為 flow
- Compose 是 **由右到左**，即為 flowRight

#### Compose
```javascript=
const compose = function(...fns) { // fns 是一個 array
    return function(x) {
        return fns.reduceRight(function (v, f){
            return f(v)
        }, x)
    }
}

```
- 假如 compose(a, b, c) ，就會 return 一個 
``` 
funcion(x){
    return a(b(c(x)))
}
```

#### Pipe
- 只是順序反過來
- 上面的 code，把 reduceRight 變成 reduce 就會是 pipe 了

### Arity
- Arity 指一個 functino 所需要的**參數個數**
- 要 compose function 的條件是，**上一個 function 的輸出是下一個 function 的輸入**，因此每個 function 的 arity 都是 1
- 如果一個 function 有兩個輸入怎麼辦？
    - 透過 partial application 跟 curring，可以把 arity 大於 1 個 function ，轉換成 arity 為 1 個 function

### partial application

- 概念是先傳遞一個參數給 function，並拿回一個可以接收別的參數的 function
- JS 中可以用 `bind` 來實作
- bind 會回傳一個新的 function (object)
- bind 的第一個參數是 `this`, 第二個參數是 `argument`
- 我們只使用 bind 的第二個參數
- 例如原先有一個接收 (arr, name) 的 function，可以用 bind 先把 arr 參數寫死
- js 中用 bind 實現 partial application 的方法
```javascript=

const users = [{name: "James",score: 30,tries: 1}, {name: "Mary", score: 110,tries: 4}, {name: "Henry",score: 80,tries: 3}];

//Modifies Data
var storeUser = function(arr, user) {
    return arr.map(function(val) {
        if (val.name.toLowerCase() === user.name.toLowerCase()) {
            return user;
        } else {
            return val;
        }
    });
};

//Pure Functions
const cloneObj = function(obj) {
    return JSON.parse(JSON.stringify(obj));
};

var getUser = function(arr, name) {
    return arr.reduce(function(obj, val) {
        if (val.name.toLowerCase() === name.toLowerCase()) {
            return val;
        }
    }, null);
};

var updateScore = function(newAmt, user) {
    if (user) {
        user.score += newAmt;
        return user;
    }
};

var updateTries = function(user) {
    if (user) {
        user.tries++;
        return user;
    }
};

// 用 bind 來 partial apply
// 先寫入 users array
const partGetUser = getUser.bind(null, users);
// 先寫入 30
const partUpdateScore30 = updateScore.bind(null, 30);

/*const usr = getUser(users, "Henry");
const usr1 = updateScore(cloneObj(usr), 30);
const usr2 = updateTries(cloneObj(usr1));
const newArray = storeUser(users, usr2);*/

// 每個 function 都只吃一個參數，return 下一個 function 需要的東西
const updateUser = pipe(
    partGetUser,
    cloneObj,
    partUpdateScore30,
    updateTries);

const newestUser = updateUser("Henry");

```
   
### curry
- 概念跟 partial application 很像
- 使用了 `High Order Function` 跟 `Clousure` 的概念
```javascript=

// 是一個 HOF，因為他 return 一個 function
const curryGreeting = function(greeting) {
    return function(name) {
        // 因為 clousure，所以這個 function 可以 access 到 greeting
        console.log(greeting + " " + name);
    };
};

const welcomeGreet = curryGreeting("Welcome");

welcomeGreet("Steve");
welcomeGreet("Mary");
```

```javascript=

const ffun = function (a, b, c) {
  return a + b + c;
};

const gfun = function (d, e) {
  return d + e;
};

const hfun = function (f, g, h) {
  return f + g + h;
};

/* const curriedF = curry(ffun);
const curriedG = curry(gfun);
const curriedH = curry(hfun);


const newFun = pipe (
    curriedF(a)(b), 傳 c 進來
    curriedG(d), 上一個 function 的結果被當作 e 傳進來
    curriedH(f)(g)); 上一個 function 的結果被當作 h 傳進來
    
*/

// 跟上面一模一樣
const newFun = pipe(
  curry(ffun)(1)(2),
  curry(gfun)(4),
  curry(hfun)(5)(6)
);


const doubleNum = function (num) {
  return num + num;
};

const totalIt = function (n1, n2, n3, n4) {
  return n1 + n2 + n3 + n4;
};

const doArray = function (num1, num2) {
  return [num1, num2];
};

const newFunction = pipe(
  doubleNum,
  curry(totalIt)(3)(2)(1),
  curry(doArray)(50)
);

```

- Curry function 的實作 （這個版本的 curry function 無法接受一次傳入兩個參數）
    - 第一個參數是要被 curry 的 function
    - 第二個參數是 curry function 總共要吃幾的參數，預設就是原本 function 的參數個數
        - fn.length 就是這個 fn 的參數個數
    - curry 回傳一個 IFFE 的 function，傳遞一個空的 array 給 prevArgs
    - 這個 IFFE 回傳一個 function，即為被 curried 過的 function
    - 當傳入參數，且參數還不夠時，就會再 invoke 一次 IFFE function，且把**現有**的 args 傳進去給 IFFE
    - **也就是透過 clousure 的方式，把之前傳進來的參數一直存起來，直到參數都到齊了，就執行 fn(args)！**
    - 例如傳進參數的順序是 a => b => c，一次一個。則最後 args 就會等於 [a, b , c]，且呼叫 fn(a, b, c)
```javascript=
function curry(fn,arity = fn.length) {
    return (function nextCurried(prevArgs){
        return function curried(nextArg){
            var args = [ ...prevArgs, nextArg ];
            if (args.length >= arity) {
                return fn( ...args );
            }
            else {
                return nextCurried( args );
            }
        };
    })( [] ); // IFFE
}
```

- 例如
```javascript=
const addNum = function (num1, num2) {
  return num1 + num2;
};
const curriedAddNum = curry(addNum);
console.log(curriedAddNum.toString());

// 結果為
function curried(nextArg) {
      const args = [...prevArgs, nextArg];
      if (args.length >= arity) {
        return fn(...args);
      }
      return nextCurried(args);
    }
    
```
 - 當傳進第一個參數 a 時，args 是 [a] (因為 prevArgs 初始值是空 arr)
 - 因為 args.length 還沒 >=2 (arity)，所以回傳 nextCurried，且傳進去的參數是現有的 args，該 IFFE 被執行完之後又回傳了一個 function 等待第二個參數被傳入
 - 當傳進第二個參數 b 時，args 是 [a, b]，且因為 args.length 為 2 了，所以執行了 fn(a, b)！

### partial application 跟 curry
- 兩著很像，但概念有點不一樣
- partial application
![](https://i.imgur.com/FuUdfxN.png)
- curry
![](https://i.imgur.com/UCygKZ4.png)

- 不一樣的地方
![](https://i.imgur.com/cgvtUJj.png)


## Imperative Programming vs Declarative Programming
- Imperative Programming
    - 直覺的 coding 方法
    - 用 control flow
    - ![](https://i.imgur.com/CJg6qoP.png)
- Declarative Programming
    - functional programming 的方法
    - ![](https://i.imgur.com/sMM2xuG.png)

- 例如
```javascript=
// Declarative Programming
return fns.reduce((v, f) => f(v), x);

// Imperative Programming
let result;
for (let i = 0; i < fns.length; i++) {
  if (i === 0) {
    result = fns[i](x);
  } else {
  result = fns[i](result);
  }
}
return result;

```

## OOP VS FP
### OOP
```javascript=
// OOP Approach
function UserRecord(id) {
  if (this instanceof UserRecord) {
    this.userId = id;
    this.questions = [];
  } else {
    return new UserRecord(id);
  }
}

const userProto = {
  addQuestion(qID, response, result, weight) {
    this.questions.push({
      qID,
      response,
      result,
      weight,

    });
    this.updateScore();
  },
  get myScore() {
    let score = 0;
    for (let i = 0; i < this.questions.length; i++) {
      const quest = this.questions[i];
      if (quest.result) {
        score += quest.weight;
      }
    }
    return score;
  },
  get possScore() {
    let possible = 0;
    for (let i = 0; i < this.questions.length; i++) {
      possible += this.questions[i].weight;
    }
    return possible;
  },
  updateScore() {
    const userFld = document.getElementById('user');
    const statusFld = document.getElementById('score');
    userFld.innerHTML = this.userId;
    statusFld.innerHTML = `${this.myScore} out of ${this.possScore}`;
  },
};

UserRecord.prototype = userProto;
UserRecord.prototype.constructor = UserRecord;

/*
To test at console:

let user1 = new UserRecord(1);
user1.addQuestion("q1", "answer", true, 1);
user1.addQuestion("q2", "wrong answer", false, 2);
*/

```

### FP
- 在 FP 中，給予每個 function **符合意義的 function name** 是簡單且重要的
    - 因為在 FP 中會有很多個小 function
    - 先把解決問題需要的步驟都拆解成 PURE 的工具 function
        - 有時依然有 IMPURE 的 function，例如要修改 DOM
    - 再集合這些工具 function 來解決原本的問題
```javascript=
//Functional Approach
let userFld = document.getElementById('user');
let statusFld = document.getElementById('score');

const createUser = function(id) {
    return {
        userId: id,
        questions: []
    };
};

const addQuestion = function(qID, response, result, weight, user) {
    const questions = clone(user.questions);
    const newQuestion = {
        qID: qID,
        response: response,
        result: result,
        weight: weight
    };
    return {
        userId: user.userId,
        questions: [...questions, newQuestion]
    };
}

const calcScore = function(user) {
    return user.questions.reduce((tot, quest) => tot + (quest.result ? quest.weight : 0), 0);
}

const calcPossible = function(user) {
    return user.questions.reduce((tot, quest) => tot + quest.weight, 0);
};

const formatResults = (user) => calcScore(user) + " out of " + calcPossible(user);

const getProp = (prop, obj) => obj[prop];


/* IMPURE */
const setDOMelem = (elem, data) => elem.innerHTML = data;
    
const displayResults = pipe(
    formatResults,
    curry(setDOMelem)(statusFld));

const displayUser = pipe(
    curry(getProp)('userId'),
    curry(setDOMelem)(userFld));


const updateScore = function(user, qID, response, result, weight) {
    let usr = addQuestion(qID, response, result, weight, user);
    /* side effects */
    displayResults(usr);
    displayUser(usr);
    return usr;
};

/*
To test at console:

const usr1 = createUser(1);

const usr2 = updateScore(usr1, "q1", "answer", true, 1);
const usr3 = updateScore(usr2, "q2", "wrong answer", false, 2);
*/

```

## Ramda

```javascript=
const users = [{ name: 'James', score: 30, tries: 1 }, { name: 'Mary', score: 110, tries: 4 }, { name: 'Henry', score: 80, tries: 3 }];

const storeUser = (arr, user) => R.map((val) => (R.eqBy(R.toLower, val.name, user.name) ? user : val), arr);

const getUser = function (arr, name) {
  return R.reduce((obj, val) => (R.eqBy(R.toLower, val.name, name) ? R.clone(val) : obj), null, arr);
};

// assoc 會 copy 一個 object 並改變指定的欄位值
const updateScore = (user, newAmt) => R.assoc('score', user.score + newAmt, user);

const updateTries = (user) => R.assoc('tries', user.tries + 1, user);

const getUsersUser = R.curry(getUser)(users);

const getHenry = () => getUsersUser('Henry');

const updateHenry = R.pipe(
  R.curry(updateScore)(getHenry()),
  updateTries,
  R.curry(storeUser)(users)
);
```