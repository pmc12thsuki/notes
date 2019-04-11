Go Basic
===
###### tags: `Go`
# Why Go
## 優點
- go 被設計來在多核心 CPU 上運作
    - 不論是單一電腦上的多 CPU 或多電腦之間
    - 可以簡單地達成平行運算
- 優點
    - compile 快速
        - 是 protable 的，可以在不同 os 上 compile
    - 執行效率高
    - 編寫簡單
- 擅長於
    - web services at scale
    - networking (youtube 是 go 寫的)
    - concurrency/parallelism
    - system / automation / command-line tools
    - crypto

## 規則
- **Go 中不能有宣告但未使用的變數**
- **Go 中的 entry point 是 package main 中的 func main**
- Go 中不用加上分號
- Go 是 static language 靜態語言
    - 每個 variable 都只能裝相同 type 的東西
## Go 是 OOP 嗎 （物件導向程式語言）
- yes and no
- go 有 OOP 的概念，但實現方式跟傳統 OOP 不同
-  Go has OOP aspects. But it’s all about TYPE. We create TYPES in Go; user-defined TYPES. We can then have VALUES of that type. We can assign VALUES of a user-defined TYPE to VARIABLES.
-  GO 中有 OOP 的概念
    -  Encapsulation
        -  state ("fields")
        -  behavior ("methods")
        -  exported & unexported; viewable & not viewable
    -  Reusability
        -  inheritance ("embedded types")
    -  Polymorphism
        -  interfaces
    -  Overriding
        -  "promotion"
- 傳統 OOP
    - Classes
        - data structure describing a type of object
        - you can then create "instances"/"objects" from the class / blueprint
        - classes hold both:
            - state / data / fields
            - behavior / methods
        - public / private
    - Inheritance
- GO 跟傳統 OOP 不伊樣的地方
    - you don't create classes, you create a TYPE
    - you don't instantiate, you create a VALUE of a TYPE


# 小常識
## Bash Command
- ls -la 列出文件權限
    - l 代表列出詳細參數
    - a 代表列出所有檔案，包含隱藏檔案
    - 也可用快捷鍵 `ll`
    - ![](https://i.imgur.com/f92HETI.png)
    - ![](https://i.imgur.com/ylNy6nB.png)
    - ![](https://i.imgur.com/tVxxrZu.png)
![](https://i.imgur.com/73GlIzT.png)


- clear
    - 快捷鍵 command + k

- chmod
    - ![](https://i.imgur.com/IixE4HI.png)
![](https://i.imgur.com/bzfO15q.png)
![](https://i.imgur.com/gXWX6Ug.png)

- grep
    - 字串比對
    - 最基本用法
    -  `cat filename | grep string`
        - 把 filename 的內容用 cat 顯示出來
        - 但只要顯示其中包含 string 的行數
    - `ls | grep string`
        - 把當前的檔案用 ls 顯示出來
        - 但只要顯示檔名包含 string 的檔案

## X86-64
- X86 是 32 bit
- X64 是 64 bit
    - 可相容 X86 跟 X64，所也也會寫作 X86-64

![](https://i.imgur.com/AATl3Ek.png)
![](https://i.imgur.com/4EDeEOP.png)

## SHA256 Checksum
- 網路上有很多檔案連結
    - 為了防止壞人偷偷更換檔案連結，導致下載到含有惡意的檔案
    - 因此出現了 checksum
    - 這個 checksum 是將檔案用 sha256 hash function 轉換後的結果
    - 只要檔案中有任一個 byte change， hash function 的結果就會不同
- 如何自我檢測下載檔案
    - 在 command line 中使用
    - 方法ㄧ `shasum -a 256 filename`
    - 方法二 `openssl sha256 filename`
    - 如果跑出來的結果跟官方文件上給的 sha256 值一樣，代表檔案是沒問題的

# GO Command
- go version
- go env
- go help
- go fmt
    - 幫助排版
    - `./…` 會對資料夾底下的檔案都排版
- go run
    - 直接執行golang code
    - `go run <file name>`
    - build + run
    - 不會產生 binary 檔案
    - go run *.go
        - 執行所有 go 檔案
- go build
    - for an executable: 
        - 如果沒有錯誤就產生執行檔於當前目錄
        - builds the file
        - reports errors, if any
        - if there are no errors, it puts an executable into the current folder
        - build 完之後用 `./filename` 來執行
    - for a package:
        - builds the file
        - reports errors, if any
        - throws away binary
- go install
    - for an executable:
        - 如果沒有錯誤則產生執行檔於$GOPATH/bin
    - for a package:
        - compiles the package (builds it)
        - puts the executable in workspace / pkg
        - $GOPATH / pkg
        - makes it an archive file
- go clean
    - 執行後會將build產生的檔案都刪除(install的不會刪)

# Document
- golang.org vs godoc.org 
    - golang.org
        - language
        - standard library
    - godoc.org
        - standard library AND third-party packages

## idiomatic Go code
- When you write "idiomatic Go code" you are writing Go code which conforms to best practices for writing Go code.
- [example](https://dmitri.shuralyov.com/idiomatic-go)
# Variables, values, & type
- **type comes after the variable name**
- `:=` : declare + assign a local variable
    - 又稱 Short variable declarations
    - `x:=40` 創造一個變數 x 並賦值 40
    - local 的變數，必須要在 function 中使用
- `var`: declare a **package scope** variable
    - `var x = 40`
    - A var statement can be at package or function level.
        - 在 function 外宣告就是 package level
        - 在 function 內宣告就是 function level
    - 只在必要時使用 var，其餘使用 :=
    - 當我們創造一個變數但沒有賦值時，要加上 type
    - 當我們創造一個變數但沒有賦值時，值會自動為 **Zero values** (不像 js 是 undefined)
        - 0 for numeric types,
        - 0.0 for floats
        - false for the boolean type,
        - "" (the empty string) for strings.
        - nil for pointers, functions, interfaces, slices, channels, and maps

```go=
var number int // 定義一個變數 number ，由於沒有賦值，所以要聲明類型，並且會自動獲得 0 value
var number int = 10 // 定義一個變數 number，賦值 10，且聲明類型為 int
var number1, number2, number3 int = 1, 2, 3 // 定義多個變數，有賦值且聲明類型為 int，型態可以寫在最後
var number1, number2, number3 = 1, 2, 3 // 定義多個變數，由於有賦值，所以可以直接忽略聲明
var c, python, java = true, false, "no!"
number1, number2, number3 := 1, 2, 3 // 使用 Short variable declarations，只能在 function 中使用


```
- `const` ：宣告常數
    - 有兩種 const
        - Constants can be character, string, boolean, or numeric values. 
        - Constants **cannot be declared using the := syntax.**
        - type const ，如`const a = 42`
        - untype const，如 `const a int = 42`
- `iota` ：自增長**常數**
    - 當用同一個 const 宣告時，就會自增長
    - 如果用兩個區塊的 const 宣告，則各自都會從 0 開始增長
        - It is reset to 0 whenever the reserved word const appears in the source
```go=
const (
    a = iota // 0
    b       // 1
    c     // 2
    d     // 3
)

const (
    a = iota + 2020 // 2020
    b // 2021
    c // 2022
    d // 2023
)
```
- `=`：賦值個**已經創造**的變數
- bit shifting
    - `>>`
        - `>>1` 是除以二 （因為在二進位中往右位移）
        - `>>2` 是除以四...以此類推
        - 如果除不進就會捨去
    - `<<`
        - `<<1` 是乘以二 （因為在二進位中往左位移）
        - `<<2` 是乘以四...以此類推

```go=
x := 5
fmt.Printf("%d\t%b\n", x, x) // 5 101

y := x << 2
fmt.Printf("%d\t%b", y, y) // 20 10100
```

```go=
package main

import "fmt"

const (
	_  = iota // iota = 0, throw away this const
	kb = 1 << (iota * 10) // iota =1, 是 1 往左位移 10 位，是 1* 2^10^ = 1024
	mb = 1 << (iota * 10)// iota =2, 1*2^20^
	gb = 1 << (iota * 10)// iota =3, 1*2^30^
)

func main() {
	fmt.Printf("%d\t\t\t%b\n", kb, kb) //1024
	fmt.Printf("%d\t\t\t%b\n", mb, mb) //1024*1024
	fmt.Printf("%d\t\t%b\n", gb, gb) // 1024*1024*1024
}

```
- string
    - 可以使用 "" 或 ``
    - ``：反引号用来创建原生的字符串字面量，这些字符串可能由多行组成(不支持任何转义序列)，原生的字符串字面量多用于书写多行消息、HTML以及正则表达式
    - ""：双引号用来创建可解析的字符串字面量(支持转义，但不能用来引用多行)
    - go语言中**不倾向使用单引号表示字符串**，单引号用于表示Golang的一个特殊类型：rune，类似其他语言的byte但又不完全一样，是指：码点字面量（Unicode code point），不做任何转义的原始内容

- function
    - func (receiver) identifier(parameters) (returns) { code }
    - When two or more consecutive named function parameters share a type, you can omit the type from all but the last. 
    - A function can return any number of results.
    - Named return values
        -  Go's return values may be named. If so, they are treated as variables defined at the top of the function.
        -  These names should be used to document the meaning of the return values.
        -  A return statement without arguments returns the named return values. This is known as a "naked" return. 
```go=
func add(x, y int) int {
	return x + y
}

func swap(x, y string) (string, string) {
	return y, x
}

func split(sum int) (x, y int) { // will return x and y
	x = sum * 4 / 9
	y = sum - x
	return
}
```
## Type
- primitive data types
    - 基本類型
    - Bool
    - Numeric
        - Int 
            - 從 uint8 ~ unit 64 (unsingned)
            - 從 int8 ~ int64
            - 可以只寫 int，其大小可能會是 int32 / int64 ，根據 compile 時的環境而不同 (unit 也是)
            - byte = uint8 (一個 byte = 8 bit)
            - rune = int32 (一個 char 的大小是 1~4 byte，所以是 int32)
        - Float 
            - 預設是 float64
            - 有 float32 跟 float64
            - 不能只寫 float，要寫 float32 或 float64
        - String
            - 是 slice of byte
    - 所有 basic type
```go=
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // alias for uint8

rune // alias for int32
     // represents a Unicode code point

float32 float64

complex64 complex128
```
- composite data types
    - 由 primitive types 組成的，如 array, slice, map, struct

- ...interface{}
    - When you see that a func has a parameter of this type "...interface{}" this is called a "variadic parameter" and it means that the **func can take as many values of that type as you want to pass in.**
    - Every value in Go is also of type "empty interface" which is expressed like this: "interface{}"


# package
## fmt (format)
- 用法跟 C 很像
we look at the difference between these funcs in the “fmt” package
    - group #1 - general **printing to standard out**
        - func Print(a ...interface{}) (n int, err error)
        - func Printf(format string, a ...interface{}) (n int, err error)
        - func Println(a ...interface{}) (n int, err error)t
    - group #2 = printing to a string which **you can assign to a variable**
        - func Sprint(a ...interface{}) string
        - func Sprintf(format string, a ...interface{}) string
        - func Sprintln(a ...interface{}) string
    - group #3 - printing **to a file or a web server’s response**
        - func Fprint(w io.Writer, a ...interface{}) (n int, err error)
        - func Fprintf(w io.Writer, format string, a ...interface{}) (n int, err error)
        - func Fprintln(w io.Writer, a ...interface{}) (n int, err error)

- format printing 的[參數](https://golang.org/pkg/fmt/)

    - 通用
        - %v 是 default format
        - %T 是 the type of the value
    - Bool
        - %t true or false
    - int
        - %b	base 2
        - %c	the character represented by the corresponding Unicode code point
        - %d	base 10
        - %#x base 16 (hex)
    - float
        - %f	decimal point but no exponent, e.g. 123.456
        - %F	synonym for %f
    - string
        - %s	the uninterpreted bytes of the string or slice
    - pointer
        - %p	

## 自定義 type
- 基於已經有的 type 上再自定義 type
    - 被當作基準的 type 又稱 **underlying type**
```go=
type newInt int // create a new type called newInt, which underlying type is int
```
## Conversion
- 在 go 中，變數轉換型態叫做 **Conversion** 而不叫 casting
- 使用 `T(x)` 來做 conversion，T 是 type，x 是變數
- 一定要顯示的轉型，無法隱式
    - Go assignment between items of different type requires an explicit conversion
```go=
type newInt int // create a new type called newInt, which underlying type is int
var a int
var b newInt

a = int(b) // conversion
```

## 比較符
- GO 中的比較相等用 `==`
    - 只有 JS 中會用到 `===`
## 邏輯
- 使用 `&&`, `||`, `!`

# Control flow
## For
- for loop
    - **GO 中沒有 while, 也沒有 do while**, 直接用 for 取代
    - 三種用法
        - 傳統 counter
        - 當作 while loop
        - 使用 range 來 loop through array, slice, map 等
            - range on arrays and slices provides both the **index and value** for each entry
            - range on map iterates over key/value pairs
            - range can also iterate over just the keys of a map
            - range on strings iterates over Unicode code points. The first value is the starting byte index of the rune and the second the rune itself.
    - 有 `break` 跟 `continue`
```go=
// 1. 傳統 counter
for i := 0; i < 100; i++ {
    // do something
}

// 2. 取代 while
for condition {
    // 會一直執行到 condition 變成 false 為止
}

// 3. 使用 range
kvs := map[string]string{"a": "apple", "b": "banana"} // 宣告 map
for k, v := range kvs { // loop through key, value pairs in map
    fmt.Printf("%s -> %s\n", k, v)
    // b -> banana
    // a -> apple
}
```
## If-else
- if / else
    - if statement 前面還可以加上一個宣告式, 宣告的參數只在該 if/else 中存活 ( 用 `;` 相隔)
        - any variables declared in this statement are available in all branches.
    - **Go 中沒有  ternary if 三元表達式**
```go=
if num := 9; num < 0 {
        fmt.Println(num, "is negative")
    } else if num < 10 {
        fmt.Println(num, "has 1 digit")
    } else {
        fmt.Println(num, "has multiple digits")
    }
```
## Switch
- switch
    - **GO 中的 switch 只會執行第一個成功的 case**
        - Go里面switch默认相当于每个case最后带有break，匹配成功后不会自动向下执行其他case，而是跳出整个switch
        - **只要加上 `fallthrough`，不論是否符合條件都一定會進入下一個 case**
        - 盡量少用 fallthrough
    - 一個 case 中可以有多個條件，用逗號隔開，只要符合其中一個條件就會進入該 case
    - case 中可以擺判斷式
    - 如果 switch 之後沒有東西，就是 if/else 的一種變形，會進入到判斷為 true 的 case
        -  Switch without a condition is the same as switch true.
        -  This construct can be a clean way to write long if-then-else chains. 
    - switch 跟 if、for 一樣， 可以在 switch 後面宣告變數

```go=
switch i {
    case 1:
        fmt.Println("one")
    case 2:
        fmt.Println("two")
    case 3:
        fmt.Println("three")
    default:
        fmt.Println("others")
    }
    
switch time.Now().Weekday() {
    case time.Saturday, time.Sunday: // 多個條件
        fmt.Println("It's the weekend")
    default:
        fmt.Println("It's a weekday")
    }
    
switch { // 跟 if-else 一樣，會進入到判斷為 true 的 case
    case t.Hour() < 12:
        fmt.Println("It's before noon")
    default:
        fmt.Println("It's after noon")
    }

switch {
    case false: // 不會進入
        fmt.Println("The integer was <= 4")
        fallthrough
    case true: // 會進入
        fmt.Println("The integer was <= 5")
        fallthrough
    case false: // 會進入，因為 fallthrough
        fmt.Println("The integer was <= 6")
        fallthrough
    case true: // 會進入，因為 fallthrough
        fmt.Println("The integer was <= 7")
        fallthrough
    case false: // 會進入，因為 fallthrough
        fmt.Println("The integer was <= 8")
    default: // 不會進入，因為上一個分支沒有加上 fallthrough
        fmt.Println("default case")
        // 最後一個分支不能加上 fallthrough
    }
    
switch os := runtime.GOOS; os { // 先宣告變數
	case "darwin":
		fmt.Println("OS X.")
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.\n", os)
	}
```



# composite data types
## Array
- **在 Go 中幾乎不會使用 Array，而是用 slice**
```go=
var x [5]int // 初始化都是 0，初始化時要宣告長度
x[3] = 42
len(x) // 5
```

## Slice
- slice = reference to an array, which will automatically expand its capacity when needed
- 就像 vector 一樣
- slice 跟 array 宣告的差別只在**有沒有指定長度**
- **Slice 中的 type 要是一樣的**
- Slices are **references to arrays**
    - A slice does not store any data, it just describes a section of an underlying array. 
    - Changing the elements of a slice modifies the corresponding elements of its underlying array. 
    - Other slices that share the same underlying array will see those changes
- `[]type` 表示 slice of type
- 也可以像 python 一樣用 `:` 來取得想要的 index 區間
    - `slice[start:end]` 包含 start ，不包含 end
- 初始化時不需要指定長度
- `append`：append(array, elements)
    - 所以只有第一個參數是 a slice of type T，後面都要是 type 為 T 的 element
    - append 是屬於 global 的 function，而不是屬於 slice 的
    - `x = append(x, 4, 5, 6)`
- `...` 可以使用這個符號來展開 slice (跟 JS 一樣)
    - `...` 要放在變數的**後面**
    - `x = append(x, y...)`
- 如何刪除元素：用 append 把前段跟後段的 slice 組合起來
    - `x = append(x[:2], x[4:]...)`
    - 代表將 x 去除 index 2 跟 3 的元素
- A slice has both a **length and a capacity.**
    - `len(array) 跟 cap(array)`
    - The length of a slice is the number of elements it contains. 
    - The capacity of a slice is the number of elements in the underlying array, counting from the first element in the slice. 
- `make`
    - 由於 **slice 是會動態調整大小的 array**，因此若已經知道 slice 大概的大小，就可以透過 make 宣告一個 slice 讓底層的 array 擁有**足夠的 capacity**，如此可以**提高效率**、節省 array 擴張的成本
    - capacity 是底層 array 的大小, 可以透過 cap(var_name) 取得一個 slice 的 capacity
    - size 是目前 slice 中已經有的元素個數
    - `make(type, size, capacity)`
    - `x := make([]int, 10, 100)` ，x 是一個擁有 10 個 0 的 slice，且底層 array 的 capacity 是 100
    - 就算 capacity 比 size 大，要新增超過 size 長度的元素時，還是要使用 `append`
    - 當 size 要超過 capacity 時，slice 的 capacity 就會擴展為兩倍
- The zero value of a slice is **nil**.
    -  A nil slice has a **length and capacity of 0 and has no underlying array.**
- 二維 slice
    - `[][]type{slice_1, slice_2}`
    - `matrix := [][]string{slice_1, slice_2}`
```go=
x := []int {4, 5, 6, 7}
len(x) // 4

for i, v := range x {
    // loop for index i and value v   
}

// slicing
x[0] // 4
x[:] // [4, 5, 6, 7]
x[1:] // 5, 6, 7

// append
x = append(x, 8, 9, 10) // [4, 5, 6, 7, 8, 9, 10]

y := []int{234, 567}

x = append(x, y...) // [4, 5, 6, 7, 8, 9, 10, 234, 567]

// 使用 make 宣告
z := make([]int, 5, 100)
z // [0, 0, 0, 0, 0]
len(z) // 5
cap(z) // 100
```

- slice vs array
```go=
[3]bool{true, true, false} //This is an array literal

[]bool{true, true, false} // And this creates the same array as above, then builds a slice that references it: 
```

- 用`range` 來 loop through
    - When ranging over a slice, two values are returned for each iteration
        - The first is the **index**, 
        - and the second is **a copy of the element at that index.** 
        - 所以回傳的 element 並不是 reference ，直接更改並不會影響原本的值 (跟 JS ㄧ樣)
        - If you only want the index, you can omit the second variable. 
```go=
var pow = []int{1, 2, 4, 8, 16, 32, 64, 128}
for i, v := range pow {
		fmt.Println(i, v)
	}

for i := range pow { // 如果只需要 index，可以直接省略第二的回傳的參數
		pow[i] = 1 << uint(i) // == 2**i
	}
```

## Map
- 宣告
    -  `var m = map[key_type]value_type{ // some key-value pair }`
        -  需要在宣告的同時加上一些 key，否則該 map 的 zero value 是 nil，之後就不能再加上新的 key-value
        - The zero value of a map is **nil**c. 
            - A nil map has no keys, nor can keys be added.
    -  `var m = make(map[key_type]value_type)`
        -  The make function returns a map of the given type, initialized and ready for use. 
        -  可以之後再加上 key-value pair
- **如果查找一個不存在的 key，就會回覆 zero values**
    - 如何確定該物件真的不存在
    - map 其實會回傳兩個參數，第一個是 map 對應到的值，第二個是該 key 是否存在
    - **`v, ok := m[key_value]`** 也稱 **"comma ok" idiom**
    - **常用 `if v, ok := m[key_value]; ok { some statement }` 來檢查 key 是否存在**
 
- 新增：直接指定新的 key, value pair 就好 (跟 JS 一樣)
    - `m[new_key] = new_value`
- 刪除: `delete(map_name, key)`
    - 如果試圖 delete 一個不存在的 key，並不會產生 error
- 訪問：使用 for..range
```go=
m := map[string]int {
"james": 32,
"kelly": 23,
}

m["james"] //32
m["suki"] //0
v, ok := m["suki"]
v // 0
ok // false 代表 suki 這個 key 不存在在 m 之中

// 常用的 code
if v, ok := m["suki"]; ok { // 只有 ok 是 true 才會執行，所以這段不會被執行

}

// 新增
m["suki"] = 24

// 刪除
delete(m, "James")

// loop
for k, v := range m {

}
```

# struct
- 就像一個 object，裡面可以有不同type的 field
- `type struct_name struct `
    - a type of struct_name is a struct
    - 就跟 `var var_name type` 很像，a var of var_name if a type
    - 如 a type of person is a struct, a variable of x is int 
- 如果有多個 field 的 type 是一樣的，可以簡潔的寫在一起
```go=
type person struct {
    // property
    age int
    name string
}

type some_struct struct {
    x, y int
    first_name, last_name string
}
```
- Embedded Struct : struct 中有 struct
    - 下面的 secretAgent 中，person 是另一個 struct
    - 在宣告 secretAgent 時，**不需要給 person field name (不像 ltk ㄧ樣)**，稱為 anonymous Field 
    - 但在實際宣告 secretAgent 的實例時, person 的 filed name 就是 person
        - person 這個 filed name 就是 person，且值是一個為 person 的 sturct
    - embedded struct 中，內層的 struct 的 filed name 會被**提升**
        - **如下例中，雖然實際上是 `sa1.person.first` ，但可以用 `sa1.first` 來取代**
        - **然而，如果 secretAgent 本身有一個 `first` 的欄位，且 secretAgent 中的 person 也有一個 first 的欄位，則 `sa1.person.first` 跟 `sa1.first` 所得到的值就不相同**
```go=
type person struct {
	first string
	last  string
	age   int
}

type secretAgent struct {
	person // 不需要給 field name
	ltk   bool
}

sa1 := secretAgent{
    person: person{ // person 這個 filed name 就是 person，且值是一個為 person 的 sturct
        first: "James",
        last:  "Bond",
        age:   32,
    },
    ltk: true,
}

fmt.Println(sa1.first, sa1.last, sa1.age, sa1.ltk)
// person 中的 field 可以直接透過 sa1.field_name 來取得
```

- Anonymous structs
    - 如果 struct 只使用一次，可以使用 anonymous struct 簡寫
    - 宣告後馬上賦值，無法重複使用
```go=

func main() {
	p1 := struct { // 宣告一個 anonymous struct 的結構
        first string
        last  string
        age   int
    }{             // 馬上賦值
        first: "James",
        last:  "Bond",
        age:   32,
    }

	fmt.Println(p1)
}

```


# Functions
- `func (receiver) identifier(parameters) (returns) { code }`
    - we define our func with parameters (if any)
    - we call our func and pass in arguments (in any)

## PASS BY VALUE
- **everything in GO is PASS BY VALUE**

- 因此一般的基本 type，GO 都是 pass by value
- 對於較複雜的 type
    - struct：如果直接 pass struct，那就會 copy 一整個 struct 到 function 中，因此無法修改 struct 中的值。如果需要修改，則需要 **pass by struct's pointer**
    - map 跟 channel：本身就是一個 pointer 指向一個 runtime 的 structure，所以我們在 function 中傳遞 map 跟 channel 時，是把 **這個 pointer** pass by value，因此還可以 access 並修改到 memory 中的 map/channel
    - slice：slice 本身只是一個 header （如下），其中包含一個指向 underlying array 的 pointer，所以在 function 中傳遞 slice 時，會把 header 複製一份（pass by value），因此在 function 中仍然可以透過 header 中的 pointer 修改到同一個 underlying array。
        - a slice value is a header, describing a contiguous section of a backing array, and a slice value only contains a pointer to the array where the elements are actually stored. The slice value does not include its elements (unlike arrays).
        - So when you pass a slice to a function, a copy will be made from this header, including the pointer, which will point to the same backing array. Modifying the elements of the slice implies modifying the elements of the backing array, and so all slices which share the same backing array will "observe" the change.
```go=
type SliceHeader struct {
    Data uintptr
    Len  int
    Cap  int
}
```
- function returns: 
    - 也是 pass by value
    - The return parameters of the function are passed by value back to the calling function when the function returns.
## Variadic parameter
- You can create a func which takes an unlimited number of arguments. 
    - 也可以傳入 0 個 argument
        - 會得到一個 len 跟 cap 都是 0 的 nil slice
    - 所有傳進去的 argument 會變成一個 slice
- 如果使用 variadic parameter，必須要放在 **最後一個 parameter**
    - 如 `func sum(s striing, x ...int)` ，parameter 順序不能相反
    - 這樣才分得出來哪些 augumeter 是屬於誰
- When you do this, this is known as a “variadic parameter.”
- When use the lexical element operator “...T” to signify a variadic parameter (there “T” represents some type).
```go=
func main() {
	x := sum(2, 3, 4, 5, 6, 7, 8, 9)
	fmt.Println("The total is", x)
}

func sum(x ...int) int {
    // x 是 slice of int
	sum := 0
	for i, v := range x {
		sum += v
	}
	return sum
}
```
- 所以使用 `...` 有兩種方式
    - `...type` 是指有 unlimited number of type argument
    - `type...` 是指把一個 slice of type **展開** 成很多個單一的 slice

- 上述的 code 可以改寫成下面
    - **且 xi 跟 x 有 same underlying array**
    - **所以如果修改 x ，也會修改到 xi**
```go=
func main() {
	xi := []int{2, 3, 4, 5, 6, 7, 8, 9}
    // xi... 代表把 slice of int 展開，所以成為多個單一的 int
	x := sum(xi...)
	fmt.Println("The total is", x)
}

func sum(x ...int) int {
    // ...int 代表很多個 int ，x 會把這些聚集成為 slice of int
	sum := 0
	for i, v := range x {
		sum += v
	}
	return sum
}
```
## Defer
- 可以暫停某個函數的執行
    - 直到最接近的 return 發生、panic 發生、或碰到函數的最末端
- 常被用來做資源的清理（如檔案讀寫）
- defer 函數的執行是在 **发生在 return 之后**
- A "defer" statement invokes a function whose execution is deferred to the moment the surrounding function returns, either because 
    - **the surrounding function executed a return statement**
    - **reached the end of its function body,**
    - **or because the corresponding goroutine is panicking.**

```go=
func FileProcess() error {
    //開啟檔案
    f, err := os.Create("/tmp/dat2")
    check(err)
    
    //在這裡呼叫 defer f.Close()，如果有參數會這時候讀入
    defer f.Close()
    
    if CASE1 {
        Do something        
        //這時候會執行 f.Close()，因為要 return 了
        return  errors.New("Error Case2")
    } else if CASE2 {
        Do something
        //這時候會執行 f.Close()，因為要 return 了
        return errors.New("Error Case2")
    } 
    // 就算之後要增加新的case，也不用擔心要補 f.Close()
    
    //這時候會執行 f.Close()，因為要 return 了
    return nil
}
```

- defer
    - A defer statement defers the execution of a function until the surrounding function returns. 
    - The deferred call's arguments are evaluated immediately, but the function call is not executed until the surrounding function returns. 
    - Deferred function calls are **pushed onto a stack**. When a function returns, its deferred calls are executed in last-in-first-out order. 

```go=
func main() {
	defer fmt.Println("world")
	fmt.Println("hello")
    // hello
    // world
}

func main() {
	fmt.Println("counting")

	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}

	fmt.Println("done")
}
// counting
// done
// 9
// 8
// 7
// ...
// 1
// 0

```

## Methods
- **Go does not have classes**. 
- However, you can define **methods on types**.
- A method is a function with a special receiver argument.
    - The receiver appears in its own argument list **between the func keyword and the method name**.
    - a method is just a function with a receiver argument
    - method 也可以直接用一般 function 取代
    - 但是 method 更明確地寫出要使用在哪個 receiver type 上
    - 使用 method 就可以對此 receiver 實例上使用  `.` 來呼叫此 method
- Method 可以定義在 struct 及 其他自定義的 type 上
    - You can only declare a method with a receiver whose **type is defined in the same package as the method**
    - 所以我們不能在 built-in type 如 int, float64 上定義 method
    - 但可以定義 method 在自定義 type 上


In this example, the Abs method has a receiver of type Vertex named v. 

```go=
package main

import (
	"fmt"
	"math"
)

type Vertex struct {
	X, Y float64
}

func (v Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := Vertex{3, 4}
    fmt.Println(v.Abs()) // 可以使用 v.Abs，而不是 Abs(v)
}

```
- method 範例
```go=
// 定義 method 在自定義 type 上
type MyFloat float64

func (f float64) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

```

### Method set
- 可以有 non-pointer receiver 跟 pointer receiver
- 無論宣告 method 時，receiver 是 pointer 或 non-pointer，**在直接透過 instance 調用該 method 時，都是通用的。但其 receiver type 是否為 pointer 會影響該 type 對 interface 的實現**
    - Method set 校验仅限于在赋值给 interface 变量时进行，无论是T还是*T类型的方法集中的方法，对于T或*T类型变量都是可见且可以调用的，如下面代码 都是合法的，因为Go编译器会自动为你的代码做receiver转换：

    pt.Method1() <=> (*pt).Method1() // 用 pointer 呼叫 non pointer receiver method
    t.Method3() <=> (&t).Method3() // 用 non pointer 呼叫 pointer receiver method


- 如果要利用 method 修改 receiver 裡面的值，那必須要用 pointer receiver ，否則會 copy 一份 receiver 的值傳去 （包含 struct）
    - Methods with pointer receivers can modify the value to which the receiver points
    - Since methods often need to modify their receiver, pointer receivers are more common than value receivers.
    - 如果要使用 pointer ，只需要在定義 method 那邊註明，呼叫時不需要修改
        - 照理如果 method 要收到 pointer，則呼叫時要使用 `&type.method()`，但我們**仍然只需要寫 `type.method()` 就好**，Go 會依照 method 的定義來決定是傳值或傳址
        - Go interprets the statement v.Scale(5) as (&v).Scale(5) since the Scale method has a pointer receiver
        - 但如果是一般的 function，則呼叫函數時需要呼應函數定義，在要傳址的函數需要加上 `&`
        - 另外，在 method 中，收到 pointer receiver 後，如果要檢視 struct 中的 field，**仍然使用 `pointer.field` 就好**
            - x.f is shorthand for (*x).f.
            - 例如下方的`v.X`
            - 跟 C++ 不同，在 C++ 中要使用 `*v.X` 或 `v -> X`

- 使用 method 時，應該要依照時機決定使用 pointer receiver 或 non pointer receiver
    - 並且要考慮到維護的難易度
    - 很多時候會使用 pointer receiver，就算我們沒有要去修改 receiver 中的值
        -  The first is so that the method can modify the value that its receiver points to.
        -  The second is to **avoid copying the value** on each method call. This can be more efficient if the receiver is a large struct
-  常使用 pointer 的時機
    -  you don’t want to pass around a lot of data, everything  in Go is PASS BY VALUE
    -  you want to change the data at a location
-  **因為 method 會搭配 interface 使用，所以定義在同一個 type 上的 method 應該要統一使用 value 或 pointer receiver**
    -  In general, all methods on a given type should have either value or pointer receivers, but not a mixture of both

```go=
type Vertex struct {
	X, Y float64
}


func (v *Vertex) Scale(f float64) { // 傳 receiver 的 pointer 來
	v.X = v.X * f
	v.Y = v.Y * f
}

func main() {
	v := Vertex{3, 4}
	v.Scale(10) // 呼叫的地方不需要修改
}
    
```


## Interfaces
- 在 interfaces 中定義需要滿足的 method，但不需要寫出如何實作
- 在 struct 中實作 interfaces 中定義的 method，只要有滿足該 interface 所有 method 的 struct，其 實例 就可以是 interface 的實例（滿足 interface type 及 struct type 的 value）
- 只要有滿足 interface 定義的 method ，就可以是 interface 的 type
    - 類型 T 的 Method Set 为所有 receiver 为 T类型的方法组成
    - 类型*T的Method Set 则包含所有 receiver 为 T 和 *T 类型的方法。
    - 因此一個 method 其 receiver type 是否為 pointer 會影響該 type 對 interface 的實現
- Empty Interfaces 
    - 沒有定義任何 method 的 interfaces
    - 所有 type 都實現了 empty interfaces
    - 所以代表empty interfaces 可以接收所有 type 的 value 
    - 例如 `func Println(a ...interface{}) (n int, err error)` 就代表 println 這個 function 可以接收 **unlimited 個任何 type 的參數**
```go=
package main

import (
	"fmt"
)

type person struct {
	first string
	last  string
}

type secretAgent struct {
	person
	ltk bool
}

func (s secretAgent) speak() { 
// secretAgent 這個 type 實現了 speak 這個 method
	fmt.Println("I am", s.first, s.last, " - the secretAgent speak")
}

func (p person) speak() {
// person 這個 type 實現了 speak 這個 method
	fmt.Println("I am", p.first, p.last, " - the person speak")
}

// 只要有實現 speak method 都可以是 type human
type human interface {
	speak()
}

func bar(h human) {
// bar 接收一個 type of human，並檢查他的 type
	switch h.(type) {
	case person:
		fmt.Println("I am a person", h.(person).first)
	case secretAgent:
		fmt.Println("I am a secretAgent", h.(secretAgent).first)
	}
	fmt.Println("I am others", h)
}


func main() {
	sa := secretAgent{
		person: person{
			"James",
			"Bond",
		},
		ltk: true,
	}

	p := person{
		first: "Dr.",
		last:  "Yes",
	}
    
	bar(sa) // sa is a type of secretAgent and Human
	bar(p) // p is a type of secretAgent and Human

}

```

## Method 與 Interface (重要)
- 一個為 pointer 的 struct 實例，其 method set 包含所有該 type 的 method，不論 receiver 為 pointer 或 non pointer
- 一個普通的 struct 實例，其 method set 只包含該 type 且 receiver 為 non pointer 的 method
- 如果直接使用 struct 實例來呼叫其 method，因為 go 會幫助我們編譯，因此不論是 pointer 或 non pointer 的實例，都可以呼叫定義在該 type 上的所有 method
- 但如果要實現 interface，就必須看該 type 之 method set 是否滿足其 interface
    - 例如 non pointer 的實例，其 method set 不包含定義在 pointer receiver 的 method，因此沒有實現該 method，就無法實現該 interface
```
对于非interface type的类型T，其Method Set为所有receiver为T类型的方法组成；
而类型*T的Method Set则包含所有receiver为T和*T类型的方法。

// details-in-go/3/othertypemethodset.go
package main

import "./utils"

type T struct {
}

func (t T) Method1() {
}

func (t *T) Method2() {
}

func (t *T) Method3() {
}

func main() {
    var t T
    utils.DumpMethodSet(&t)

    var pt *T
    utils.DumpMethodSet(&pt)
}

我们要dump出T和*T各自的Method Set，运行结果如下：

$go run othertypemethodset.go
main.T's method sets:
     Method1

*main.T's method sets:
     Method1
     Method2
     Method3

可以看出类型T的Method set仅包含一个receiver类型为T的方法：Method1，
而*T的Method Set则包含了T的Method Set以及所有receiver类型为*T的Method。

如果此时我们有一个interface type如下：

type I interface {
    Method1()
    Method2()
}

那下面哪个赋值语句合法呢？合不合法完全依赖于右值类型是否实现了interface type I的所有方法，即右值类型的Method Set是否包含了I的 所有方法。

var t T
var pt *T

var i I = t

or

var i I = pt

编译错误告诉我们：

     var i I = t // cannot use t (type T) as type I in assignment:
                  T does not implement I (Method2 method has pointer receiver)

T的Method Set中只有Method1一个方法，没有实现I接口中的 Method2，因此不能用t赋值给i；而*T实现了I的所有接口，赋值合 法。

-- 重要 --
不过Method set校验仅限于在赋值给interface变量时进行，无论是T还是*T类型的方法集中的方法，对于T或*T类型变量都是可见且可以调用的，如下面代码 都是合法的：

    pt.Method1()
    t.Method3()

因为Go编译器会自动为你的代码做receiver转换：

    pt.Method1() <=> (*pt).Method1()
    t.Method3() <=> (&t).Method3()
```

## Anonymous self-executing func

```go=

func main() {
	foo()

	func() {
		fmt.Println("Anonymous func ran")
	}() // 立即執行的匿名函數

	func(x int) {
		fmt.Println("The meaning of life:", x)
	}(42)
}

func foo() {
	fmt.Println("foo ran")
}

```

### A function return function
- a function return function
```go=
func main() {
    // bar() 會回傳一個 function ，所以再執行這個 function，會得到回傳的 int
	fmt.Println(bar()())

}

func bar() func() int {
    // bar 回傳一個「會回傳 int 的 function」
	return func() int {
		return 451
	}
}
```

## Callback


- Callback 就是 passing a func as an argument，並在適當的時候會呼叫該 function
```go

func main() {
    // 將 sum 這個函數當作參數傳入
	t := evenSum(sum, []int{1, 2, 3, 4, 5, 6, 7, 8, 9}...)
	fmt.Println(t)
}

func sum(x ...int) int {
	n := 0
	for _, v := range x {
		n += v
	}
	return n
}

func evenSum(f func(x ...int) int, y ...int) int {
	var xi []int
	for _, v := range y {
		if v%2 == 0 {
			xi = append(xi, v)
		}
	}
	total := f(xi...)
	return total
}
```

## Closure
- variables declared in the outer scope are accessible in inner scopes
```go=

func main() {
	a := incrementor()
	b := incrementor()
	fmt.Println(a()) // 1
	fmt.Println(a()) // 2
	fmt.Println(a()) // 3
	fmt.Println(a()) // 4
    
	fmt.Println(b()) // 1, 因為是一個新的 instance of function
	fmt.Println(b()) // 2
}

func incrementor() func() int {
	var x int
	return func() int {
		x++
		return x
	}
}

```

## Recursion
- factorial in recursion and loop
```go=
func main() {
	fmt.Println(4 * 3 * 2 * 1)
	n := factorial(4)
	fmt.Println(n)

	n2 := loopFact(4)
	fmt.Println(n2)
}

func factorial(n int) int {
	if n == 0 {
		return 1
	}
	return n * factorial(n-1)
}

func loopFact(n int) int {
	total := 1
	for ; n > 0; n-- {
		total *= n
	}
	return total
}


```

# JSON
## Marshalling and Unmarshalling
- Marshalling : golang struct -> JSON objects
    - 例如要送 REST API Respond 時需要使用 JSON
- Unmarshalling: JSON objects (String literal byte slice) -> golang struct
    - 收到 HTTP Request 後要把 JSON 格式解析，需要把 byte slice of string literal -> Golang struct obj

## JSON to GO 工具
- https://mholt.github.io/json-to-go/
## 使用 json package
### 重要
- 不論是 Marshal 或 Unmarshal，因為都是要對外溝通 (對 json 套件)，所以要被轉換的 struct 中的 **Field name 一定要大寫**
    - 只有大寫的 filed name 會被 Marshal 轉換成 JSON
    - 只有大寫的 field name 可以從 JSON 讀入成 struct
    - struct 本身的 name 有沒有大寫沒差
### JSON
- JSON can represent four primitive types (strings, numbers, booleans, and null) and two structured types (objects and arrays)
- JSON 的 name 要是 string，value 可以是以上的 type
### json.Marshal
- json.Marshal 就是 encode/convert to JSON object
    - 輸入是 struct 或是 raw string literal formatted to JSON
        - raw string 要使用 `` 而不是 ""
    - 會回傳 a slice of byte ([]byte) 及 err
        - slice of byte 就是一個 raw UTF-8 string
    - Go 中的 type 如何對應到 JSON
        - bool -> JSON booleans
        - float64 -> JSON numbers
        - string -> JSON strings
        - nil -> JSON null

```go=
package main

import (
	"encoding/json" // 引入套件
	"fmt"
)

type person struct { // struct name 可以不大寫
	First string // Field name 一定要大寫
	Last  string
	Age   int
}

func main() {
	p1 := person{
		First: "James",
		Last:  "Bond",
		Age:   32,
	}

	p2 := person{
		First: "Miss",
		Last:  "Moneypenny",
		Age:   27,
	}

	people := []person{p1, p2} // list of struct object

	fmt.Println(people)
    // [{James Bond 32} {Miss Moneypenny 27}]


	bs, err := json.Marshal(people) // bs 是 slice of byte = raw UTF-8 string
	if err != nil {
		fmt.Println(err)
	}
	fmt.Println(string(bs)) // 轉換成 string
    // [{"First":"James","Last":"Bond","Age":32},{"First":"Miss","Last":"Moneypenny","Age":27}]
}

```
### json.Unmarshal
- json.Unmarshal 就是 parse JSON object into a valid Golang struct
    - 輸入是 slice of byte (raw string) 以及 a pointer to a struct to parse ths JSON into （parse 之後的 JSON 要放的位置）
    - 回傳 error
    - **Only field founds in the destination type(struct) will be decoded**
        - 如果 JSON 中有些欄位在 destination struct 中沒有，則這些欄位會直接被忽略
- Tag
    - tag 用來指定 JSON 中的哪些 field 要 mapping 到 Go Struct 中的哪些 field
    - 如果 JSON 中的 field name 跟 struct 中的依樣可以不用指定 tag

```go=
package main

import (
	"encoding/json"
	"fmt"
)

type person struct {
	First string `json:"First"` // Field name 要大寫，並加上 tag
	Last  string `json:"Last"`
	Age   int    `json:"Age"`
}

func main() {
	s := `[{"First":"James","Last":"Bond","Age":32},{"First":"Miss","Last":"Moneypenny","Age":27}]`
    bs := []byte(s)  // string 轉成 slice of byte (UTF-8)
	fmt.Printf("%T\n", s) //string
    fmt.Printf("%T\n", bs) // []uint8 (就是 slice of byte)

	var people []person

	err := json.Unmarshal(bs, &people) // 要傳入 slice of byte 跟要存放 JSON 的 address
	if err != nil {
		fmt.Println(err)
	}

	fmt.Println("\nall of the data", people) //all of the data [{James Bond 32} {Miss Moneypenny 27}]

	for i, v := range people {
		fmt.Println("\nPERSON NUMBER", i)
		fmt.Println(v.First, v.Last, v.Age)
	}  
    // PERSON NUMBER 0
    // James Bond 32

    // PERSON NUMBER 1
    // Miss Moneypenny 27
}

```

### Encode and Decode
- 如果不想要使用 Marshal 跟 Unmarshal 把結果存進變數中，可以使用 Encode 跟 Decode 直接將結果 output 到某個 stream 中
```go=
err := json.NewEncoder(os.Stdout).Encode(users)
	if err != nil {
		fmt.Println("We did something wrong and here's the error:", err)
	}

```
# Sort

## 使用 package
```go=
package main

import (
	"fmt"
	"sort"
)

func main() {
	xi := []int{4, 7, 3, 42, 99, 18, 16, 56, 12}
	xs := []string{"James", "Q", "M", "Moneypenny", "Dr. No"}

	fmt.Println(xi)
	sort.Ints(xi) // 直接修改原本的 slice
	fmt.Println(xi)

	fmt.Println("------")
	fmt.Println(xs)
	sort.Strings(xs) // 直接修改原本的 slice
	fmt.Println(xs)

}

```

## custom Sort
- 針對自定義的 struct，如果想要定義 sort 方法，只需要滿足 sort 的 `type Interface`
    - 其中有三個 method 要滿足
        - Len
        - Less(i, j int) bool
        - Swap(i, j int)
    - 實際內部的 sorting 細節我們不需要知道，這是使用 Interface 的好處
![](https://i.imgur.com/6Upy0lK.png)

- 例如有一個 person struct，我們想使用其中的 age field 來 sort

```go=
package main

import (
	"fmt"
	"sort"
)

type Person struct { // 因為要給 sort package 使用，所以 field 要大寫
	First string
	Age   int
}

type ByAge []Person // ByAge 是 type of slice of Person

// 實現 Interface 的 三個 method
func (a ByAge) Len() int           { return len(a) }
func (a ByAge) Swap(i, j int)      { a[i], a[j] = a[j], a[i] }
func (a ByAge) Less(i, j int) bool { return a[i].Age < a[j].Age } 
// 以 Age 來做排序基準

func main() {
	p1 := Person{"James", 32}
	p2 := Person{"Moneypenny", 27}
	p3 := Person{"Q", 64}
	p4 := Person{"M", 56}

	people := []Person{p1, p2, p3, p4}

	fmt.Println(people)
	sort.Sort(ByAge(people)) // 把 slice of Person 轉型成 ByAge
	fmt.Println(people)

}

```

# bcrypt
- 常用在 password 的加密

```go=
package main

import (
	"fmt"

	"golang.org/x/crypto/bcrypt"
)

func main() {
	password := `123456`
	bs, err := bcrypt.GenerateFromPassword([]byte(password), bcrypt.MinCost)
	if err != nil {
		fmt.Println(err)
	}

	fmt.Println("original pw", password)
	fmt.Println("hashed pw", bs)

	fakePassword := "fake123456"
	err = bcrypt.CompareHashAndPassword(bs, []byte(fakePassword))
	if err != nil {
		fmt.Println("Login denined")
		return
	}
	fmt.Println("log in successfully")
}

```

# Concurrency
- Go 是一個專門為多 CPU 創造的語言
    - 可以輕易地達成 Concurrency

- Concurrency 是一種 Design pattern ，支援 Concurrency 的 program **有能力可以做到 parallelism**
- parallelism 是否能真的達成，就取決於電腦有**幾個 CPU**
    - 支援 concurrency 的 program 在一個 CPU 上還是不能達到 parallelism
    - 不支援 concurrency 的 program 在多個 CPU 上還是不能達到 parallelism
- 若程式碼沒有使用共時性 (concurrency) 的特性來撰寫，則無法真正發揮平行處理 (parallel computing) 所帶來的效能提升。

## Goroutines
- 大部分的程式語言，像是 C++ 或 Java 等，以 thread 做為並行程式的單位。Go 程式以 goroutine 做為並行執行的程式碼區塊，goroutine 類似於 thread，但更輕量，一次啟動數百甚至數千個以上的 goroutine 也不會占用太多記憶體。要使用 goroutine，在函式前加上 go 關鍵字即可。

- **在呼叫 function 或 method 前使用 `go` 就能開啟一個新的 `goroutines`**
    - 達成 concurrency disign pattern
    - **如果沒有特別 wait goroutines 完成，則 main  結束後 process 就會直接結束，不管其他 routines 有沒有結束**

- A goroutine has a simple model: it is a function executing concurrently with other goroutines in the same address space.

- When a new Goroutine is started, the goroutine call returns immediately. Unlike functions, the control does not wait for the Goroutine to finish executing. The control returns immediately to the next line of code after the Goroutine call and any return values from the Goroutine are ignored.

- The main Goroutine should be running for any other Goroutines to run. If the main Goroutine terminates then the program will be terminated and no other Goroutine will run.
    - main goroutine 結束後所有 goroutine 也會跟著結束

- 如果某個 function 有 return 值，必須包覆在另一個立即執行的 function 中，且將 該 return 值放入 channel 中。因為 goroutines 無法 return 值

```go=
package main

import (
	"fmt"
)

func doSomething(x int) int {
	return x * 5
}

func main() {
	ch := make(chan int)
	go func() { 
    // 把 doSomething 放在另一個立即執行的 func 中，並將 return 值放入 channel
		ch <- doSomething(5)
	}()
	fmt.Println(<-ch)
}

```

### Gosched
> Gosched yields the processor, allowing other goroutines to run. It does not suspend the current goroutine, so execution resumes automatically.
> 
这个函数的作用是让当前goroutine让出CPU，好让其它的goroutine获得执行的机会。同时，当前的goroutine也会在未来的某个时间点继续运行。

```go=
package main

import (
	"fmt"
	"runtime"
)

func say(s string) {
	for i := 0; i < 2; i++ {
        fmt.Println("before Gosched", i, s)
        runtime.Gosched()
        fmt.Println("after goSched", i, s)
        }
}

func main() {
	go say("world")
	say("hello")
}

```
輸出為
```
before Gosched 0 hello
before Gosched 0 world
after goSched 0 hello
before Gosched 1 hello
after goSched 0 world
before Gosched 1 world
after goSched 1 hello 
```
- 上述例子中
    - main 啟動一個 `say(world)` 的 goroutines，並立即返回至 main goroutines 中
    - main goroutines執行第一次 `say(hello)`  時，先 print before，然後遇到 `Gosched()`，便把 CPU 交給另一個 goroutines
    - 另一個 goroutines 開始執行，依樣先 print 了 before 後，遇到 goshced，便把 CPU 交還給 main
    - main 從剛剛中斷的地方開始跑，於是 print 了 after，接著進入第二圈 loop。print 出第二次 before 後又遇到 gosched，再次交棒給另一個 goroutines
    - 同上，這個 goroutine 跑到第二次 loop 時，先 print 了 before 後碰到 gosched，CPU 交給 main goroutine
    - main goroutine 把最後一次的 print 結束後，**main goroutine** 便結束了，於是等個 process 結束，導致最後的一次 world 沒有被 print

- 把代码中的runtime.Gosched()注释掉，則 world 完全不會有機會執行
- 这里同时可以看出，go中的goroutins并不是同时在运行。事实上，如果没有在代码中通过 `runtime.GOMAXPROCS(n)` 其中n是整数，指定使用多核的话，goroutins都是在一个线程里的，它们之间通过不停的让出时间片轮流运行，达到类似同时运行的效果。
## WaitGroup
- 由於 goroutine 和主程式並時執行，若我們沒有使用 WaitGroup 將程式同步化，本程式在主程式結束時即提早結束，因此，我們宣告 wg 變數，在程式尾端等待所有 goroutine 執行結束。

- **並時性程式和傳統的循序式程式的思維不太一樣，無法保證程式的先後順序，需注意。**
- A WaitGroup waits for a collection of goroutines to finish
    - 用 add 來 set the number of goroutines we want to wait for
    - 用 done 來告訴 waitgroup 這個 goroutines 已經結束，we call .Done() within any goroutine to signal the end of its’ execution.
    - 用 wait 來讓 main 開始等待 waitGroup

- Writing concurrent code is super easy: all we do is put “go” in front of a function or method call. 
- The main goroutine calls Add to set the number of goroutines to wait for. 
- Then each of the goroutines runs and calls Done when finished. 
- At the same time, Wait can be used to block until all goroutines have finished. 

```go=
package main

import (
	"fmt"
	"runtime"
	"sync"
)

var wg sync.WaitGroup // 一個 waitgroup

func main() {
	fmt.Println("OS\t\t", runtime.GOOS) // 執行的 OS
	fmt.Println("ARCH\t\t", runtime.GOARCH) // 執行的 arch
	fmt.Println("CPUs\t\t", runtime.NumCPU()) // CPU 個數
	fmt.Println("Goroutines\t", runtime.NumGoroutine()) // goroutine 個數，一開始是 1

	wg.Add(1) // 加入一個 gorotines 至 waitgroup
	go foo() // 開啟一個新的 gorotines 來執行 foo
	bar()

	fmt.Println("CPUs\t\t", runtime.NumCPU())
	fmt.Println("Goroutines\t", runtime.NumGoroutine())// 2，因為多了 foo
	wg.Wait() // 等待 wait group 中的 gorotines 執行完畢
}

func foo() {
	for i := 0; i < 10; i++ {
		fmt.Println("foo:", i)
	}
	wg.Done() // 告訴 waitgroup 該 gorotines 已經執行完畢
}

func bar() {
	for i := 0; i < 10; i++ {
		fmt.Println("bar:", i)
	}
}

```

- 以上的 code 如果在多 CPU 的環境中執行，即便沒有使用 waitGroup 也有機會執行完畢，因為不同 CPU 可以分別來執行不同的 goroutines
- 然而如果只有一個 CPU，則一定要加入 waitgroup，讓原本的 main goroutines 執行完之前，可以先執行 waitgroupt 中的 goroutine

## RaceCondition
- A race condition will give unpredictable results
- 使用 goroutine 可能發生 **race condition**，可以使用 mutex 避免
    - ![](https://i.imgur.com/vKi9ysE.png)

```go=
package main

import (
	"fmt"
	"runtime"
	"sync"
)

func main() {
	fmt.Println("CPUs:", runtime.NumCPU())
	fmt.Println("Goroutines:", runtime.NumGoroutine())

	counter := 0

	const gs = 10
	var wg sync.WaitGroup
	wg.Add(gs)

	for i := 0; i < gs; i++ {
		go func() {
			v := counter
			runtime.Gosched()
			v++
			counter = v
			wg.Done()
		}()
		fmt.Println("Goroutines:", runtime.NumGoroutine())
	}
	wg.Wait()
	fmt.Println("Goroutines:", runtime.NumGoroutine())
	fmt.Println("count:", counter)
}

```
輸出為
```
CPUs: 1
Goroutines: 1
Goroutines: 2
Goroutines: 3
Goroutines: 4
Goroutines: 5
Goroutines: 6
Goroutines: 7
Goroutines: 8
Goroutines: 9
Goroutines: 10
Goroutines: 11
Goroutines: 1
count: 1
```

- 上面的例子，因為只有一個 CPU
    - main 中， wait group 中加了 10 個 goroutines
    - main 中跑了 loop 10 次，開了 10 個 goroutines 
        - 目前 CPU 都還是在 main goroutines 中，沒有跑其他 goroutines，**因為只有一個 CPU**
    - 遇到 wg.Wait 後，main goroutines 會交給其他 goroutines 跑
    - 每個 goroutines 都先讀 counter (此時為 0)，然後碰到 Gosched() 後就把 CPU 交給下一個 goroutines
    - 等所有 goroutines 都讀完 counter 後，就各自 v++ ，再寫回 counter (都寫進 1)，並結束該 goroutine
    - 等所有 goroutine 都結束， main 才結束，但 counter 為 1，此為 **race condition**

- 如果是多個 CPU 同時執行
    - 則在 main 會與其他 goroutines 一起執行
    - 仍然會發生 race condition，且**每次的執行可能不同，是不可預期的**

- 我們可以用 command `-race` 來檢查是否有 race condition 發生
    - `go run -race filename.go `

## Mutex
- 使用 mutex 來鎖住要讀寫的 data，在某段時間內防止其他 goroutines 來讀取或改寫
- A “mutex” is a mutual exclusion lock. Mutexes allow us to lock our code so that only one goroutine can access that locked chunk of code at a time.

- Mutex 是互斥鎖（全局鎖）
- RWMutex 是一个读写锁，该锁可以加多个读锁或者一个写锁，其经常用于读次数远远多于写次数的场景．
```go=
package main

import (
	"fmt"
	"runtime"
	"sync"
)

func main() {
	fmt.Println("CPUs:", runtime.NumCPU())
	fmt.Println("Goroutines:", runtime.NumGoroutine())

	counter := 0

	const gs = 100
	var wg sync.WaitGroup
	wg.Add(gs)

	var mu sync.Mutex 

	for i := 0; i < gs; i++ {
		go func() {
			mu.Lock() // 在要讀寫前先 lock
			v := counter
			// time.Sleep(time.Second)
			runtime.Gosched()
			v++
			counter = v
			mu.Unlock() // 完成操作之後再 unlock
			wg.Done()
		}()
		fmt.Println("Goroutines:", runtime.NumGoroutine())
	}
	wg.Wait()
	fmt.Println("Goroutines:", runtime.NumGoroutine())
	fmt.Println("count:", counter)
}

```

## Atomic
- 除了使用 mutex 外，也可以使用 atomic 來避免 race condition
- 是比較進階的用法，要謹慎使用
- 使用 atomic 需要明確的指定變數的 type (如 Int64)
- We can use package atomic to also prevent a race condition in our incrementer code.

```go=
package main

import (
	"fmt"
	"runtime"
	"sync"
	"sync/atomic"
)

func main() {
	fmt.Println("CPUs:", runtime.NumCPU())
	fmt.Println("Goroutines:", runtime.NumGoroutine())

	var counter int64 // 使用 atomic 要指定 type

	const gs = 100
	var wg sync.WaitGroup
	wg.Add(gs)

	for i := 0; i < gs; i++ {
		go func() {
			atomic.AddInt64(&counter, 1) // write without race condition
			runtime.Gosched()
			fmt.Println("Counter\t", atomic.LoadInt64(&counter)) // read without race condition
			wg.Done()
		}()
		fmt.Println("Goroutines:", runtime.NumGoroutine())
	}
	wg.Wait()
	fmt.Println("Goroutines:", runtime.NumGoroutine())
	fmt.Println("count:", counter)
}

```

# Channels
- making a channel
`c := make(chan int)`
- putting values on a channel
`c <- 42`
- taking values off of a channel
`<-c`
- buffered channels
`c := make(chan int, 4)`
- **taking values from channel-1 and send to channel-2**
`c2 <- <- c1` // 從 c1 拿出來再給 c2
- channel 是 **FIFO 先進先出 （queue）**
- **channels  block**
    - they have to pass/receive the value at the same time

- channels allow us to pass values between goroutines
- 下面的 code 不會運作
    - 因為執行到 s <- "hello" 這步的時候, sender 就會進入 ready 狀態，然後就停住了, 也就是他不會執行到 val := <- s .
```go=
import "fmt"

func main(){
     s := mack(chan string) //宣告一個 channel 變數
     s <- "hello"           //寫入 channel (sender)
     val <- s               //讀取 channel (receiver)
     fmt.Println(val)
}
```
> IMPORTANT: CHANNELS BLOCK

- 如果改成以下就會運作，因為有兩個 goroutine 
```go=
import "fmt"


func main(){
     s := make(chan string) //宣告一個 channel 變數
     go func(){
        s <- "hello"           //寫入 channel (sender)
     }()

     val := <- s               //讀取 channel (receiver)
     fmt.Println(val)
}
```
執行順序就是

1. 建立一個 goroutine  //此時 s <- "hello" 還沒執行
2. 執行 val := <- s   //s 空的, receiver ready (停住)
3. 執行 s <- "hello"  //sender 將訊息寫入 s, sender ready
4. val := <- s       //成功讀取 s, (因為 receiver, sender 都 ready)
5. fmt.Println(val)

goroutine 不見得會比較慢執行. 不過重點不在這, 就不多描述了.

- 以下的例子
```go=
import "fmt"

func main(){
     s := make(chan string)
     go func() {
          for i := 0; i < 3; i++ {
               fmt.Println("sender hello",i)
               s <- fmt.Sprintf("receiver hello %d", i)
          }
     }()

     for i := 0; i < 3; i++ {
          val := <-s
          fmt.Println(val)
     }
}
```

結果
```
sender hello 0
sender hello 1
receiver hello 0
receiver hello 1
sender hello 2
receiver hello 2
```

程式碼順序是這樣

1. 建立一個 goroutine 
2. 執行 val := <- s          //s 空的, receiver ready (停住)
[sender hello 0]
3. 執行 s <- fmt.Sprintf..   //sender 將訊息寫入 s, 傳訊成功
[sender hello 1]
4. 執行 s <- fmt.Sprintf..   //s 有值, sender ready(停住)
5. val := <-s               //讀到 s 的值
[receiver hello 0]
6. val := <-s               //s 空的, receiver ready, 傳訊成功
[receiver hello 1]
7. val := <-s               //s 空的, receiver ready (停住)
[sender hello 2]
8. 執行 s <- fmt.Sprintf..   //sender 將訊息寫入 s, 傳訊成功
9. val := <-s               //讀到 s 的值
[sender hello 2]

## buffering (buffered channels)

- buffer
    - **事先開好一定容量的 channel，直到達到這個容量後才會 block** (不會每一次都阻塞)
    - “The capacity, in number of elements, sets the size of the buffer in the channel. If the capacity is zero or absent, the channel is unbuffered and communication succeeds only when both a sender and receiver are ready.” Golang Spec

```go=
import "fmt"

func main(){
     s := make(chan string,2)
     go func() {
          for i := 0; i < 3; i++ {
               fmt.Println("sender hello",i)
               s <- fmt.Sprintf("receiver hello %d", i)
          }
     }()

     for i := 0; i < 3; i++ {
          val := <-s
          fmt.Println(val)
     }
}
```

結果

```
sender hello 0
sender hello 1
sender hello 2
receiver hello 0
receiver hello 1
receiver hello 2
```

## Directional channels 
- receive channels：僅限於 receive data 的 channel
    - `cs := make(<-chan int)`
- send channels：僅限於 send data 的 channel
    - `cs := make(chan<- int)`


### Assign and Conversion
- 雙向 channel 是比較 general 的
- 單向 channel 是比較 specific 的
- 雙向 channel 可以 assign 給單向 channel
    - general can assign to specific 
    - 其他方向都不行

```go=
c := make(chan int)
cr := make(<-chan int) // receive
cs := make(chan<- int) // send

// general to specific assigns
cr = c
cs = c
```

- 雙向 channel 可以 convert to 單向 channel
    - general can convert to specific 
    - 其他方向都不行
```go=
c := make(chan int)
cr := make(<-chan int) // receive
cs := make(chan<- int) // send


// general to specific converts
fmt.Printf("c\t%T\n", (<-chan int)(c)) // <-chan int
fmt.Printf("c\t%T\n", (chan<- int)(c)) // chan<- int

```

### Useage
- 用在 function 中可以讓 code 更加易讀
    - 透過上述 Assign and Conversion 的原則，我們可以將雙向 channel 轉換成單向 channel
```go=
func main() {
	c := make(chan int)

	go send(c)

	receive(c) // block until send and receive both are ready

	fmt.Println("about to exit")
}

// send channel
func send(c chan<- int) {
	c <- 42
}

// receive channel
func receive(c <-chan int) {
	fmt.Println("the value received from the channel:", <-c)
}

```

## Range and Close
- Close channel 可以防止 channel 再被使用（誤用），也讓 code 更易讀
- 如果要使用 range 來從 channel 中讀出東西，一定要搭配 close
    - range 就是不停的 receive （會把 channel 中的東西拿出來）
    - Range stops reading from a channel when the channel is closed.
    - 如果 channel 沒有 close 則 range 就會一直讀，導致 error


```go=
func main() {
	c := make(chan int)

	go send(c)

	receive(c)

	fmt.Println("about to exit")
}

// send channel
func send(c chan<- int) {
	for i := 0; i < 100; i++ {
		c <- i
	}
	close(c) // 沒有 close 會產生 error
    // 由於 underlying 是指向同一個 channel，所以 close(c) 就是 close 到 main 中定義的那個 channel
}

// receive channel
func receive(c <-chan int) {
	for v := range c {
		fmt.Println("the value received from the channel:", v)
	}
}


```

- 從 已經 close 的 channel 中取值，不會出現 error，而是會取得 zero value (看 channel 是什麼 type)
- 使用 comma-ok 來確定 channel 是否已經 close
    - 如果 close ，則 ok 為 false

```go=
func main() {
	c := make(chan int)
	go func() {
		c <- 42
		close(c)
	}()

        v, ok := <-c 

	fmt.Println(v, ok) // 42, true ( 只要還可以取到值，則 ok 為 ture )

	v, ok = <-c

	fmt.Println(v, ok) // 0, false ( 已經 close 了 )
}

```

## Select
- The select statement lets a goroutine wait on multiple communication operations.

- A select blocks until one of its cases can run, then it executes that case. **It chooses one at random if multiple are ready.**

- Select statements pull the value from whatever channel has a value ready to be pulled

```go=
func main() {
	even := make(chan int)
	odd := make(chan int)
	quit := make(chan bool)

	go send(even, odd, quit)

	receive(even, odd, quit)

	fmt.Println("about to exit")
}

// send channel
func send(even, odd chan<- int, quit chan<- bool) {
	for i := 0; i < 100; i++ {
		if i%2 == 0 {
			even <- i
		} else {
			odd <- i
		}
	}
	quit <- true
}

// receive channel
func receive(even, odd <-chan int, quit <-chan bool) {
	for {
		select {
		case v := <-even:
			fmt.Println("the value received from the even channel:", v)
		case v := <-odd:
			fmt.Println("the value received from the odd channel:", v)
		case <-quit:
			return
		}
	}
}

```
## Fan in and Fan out
- 是一個常見的 design pattern
- fan out ：把一個大工作分散給多個 goroutine，讓他們同步進行
- fan in：把上述多個 goroutine 的工作成果聚集在一起
### Fan in

- 把很多個 goroutines 各自工作的結果，聚集進一個大 channel 中
- Taking values from many channels, and putting those values onto one channel.

- Eg 1
```go=
func main() {
	even := make(chan int)
	odd := make(chan int)
	fanin := make(chan int)

	go send(even, odd)

	go receive(even, odd, fanin)

	for v := range fanin { // keep reading until fanin is close
		fmt.Println(v)
	}

	fmt.Println("about to exit")
}

// send channel
func send(even, odd chan<- int) {
	for i := 0; i < 100; i++ {
		if i%2 == 0 {
			even <- i
		} else {
			odd <- i
		}
	}
	close(even)
	close(odd)
}

// receive channel
func receive(even, odd <-chan int, fanin chan<- int) {
	var wg sync.WaitGroup
	wg.Add(2)

	go func() {
		for v := range even {
			fanin <- v
		}
		wg.Done()
	}()

	go func() {
		for v := range odd {
			fanin <- v
		}
		wg.Done()
	}()

	wg.Wait()
	close(fanin)
}

```

- Eg2 較複雜
```go=
func main() {
	c := fanIn(boring("Joe"), boring("Ann"))
	for i := 0; i < 10; i++ {
		fmt.Println(<-c) // 從 c 中取出 10 個值，會 block （等到 c 中有值時才會 receive）
	}
	fmt.Println("You're both boring; I'm leaving.")
}

func boring(msg string) <-chan string {
	c := make(chan string)
	go func() { 
		for i := 0; ; i++ { // 無限的 send data 進去 c
            // 會 block，等 fanIn receive 後才會再 send
			c <- fmt.Sprintf("%s %d", msg, i) 
		}
	}()
	return c 
    // 雖然 c 馬上就被 return，但 goroutine 會一直進行
}

// FAN IN
func fanIn(input1, input2 <-chan string) <-chan string {
	c := make(chan string)
	go func() {
		for {
			c <- <-input1 // 從 input1 中一直取值，放進 c 中
		}
	}()
	go func() {
		for {
			c <- <-input2 // 從 input2 中一直取值，放進 c 中
		}
	}()
	return c
    // 雖然 c 馬上就被 return，但 goroutine 會一直進行
}

```

### Fan Out
- 把一個大工作分給很多 goroutine 做

- Eg1 - 切幾個小工作就開幾個 goroutine
```go=
func main() {
	c1 := make(chan int)
	c2 := make(chan int)

	go populate(c1)

	go fanOutIn(c1, c2)

	for v := range c2 {  // block until c2 closed
		fmt.Println(v)
	}

	fmt.Println("about to exit")
}

func populate(c chan int) {
	for i := 0; i < 100; i++ {
		c <- i
	}
	close(c)
}

func fanOutIn(c1, c2 chan int) {
	var wg sync.WaitGroup
	for v := range c1 { // block until c1 closed, 
		wg.Add(1) // 會加 100 個 goroutines 進 wait group
		go func(v2 int) { // 開 100 個 goroutine 去做 timeConsuming work （把大工作 fanout）
			c2 <- timeConsumingWork(v2) // 把工作的結果 fanin 到 c2
			wg.Done()
		}(v)
	}
	wg.Wait() // 等待所有小工作完成
	close(c2)
}

func timeConsumingWork(n int) int {
	time.Sleep(time.Microsecond * time.Duration(rand.Intn(500)))
	return n + rand.Intn(1000)
}

```

- Eg2-用一定數量的 goroutine 來完成很多小工作

```go=
func main() {
	c1 := make(chan int)
	c2 := make(chan int)

	go populate(c1)

	go fanOutIn(c1, c2)

	for v := range c2 {
		fmt.Println(v)
	}

	fmt.Println("about to exit")
}

func populate(c chan int) {
	for i := 0; i < 100; i++ {
		c <- i
	}
	close(c)
}

func fanOutIn(c1, c2 chan int) {
	var wg sync.WaitGroup
	const goroutines = 10 // 固定數量的 goroutines
	wg.Add(goroutines)

	for i := 0; i < goroutines; i++ { // 開 10 個 goroutine
		go func() {
			for v := range c1 { // 每個 goroutine 都一直工作，直到 c1 closed
				func(v2 int) {
					c2 <- timeConsumingWork(v2)
				}(v)
			}
			wg.Done()
		}()
	}
	wg.Wait()
	close(c2)
}

func timeConsumingWork(n int) int {
	time.Sleep(time.Microsecond * time.Duration(rand.Intn(500)))
	return n + rand.Intn(1000)
}

```

## Context
- 使用 context 可以幫助我們關閉關聯的 goroutine 
    - 避免只關閉一部分的 goroutine，而有某些還持續運行
    - 持續運行的 goroutine 會繼續消耗資源，或造成 goroutine leaks
- 是一個比較進階的用法

> In Go servers, each incoming request is handled in its own goroutine. Request handlers often start additional goroutines to access backends such as databases and RPC services. The set of goroutines working on a request typically needs access to request-specific values such as the identity of the end user, authorization tokens, and the request's deadline. When a request is canceled or times out, all the goroutines working on that request should exit quickly so the system can reclaim any resources they are using. At Google, we developed a context package that makes it easy to pass request-scoped values, cancellation signals, and deadlines across API boundaries to all the goroutines involved in handling a request. The package is publicly available as context. This article describes how to use the package and provides a complete working example.

- further reading:
    - https://blog.golang.org/context 
    - https://medium.com/@matryer/context-has-arrived-per-request-state-in-go-1-7-4d095be83bd8 
    - https://peter.bourgon.org/blog/2016/07/11/context.html 
- code:
    - exploring context
        - background
            - https://play.golang.org/p/cByXyrxXUf 
    - WithCancel
        - throwing away CancelFunc
            - https://play.golang.org/p/XOknf0aSpx
        - using CancelFunc
            - https://play.golang.org/p/UzQxxhn_fm 
        - Example
            - https://play.golang.org/p/Lmbyn7bO7e
        - func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
            - https://play.golang.org/p/wvGmvMzIMW 
    - with deadline
        - func WithDeadline(parent Context, deadline time.Time) (Context, CancelFunc)
        - https://play.golang.org/p/Q6mVdQqYTt 
    - with timeout
        - func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
        - https://play.golang.org/p/OuES9sP_yX 
    - with value
        - func WithValue(parent Context, key, val interface{}) Context
        - https://play.golang.org/p/8JDCGk1K4P 


# Error Handling
- error 是一個 interface
    - 只要滿足這個 method 就可以實現
```go=
type error interface {
    Error() string
}
```

- Go 的 error handling 跟其他語言不同
    - 不使用 try-catch
    - 而是直接在每次可能產生 err 的地方檢查

```go=
package main

import (
	"fmt"
	"io"
	"os"
	"strings"
)

func main() {
	f, err := os.Create("names.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer f.Close()

	r := strings.NewReader("James Bond")

	io.Copy(f, r)
}
```

```go=
package main

import (
	"fmt"
	"io/ioutil"
	"os"
)

func main() {
	f, err := os.Open("names.txt")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer f.Close()

	bs, err := ioutil.ReadAll(f)
	if err != nil {
		fmt.Println(err)
		return
	}

	fmt.Println(string(bs))
}
```

## Printing and logging
- fmt.Println()
    - 輸出在 standard output
- log.Println()
    - 用 log 的格式，可以輸出在其他地方
```go=
func main() {
	f, err := os.Create("log.txt")
	if err != nil {
		fmt.Println(err)
	}
	defer f.Close()
	log.SetOutput(f)

	f2, err := os.Open("no-file.txt")
	if err != nil {
		//		fmt.Println("err happened", err)
		log.Println("err happened", err)
		//		log.Fatalln(err)
		//		panic(err)
	}
	defer f2.Close()

	fmt.Println("check the log.txt file in the directory")
}
```
- log.Fatalln()
    - 發生嚴重的錯誤，會直接離開程序
    - Fatalln is equivalent to Println() followed by a call to os.Exit(1)
    - When os.Exit() is called, deferred functions don't run
- log.Panicln()
    - 發生較不嚴重的錯誤，可以使用 recover 來恢復
    - Panicln is equivalent to Println() followed by a call to panic().
    - deferred functions run
    - can use “recover”

## Recover
- 比較進階的用法
    - 用來處理 panic
- https://blog.golang.org/defer-panic-and-recover
- https://ieevee.com/tech/2017/11/23/go-panic.html

## Custom Error
- 待補（重看）
- We can add information to our errors. We can do this with
    - errors.New()
        - fmt.Errorf()
    - builtin.error

```go=
func main() {
	_, err := sqrt(-10)
	if err != nil {
		log.Fatalln(err)
	}
}

func sqrt(f float64) (float64, error) {
	if f < 0 {
		return 0, errors.New("norgate math: square root of negative number")
	}
	return 42, nil
}

============================================

func main() {
	_, err := sqrt(-10.23)
	if err != nil {
		log.Fatalln(err)
	}
}

func sqrt(f float64) (float64, error) {
	if f < 0 {
		return 0, fmt.Errorf("norgate math again: square root of negative number: %v", f)
	}
	return 42, nil
}
```

- 若是自定義的 Error struct，只要實現 Error method，就可以使用 error type

```go=
type norgateMathError struct {
	lat  string
	long string
	err  error
}

func (n norgateMathError) Error() string {
	return fmt.Sprintf("a norgate math error occured: %v %v %v", n.lat, n.long, n.err)
}

func main() {
	_, err := sqrt(-10.23)
	if err != nil {
		log.Println(err) // 呼叫 Error method 來產出 error string
	}
}

func sqrt(f float64) (float64, error) {
	if f < 0 {
		nme := fmt.Errorf("norgate math redux: square root of negative number: %v", f)
		return 0, norgateMathError{"50.2289 N", "99.4656 W", nme}
	}
	return 42, nil
}
```

# Writing Documentation
- 待補
# Testing
- 待補