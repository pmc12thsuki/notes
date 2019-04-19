DSA
===
###### tags: `DSA`

# 第一章 緒論
## 什麼是演算法
一個算法應該滿足以下元素：
- 有 input
- 有 output
- 正確性 - 可以確定 output 是正確的 （現實世界中不一定容易達到）
- 確定性 - 每一個步驟都很明確，沒有模稜兩可的步驟
- 可行性 - 每一個步驟都是可行的。例如把大象塞進冰箱就是個不可行的步驟
- **有窮性** - 對於任何輸入 n，都可以在經過有窮次操作後得到輸出
### 有窮性
有窮性的違反：
- 在 `hailstone` 的例子中可以知道，由於世人對於 `hailstone` 這個問題並沒有保證*有窮*的證明，所以我們可以開發針對 `hailstone` 問題的 **程序**，但並不能稱為一個 **算法**
- 我們常寫出 dead while 的程序，這不能稱作為算法

## 演算法的好壞
- 在已有的**算法**中，如何去比較算法的好壞優劣
- 我們用效率(成本)來評斷演算法的好壞
    - 速度越快越好
    - 儲存空間越少越好
- 需要一個 **度量** 演算法好壞的標準
- T~A~（P）：一個演算法 A，一個問題的實例 P，則用此算法解決此實例的計算成本為 T~A~（P）
    - 通常 P 為無窮多種
    - 我們通常會以P的 **規模** 來劃分類別 -> 把實例劃分為 n 個規模
    - 通常規模相近的計算成本相近、規模越大則成本越大
- T(n)：針對一個**特定的演算法**（同一個演算法），在規模同為 `n` 的所有實例中，成本最高（表現最壞）的那一個

## 計算機與實例
### 圖靈機模型與實例
- TM: Turing machine
- 例如：將二進位非負整數加一
    - 從最低位開始，一直向左，將所有的 1 改為 0，直到遇見第一個 0 或 #
    - 將遇見的 0 或 # 改為 1
    - 歸位到最低位
    - 進入停機狀態 h
### RAM 模型與實例
- RAM: Random access machine
- RAM 和 TM 是完全對等的
- 理論上大小應是無上限的，但現實中不可能
- R[0], R[1]....R[n]，n 表示第 n 個暫存器
- 可以做到許多基本操作，每個操作只需要常數時間
    - 賦值：R[i] <- c
    - 複製值： R[i] <- R[j]
    - pointer: R[i] <- R[R[j]] or R[R[i]] <- R[j]
    - 加減運算： R[i] <- R[j] + R[k], R[i] <- R[j] - R[k]
    - 強制轉向： GO TO l (某語句編號)
    - 條件語句、相等比值、大小比值：IF R[i] = 0 GO TO l / IF R[i] > 0 GO TO l
    - 終止語句： STOP。等於 TM 的停機狀態 h
- 在這些模型(RAM, TM)中，由於`算法的運行時間` 正比於 `算法需要的基本操作次數`，所以 T(n) 簡化為解決規模為 `n` 的問題，所需要的 **基本操作次數**
    - 只考慮次數而不考慮時間，代表不受運行的硬體所影響
    - 比考慮時間更加客觀
- 實例：取 floor 值
    - 計算 [c/d]：反覆地從 R[0] <- c+1 中，減去 R[1] = d，直到 R[0] < 0 之前，所做的減法次數 x 即為答案
    - 我們可以將所需的基本操作一一列出，得到該算法所需的 **基本操作次數**

## Big O
- 不求甚解：不需要太在意細節，主要是看這個演算法在**長遠**(輸入規模很大)時的**主流**(只看次方最大的項目)表現
- 使用**漸進分析**（asymptotic analysis）：在数学分析中是一种描述函数在极限附近的行为的方法。
- T(n)：需執行的**基本次數**
- S(n)：需要佔用的**儲存單元數**，但通常我們不考慮這部分
- Big O 定義：T(n) = O(f(n)) iff 存在 c >0, 當 n >> 2，有 T(n) **<** c*f(n)
    - example: 如下圖，因為 n>>2, 首先把 (n+2) 中的常數(2)換成 n, 再把 (6n^2 +4) 中的常數(4)換成 n^2, 再把 35n^3+6 中的常數(6)換成 n^3，最後變成 6*n^1.5 = c*f(n) 的形式，故 T(n) = O(n^1.5)
    ![](https://i.imgur.com/uzzSYOQ.png)
    - F(n) 比 T(n) 更為簡潔，且依然能反應 T(n) 的增長趨勢
    - 訣竅：
        - **常數係數可忽略**: O(f(n)) = O(c*f(n))
        - **低次項可忽略**: O(n^a + n^b) = O(n^a), a>b>0
- Big O 是對 T(n) 的一個悲觀假設
    - 當 n 夠大，Big O 是 T(n)的 **upper bound**
    - 當 n 小時則不一定

## 其他記號
### Big Omaga
- Big Omaga 定義：T(n) = Omaga(f(n)) iff 存在 c >0, 當 n >> 2，有 T(n) **>** c*f(n)
- 當 n 夠大，Big Omaga 是 T(n)的 **lower bound**, 即為算法表現最好的時候
### Theta
- theta 定義：T(n) = Theta(f(n)) iff 存在 c1 > c2 >0, 當 n >> 2，有 c1*f(n) > T(n) > c2*f(n)
- theta 可以說是 Big O 跟 Big Omaga 的一個組合
- 當 n 夠大時，Theta 是對 T(n) 的一個描述
![](https://i.imgur.com/K8g2MFD.png)

### Note-1.0
> 我們通常最關心的是 Big O

## 高效解
### 常數複雜度 O(1)
- O(1) =  2 = 2013 = 2049*89408 = 2013^2013....
- 效率最高的算法，不依 n 越大而改變速度
- 怎樣是 O(1): 
    - 一個算法不含**轉向**，即為循環、調用、遞迴等，必然依照順序執行，即為 O(1)

### 對數複雜度 O(log n)
- O(log n) =  log~100~n (換底) = log n^100^ ...
- 效率十分高，接近於常數
- 常數底數無所謂：因為底數是常數時是可忽略的
- 常數次冪無所謂：因為 log n^c^ = c* log n = O(log n)
- log 本身的次方不可忽略： 123* (log n)^321^ + log^105 * (n^2-n) = Big O((logn)^321)

## 有效解
### 多項式複雜度 O(n^c^)
- O(n^c^) =  O(n^3^) = O(n^k^)... k,c 為常數
- 線性複雜度：O(n) (k=1)
- O(n) ~ O(n^2^)：許多習題主要涵蓋的範圍

## 難解 
### 指數複雜度 O(a^n^)
- a 是常數
- 仍是可以計算的
- 計算成本增長極快，通常不能被接受
- 從 O(n^c^) 到 O(a^n^) 是從**有效算法** 到 **無效算法** 的分水嶺
- 然而很多問題可以輕易找到 O(2^n^) 的無效算法，卻不容易甚至無法轉換為 O(n^c^) 的有效算法
- 2-SUBSET 問題：
    - S 集合中有 n 個正整數，且S之總和為 2m
    - 問：S中是否存在子集 T，使T之總和為 m (即為把S分成兩半，兩半總合都是 m)
    - 實例：美國總統大選，共有 n=51 州，共有 2m=538 張選舉人團票，問是否可能出現票數相同的情況
    - 直覺算法：列出S中所有子集來驗證，將有 2^n^ 輪操作，屬於指數複雜度
    - 然而我們至今沒有找到「多項式複雜度」的解
    - 故 2-Subset 是 **NP-COMPLETE** 的問題
### Note-1.1
> P-Complete: **存在**可以在**多項式時間**內解決的算法
> NP-Complete：在目前的算法中，**不存在**可以在**多項式時間**內解決的算法

## 複雜度比較
![](https://i.imgur.com/7h3eNw5.png)

## 算法分析
- 評估算法的：
    - 正確性：不變性 + 單調性
    - 複雜度
- 複雜度：我們認為 C++ 即高級語言中的**基本指令**，跟RAM的**基本指令**，在漸進分析中的大致相當的（也就是說 c++ 中的一個指令 = 常數個RAM的基本指令），所以我們將用高級程式語言中的基本指令來計算複雜度。包含
    -  分支轉向： goto 
    -  迭代循環：for(), while()，本質上就是 if+goto
    -  調用、遞迴（自我調用），本質上也是 goto
-  分析複雜度的方法
    -  for, while 迴圈：級數求和
    -  遞迴：遞迴跟蹤 + 遞推方程
    -  猜測+驗證

### 級數 (重要)
- 算術級數：**與末項平方**同階
    - 1+....+n = O(n^2)
- 冪方級數：比冪次**高出一階**
    - 2次冪方: 1^2^ + 2^2^ + .....+ n^2^ = O(n^3^)
    - 3次冪方: 1^3^ + 2^3^ + .....+ n^3^ = O(n^4^)
    - 4次冪方: 1^4^ + 2^4^ + .....+ n^4^ = O(n^5^)
- 幾何級數、等比級數 (a>1)：與**末項同階**
    - T~a~(n): a^0^ + a^1^ .... + a^n^ = O(a^n^)
    - 1 + 2 + 4+ ....+ 2^n^ = O(2^n^)
- 收斂級數：總合不超過某上界的級數 = **O(1)**
- 未必收斂，然而長度有限的級數 **(常用到, 要記)**
    - 調和級數：1 + 1/2 + 1/3 + ..... + 1/n = O(logn)
    - 對數級數：log1 + log2 + log3 + ..... + logn = log(n!) = O(nlogn)

### 循環
- 二階循環-1
    - 屬於算術級數(**與末項平方**同階)：n+n+n+n+....n = n^2^ = O(n^2^)
    ```cpp=
    for(int i=0; i<n; i++){
        for(int j=0; j<n; j++){
            O1Operation(i,j);
        }
    }
    ```
- 二階循環-2
    - 屬於算術級數(**與末項平方**同階)：0+1+2+....(n-1) = O(n^2^)
    - 儘管跟第一個例子的公式不同，但在漸進意義上來說是相同的
    ```cpp=
    for(int i=0; i<n; i++){
        for(int j=0; j<i; j++){
            O1Operation(i,j);
        }
    }
    ```
- 二階循環-3
    - 屬於算術級數(**與末項平方**同階)：是例子2以常數 2019 縮小，所以依然是 O(n^2^)
    ```cpp=
    for(int i=0; i<n; i++){
        for(int j=0; j<i; j+=2019){
            O1Operation(i,j);
        }
    }
    ```
- 二階循環-4
    - 屬於幾何級數(**與末項**同階) = 1+2+4+...+2^(log~2~(n-1)) = O(n)
    ```cpp=
    for(int i=0; i<n; i *= 2){
        for(int j=0; j<i; j++){
            O1Operation(i,j);
        }
    }
    ```
### 實例：取非極端元素 O(1)
- 從子集 S 中取出一個任意的非極端元素，且 |S| > 3
- 非極端元素：不是最大也不是最小的
- 方法：從 S 中取出任意 3 個元素 (或直接取前三的元素)，排除其中最大及最小的，輸出剩餘的元素必是 S 中的非極端元素
- 無論 S 有多大，上述的複雜度 **都是 O(1)**

### 實例：Bubble sort
- 一直掃描序列，如果發現順序錯誤的就兩兩交換
- 每掃描一次都會將最大的元素歸位，故第 k 趟時，最大的 k 個元素都已經歸位
- 直到某趟掃描中發現沒有順序錯誤的元素，即可終止

![](https://i.imgur.com/gIjOONS.png)

- 驗證 bubble sort 是否正確
    - 不變性：可以斷言經過 k 輪掃描後，最大的 k 個元素都會歸位
    - 單調性：可以斷言經過 k 輪掃描後，問題規模縮減至 n-k
    - 正確性：經由`不變性`與`單調性`，至多經過 n 趟掃描之後，算法會終止(`單調性`)，且可以得到正確答案(`不變性之 k=n 時`)

### Note-1.2
> 技巧：透過算法的 **不變性** 與 **單調性** 得出 **正確性**

## 封底估算 Back-Of-The-Envelope Calculation
- 即為簡便計算方法
- 背：

![](https://i.imgur.com/DvmKPUr.png)

- 三生三世中的一天，即為一天中的一秒
- 算法的威力

![](https://i.imgur.com/ORxiMvY.png)

## 遞迴
- 遞迴可以創造出更優雅的程式碼
- 但遞迴在效率上其實比起一般的循環較差
- 將問題先切分成小問題後再解決，再將結果合併求出原始問題的答案。
    - Decrease and Conquer(減而治之): 把問題拆成大問題 + 一個已知解法的步驟
    - Divide and Conquer(分而治之) (較常使用)：把問題拆分成**大小相同**的若干個子問題

### Decrease and Conquer
#### 實例 - List 求和
- 使用一般循環解
    - 時間複雜度為 O(n)
    - 空間複雜度為 O(1) (輸入的空間不算在內)
    ![](https://i.imgur.com/pQw8nPV.png)

- 使用遞迴：線性遞迴（每個遞迴只會在產生下一個遞迴，而不會變成多條路）
    - 時間複雜度
        - 使用 Recursion trace (遞迴跟蹤) 分析
            - 畫出所有遞迴實例，計算單個遞迴實例的複雜度
            - **調用下個遞迴實例的代碼不用考慮進複雜度中**
            - 總複雜度 = 單個遞迴自身需要的複雜度 * 遞迴個數 
            - 這個例子中，單個遞迴複雜度為 O(1), 共有 n+1 個遞迴，故為 O(1) * (n+1) = O(n)
            - 這個方法很直觀，但不適合分析複雜的遞迴
        - 使用 Recurrence (遞迴方程) 分析
            - 為求解 sum(A,n), 需要遞迴求解規模為 n-1 的問題 sum(A,n-1), 其複雜度為 T(n-1)，並將其解加上 A[n-1] (O(1))
            - 故遞迴方程為：
                - T(n) = T(n-1) + O(1) //recurrence
                - T(0) = O(1) // base case
            - 解法
                -  T(n) - n = T(n-1) - (n-1) = T(n-2) - (n-2) = .... = T(1) - 1 = T(0) - 0 = O(1)
                -  T(n) = O(1) + n = O(n)
![](https://i.imgur.com/MZ7auIy.png)

#### 實例 - List 反向
-  ![](https://i.imgur.com/nGuE5qM.png)
- 每次都將題目從 n 縮減為 n-2

### Divide and Conquer
#### 實例 - List 求和
- 跟上方 decrease and conquer 不同，這次直接拆成兩個子問題

![](https://i.imgur.com/u0JnloR.png)

- 計算時間度雜度
    - 使用 Recursion trace (遞迴跟蹤) 分析
        - 單個遞迴的複雜度為 O(1) 
        - 調用下個遞迴實例的代碼不用考慮進複雜度中
        - 遞迴個數如下圖，有 1+2+4+...+(2^logn^) = 幾何級數個 (與末項同階) = O(2^logn^) = O(n)
        - 總複雜度為 O(1) * O(n) = O(n)
        - 從圖中也可看出底層針對每個 n 皆有一個實例
        - ![](https://i.imgur.com/vhFBaHu.png)
    - 使用 Recurrence (遞迴方程) 分析
        - T(n) = 2 * T(n/2) + O(1)
        - T(1) = O(1)
        - ![](https://i.imgur.com/ochwKK3.png)


### Note-1.3
> 計算單個遞迴實例的複雜度時，要將 **調用下個遞迴實例** 的代碼隱蔽起來，不併入計算中

### 優勢
#### 實例 Max2
- 從區間 A(lo~hi) 中找出最大的兩個數 A[x1], A[x2]，並花上最少的**比較次數**
- 方一：for loop
    - 掃描一次找到最大的 A[x1] // n-1 次
    - 掃描 lo ~ x1-1，找出最大的潛在 A[x2] // x1-lo-1 次
    - 掃描 x1+1 ~ hi，找出真正的 A[x2] // hi-x1-1 次
    - 共花 2n-3 個比較次數，不論情況好壞
- 方二：for loop
    - 先將 x1 指向 lo, x2 指向 lo+1，如果 A[lo] > A[lo+1]，則交換 // 1 次
    - 從 i = lo+2 ~ hi, 逐一將 A[i] 與 A[x2] 比較 // n-2 次
        - 如果比 A[x2] 小則 i+1
        - 如果比 A[x2] 大則 x2 指向 i
            - 再將 A[x2] 與 A[x1] 比較，如果 A[x2] > A[x1] 則交換 // n-2 次
    - 最好情況為 1+ 1*(n-2) = n-1次，
    - 最壞情況為 1+ 2*(n-2) = 2n-3 次，與方法一相同
- 方三：divide and conquer
    - 如果只剩兩個數，需要比較1次
    - 如果只剩三個數，需要比較3次
    - 左側的最大、次大跟右側的最大、次大，只需要經過 2 次比較就可以找到綜合的最大、次大
    - 此方法所需步驟為 5/3*n + 2 比起之前的方法，所需的比較次數減少了
    - ![](https://i.imgur.com/gVU4jVC.jpg)
## 遞推方程
![](https://i.imgur.com/HyerJJI.png)

## 動態規劃
- 遞迴：可以做到 make it work, make it right, 但不一定能做到 make it fast
- 迭代(loop)：可以做到 make it fast
- 動態規劃：先透過遞迴找到算法本質，得到初步解之後，再等效的轉換成迭代

### Fib
- 遞迴解
    - 超慢
    - 因為時間複雜度是 O(2^n^) !
    - 僅算出 fib(67) 就需要花上 10^5^ sec = 1 day
    - 僅算出 fib(92) 就需要花上 10^10^ sec = 300 年！
    - 很慢的原因：每個遞迴實例都被大量的重複調用（計算），這樣不僅耗時也耗空間
    ![](https://i.imgur.com/aavHneE.png)
- 改進方法 A - 記憶解
    - 方法同上，但要將已經計算過的結果記錄下來，減去重複計算的麻煩
- 改進方法 B - **動態規劃**
    - 從下而上計算，例如要算 fib(6)，就從 f(0) 一路算到 f(6)
    - 若要計算 f(n), 每次都要記憶 f(n-1) 及 f(n-2) 的數值
    - 只需要 O(n) 的時間，O(1)的空間
    - ![](https://i.imgur.com/YIvh9ua.png)

### 最長子序列 LCS (Longest Common Subsequence)
- 例如 `data` 是 `educational` 跟 `advantage` 的最長公共子序列
- 可能有多個解, 例如 dana 也是 educational 跟 advantage 的最長公共子序列
- 如何計算序列 A[0,n] 和 B[0,m] 之 LCS(A,B)?
- 遞迴解
    - 比較兩個序列的最後一個字符 
        - if A[n] = B[n]，則答案為 LCS(A[0,n-1], B[0,m-1]) + 1 // decrease and conquer
        - if A[n] != B[n]，則答案為 LCS(A[0,n-1], B[0,m]) 跟 LCS(A[0,n], B[0,m-1]) 中較長的那一個
    - 遇到 base case
        - 若 n = -1 或 m = -1，則回傳空字串 ""
    - 效率
        - 效率是 O(2^n^)，很不佳
        - 跟 fib 的情況類似，會有許多重複計算的問題
- 動態規劃
    - 顛倒計算方向，從 LCS(A[0], B[0]) 出發，依次計算所有實例，每個實例僅需要計算一次
    - 只需要 O(m * n) 時間即可以計算出所有子問題

### Note-1.4
> 遞迴：容易設計出可行且正確的解，然而效率時常不佳
> 動態規劃：消除重複的計算，提高效率

## ADT 與 DS
### ADT (Abstract Data Type)
- 抽象數據類型 = 數據模型 + 定義在該模型上的一組操作
- 特性
    - 抽象的，只有一種定義
    - 不考慮時間複雜度，不涉及數據的儲存方法
    - 只定義操作及語義、外部的邏輯特性

### DS (Data Structure)
- 資料結構 = 基於某種特定**語言**，**實現 ADT** 的一套算法
- 特性
    - 是一種具體的實現，有多個實現的方法
    - 與時間複雜度密切相關，要考慮數據的儲存機制
    - 定義完整的算法、內部的表現與實現

# 第二章 向量 Vector
## 向量
- 向量是 c++ 中的 array 的抽象與泛化
- 由一組元素按照線性封裝而成
## 向量 ADT
![](https://i.imgur.com/6DsWCB1.png)
## 向量 DS
- 模板 template
    - ![](https://i.imgur.com/i7Nlkjm.png)
    - T 之後可以帶入各種類別，例如 int, float, char... 或自定義的類別如 student, bintree 等等
- constructor
    - 包含基本的 constructor
    - 複製 array 的 constructor
    - 複製 vector 的 constructor
    - ![](https://i.imgur.com/TraS84m.png)

- destructor
    - ![](https://i.imgur.com/5BTVu1H.png)

- private member
    - size 記錄向量的長度
    - capacity 紀錄向量的容量
    - elem 向量內部儲存的數據區 (也是個 array)
- **load factor = size/capacity**
    - 當 load factor 過小，代表此 vector 下溢 underflow
    - 當 capacity 不夠，代表此 vector 發生上溢 overflow
- 如何達到**動態調整** capacity
    - 即將發生overflow時，宣告一段新的空間，大小為原空間的**兩倍**，再將原本的空間釋放
    - 固定內存空間擴容：
        - 每次overflow時，只宣告一個(原空間+常數)的空間，會導致結構的效率下降。
        - 規模為 n 的向量總耗時為O(n^2^)，分攤每次插入元素的複雜度是 O(n)。
        - 空間使用率較高
    - 加倍式擴容：
        - 每次overflow時，宣告新的空間為原空間的兩倍。
        - 規模為 n 的向量總耗時為O(n), **分攤每次插入元素的複雜度是 O(1)**。
        - 空間使用率較低
    - ![](https://i.imgur.com/utVpksO.png)

## 無序向量
- 沒有排序，或是無法排序的向量
### get and put
- get() and put():
    - 目前已經有這兩個方法來對向量進行讀寫，但這種方法不直觀
    - 如果想要使用[] 符號，達到用 myVector[i] 的操作，則需要 overload [] 的符號定義
    - ![](https://i.imgur.com/IDVl16h.png)
### insert
- insert(): 依照**由後而前**的順序將插入的 index 之後的元素向後移動
    - ![](https://i.imgur.com/MdYxx7L.png)

### remove
- 區間 remove()：依照**由前而後**的順序將 hi 之後的元素覆蓋過 lo
    - ![](https://i.imgur.com/zYLkcql.png)

- 單一元素 remove()：為區間remove 的一個特例
    - 若反過來，先實現單元刪除，再利用單元刪除來實現區間刪除，實質上的可行的，但效率會**大大下降**，因為會多出很多次不必要的**前移**動作
    - ![](https://i.imgur.com/3luX9L8.png)

### find
- 查找 find()
    - 在無序向量中，由於無法排序，因此實作 == 或 !=
    - 在有序向量中，則可以實作 > 或 <
    - 算法：由 hi 開始一路比較每個元素到 lo，如果找到相等的元素則返回該元素的 index，如果都找不到則返回 (lo-1) 的 index，由調用者來判定查找是否成功。由於判斷方向由 hi 至 lo，因此若有多個目標元素，則會回傳 **index 較高者**
    - 查找是一個 **輸入敏感** (input-sensitive) 的演算法：因為其最好情況是 O(1) 馬上找到，最壞的情況是 O(n)，相差懸殊
    
### deduplicate
- deduplicate()
    - 從第二個元素(i=1)開始，針對每個元素 e ，只對 e 前面的那些元素 (prefix) 執行 find(e)，如果有找到則 remove (e)，沒有找到就再看下一個元素
    - 如何證明正確性：
        - 不變性：在當前元素 A[i] 之前的元素 A[0, i-1] 中必彼此互異
            - 透過歸納法證明不變性
            - base case：i = 1 時必成立
            - 設 i = k 時，前 [0, k-1] 互異
            - 若 find(i) >= 0，則我們會 remove(i)，此時 i 之前的元素 [0, k-1] 依然互異。
            - 若 find(i) < 0 ，則 i 變成 i +1，可以確定前綴元素[0,k] 都互異
            - 當 i 在最後一個元素時，可以保證所有元素都彼此互異
        - 單調性：當前元素的**後綴**長度單調下降，遲早變成 0 (i 遲早到達最後一個元素)，算法必然終止
    - 時間複雜度：
        - 內層：每次 find + remove 累計消耗 O(n)
        - 外層：i 的 while 循環 O(n)
        - 總體為 O(n^2^)
    - ![](https://i.imgur.com/hviRoCv.png)
### traverse
- traverse()
    - ![](https://i.imgur.com/HwQY7Wp.png)
    - 之後只要定義一個操作函數，如 increase，就可以透過以下方法實現
    - ![](https://i.imgur.com/5JAfsyX.png) 

## 有序向量
- 元素之間可以比較大小且已排序
### uniquify (重要)
- uniquify () 剔除重複元素
    - 因為有序向量有排序，所以相同的元素必定會緊鄰在一起
    - 可以成批的刪除重複向量
        - i, j 都初始在頭，j 一直往後直到當發現與 i 不相等的第一個元素時，直接將那個值複寫 i+1 ，並將 i++ 後繼續
    - 最後甚至**不需要執行任何刪除動作**，因為我們可以透過**size 的縮小**來達到刪除的效果
    - **為高明的刪除技巧！**
    - 時間複雜度為 **O(n)**
    - ![](https://i.imgur.com/JZS3VsI.png)

### search
- 接口定義
    - 在有序向量中，使用 search(e) 時應該返回 **不大於e的最後一個元素** 的 index
    - 如此可以方便向量的維護，例如可以透過 `v.insert(1+v.search(e), e) ` 來進行插入，且不會破壞順序
    - 如果插入一個重複的值，新插入的值也會被放在舊的值後面
#### binary search -1
- binary search - 版本 1
    - 時間複雜度為 **O(logn)**
    - 細微一點考察，時間複雜度大約是 O(1.5 * logn),是可以被改進的
    - 因為 binary search **向左側**搜尋的成本跟**向右側**搜尋的成本不一樣
        - 向左側只需要一次比較 (一個if)
        - 向右側則需要兩次比較 (兩個if)
        - 希望可以有多一點數據在左側 -> fib search
        - 下面 code 尚未符合接口定義
    - ![](https://i.imgur.com/vI8SQIm.png)
#### fib search
- fibonacci search
    - 概念跟 bin search 相同，只差在取 mi 的方式不同
        - mi = (lo+hi)/2 就是 bin search
        - mi 按照黃金切割(fib)就是 fib search
        - 可以證明按照 fib 的方式切分的 mi，是比其他任何方式都好的
    - fib search 的複雜度在常數意義上會比 bin search 好
    - 時間複雜度為 O(1.44 *  logn)
    - 下面 code 尚未符合接口定義
    - ![](https://i.imgur.com/MNjvcE9.png)
    - ![](https://i.imgur.com/dCOZkPm.png)
    - ![](https://i.imgur.com/JBZtccM.png)
#### binary search -2
- binary search 版本 - 2
    - 試圖讓 binary search **向左側**搜尋的成本跟**向右側**搜尋的成本一樣
    - 原本是比較 e < mi, e > mi 兩次，並把元素區分為三段(lo~mi-1, mi, mi+1~hi)
    - 現在只比較一次 e < mi, 並把元素分成兩段 (lo~mi-1, mi~hi)
    - 只有當 hi-lo = 1 時，才會判斷該元素是否命中
    - 相對於版本 1 
        - 最好的情況表現：版本2較版本1差 (第一次的 mid 就命中)
            - 版本 1 為 O(1)
            - 版本 2 為 O(log~2~n)
        - 最壞的情況表現：版本2較版本1好 (查找元素在最右邊)
    - ![](https://i.imgur.com/a3OxrFc.png)
#### binary search -3
- binary search 版本 - 3
    - 改進 binary search 版本 2，使其符合接口
    - ![](https://i.imgur.com/HnEc3fy.png)

    - 證明版本3的正確性
        - 不變性：**A[0, lo) <= e < A[hi, n)**
        
            - ![](https://i.imgur.com/Kco2Hn1.png)
            
            - 當 e < A[mi], hi 變成 mi， A[0, lo) <= e 依然成立，而 hi 中最小的 mi 都 > e，故 e < A[hi, n) 成立
            - 當 A[mi] <= e, lo 變成 **mi+1**，既然比 lo 都大的 mi 都 <= e，故 A[0, lo) <= e 成立，原先的 e < A[hi, n) 也依然成立
            
            - ![](https://i.imgur.com/6xM1TVT.png)
        - 單調性：最後區間寬度會變成 0 (lo == hi)
        - 正確性：當 lo == hi 時， A[0, lo) <= e < A[lo, n)，故 **lo-1** 就會符合接口，為**不大於e的最後一個元素** 的 index

#### Interpolation Search
- Interpolation Search 插補搜尋法
    - [資料](https://ithelp.ithome.com.tw/articles/10207069)
    - 假設資料的**值**跟他們的**index**是有**大致的線性關係**
    - 所以每次可以動態決定 mi (有點像插值法)
    - ![](https://i.imgur.com/POApiRN.png)
    - 時間複雜度
        - 最壞情況：比其他來得低，只有 O(n)，會出現在資料不如預期分佈時
        - 最好情況：跟其他算法一樣，為一次命中，O(1)
        - 平均情況：(經過論證)每經一次比較，**n 縮減為 n^(1/2)^**，故平均時間複雜度為 **O(loglogn)** ，小於其他算法的 O(logn)
        - 雖然平均情況表現較好，但其優勢不明顯，除非區間 n 極大。且每次計算 mi 都需要乘法跟除法的計算，比起其他算法來得複雜
    - 綜合應用
        - ![](https://i.imgur.com/wx1G2cl.png)

#### Note-2.1
> 如果某算法每次會將 **n 縮減為 n^(1/2)^**，則其時間複雜度為 **O(loglogn)**

> 極大規模：先用 **插補查找** 縮小問題規模
> 中規模：用 binary search、fib search
> 小規模：用順序查找
> 

## Sorting 排序 
- 有序向量比起無序向量，在**查找**跟**去除重複**上都更高效率
### 排序演算法的穩定性
- 輸入有**重複元素**時，輸出的順序是否保持不變
    - ![](https://i.imgur.com/81DBbEj.png)

### bubble sort
- 原始版
    - 若長度為 n 的向量，從頭掃描到尾，兩兩元素比較大小，若順序相反則交換
    - 每次掃瞄最大的元素都會歸位
    - 掃描到某次都不需要交換為止，便停下。最壞需要掃描 n-1 趟
    - 時間複雜度 O(n^2^)
    - ![](https://i.imgur.com/40G8lXK.png)
- 改良版
    - 假設經過幾次掃瞄交換後(或初始時)，有一段**更長的後綴**已經有序，則不需要浪費時間排序
    - 原始版只紀錄**是否已經完全排序**，改良版則持續紀錄**最後一個交換的index**，如此該 index 之後的元素都已經是有序的，可以縮短排序的長度
    - 雖然最壞仍是 O(n^2^)，但平均來說表現會比原始版好很多
    - ![](https://i.imgur.com/jcV8PP7.png)
- 穩定性
    - bubble sort 是**穩定的**
    - 因為我們只在 A[i] > A[i+1] 時才會交換

### merge sort
- 演算法效能：不論好壞都是 **O(nlogn)**
    - T(n) = 2 * T(n/2) + O(n)
    - 長度為 n 的排序複雜度 = 解決兩個長度為一半的子問題的複雜度 + 合併子問題的複雜度
    - T(n) = O(nlogn)
    
![](https://i.imgur.com/nqXqkHT.png)


- merge 算法
    - ![](https://i.imgur.com/7xklC7Q.png)
    - 只需要額外的一段子空間(B)，另一段則不需要額外的空間 `(C)`，可以直接在原向量(A)中操作，並可以確保 C 中的元素不會被覆蓋
    - 將 B 跟 C 的首元素兩兩比較，較小的就填入 A 中
    - 可以將 merge 算法更精簡，因為 C 中的元素原本就在 Ａ中，所以當 B 中的元素都已經進入排序後，C 中剩餘的元素不需要再進行複製的動作
    - merge 算法中，累計只需要線性 O(n) 的時間
    - ![](https://i.imgur.com/86J0xdT.png)

### Note-2.2
> 當 T(n) = 2 * T(n/2) + O(n), T(1) = O(1)
> 則 T(n) = O(nlogn)
> 

> 當 T(n) = 2 * T(n/2) + O(1), T(1) = O(1)
> 則 T(n) = O(n)

# 第三章 列表 Linked List

## Linked List vs Vector
- vector 
    - 是靜態的資料結構
    - 適合執行靜態的資料操作：**get()** 只需要O(1), **search** 只需要 O(logn)
    - 不適合動態的資料操作：**insert()**, **remove()** 都需要 O(n)
- linked list
    - 是動態的資料結構
    - 適合執行動態的資料操作
- ![](https://i.imgur.com/sZm9GUQ.png)

## Linked List
![](https://i.imgur.com/6JhEuoh.png)

### position
- 在 vector 中
    - 我們會稱 index 為秩
    - **循秩** (call-by-rank) 非常快，為 O(1)，V[i] = V + i * s，s 是單個元素所佔的空間 (如 4 byte)
- 在 list 中
    - 由於物理上沒有直接相鄰，而是靠 node 之間的互相引用(pointer) 來串接，因此稱為 **循位置** (call-by-position)

### ADT
#### Node
![](https://i.imgur.com/RkjRHfa.png)

#### Linked List
![](https://i.imgur.com/xPzTiW0.png)


### 實現
- list 中
    - 必有 header 跟 trailer 兩個 node，用來標記 list 的頭尾，他們對外是不可見的
    - 對外可以見的 node 中，第一個叫 first，最後一個叫 last
    - header 跟 trailer 必定存在，且兩個互異
    - first 跟 last 不一定存在(如果是空的 list)，且兩者可能回同一個 node (只有一個 node 時)
    
![](https://i.imgur.com/O5GzfyZ.png)

![](https://i.imgur.com/sYpjJDS.png)

## 無序列表
### call by index 
![](https://i.imgur.com/f0VTC7F.png)

### find
- 算法跟 vector 相似，ㄧ樣由後往前查找
- 接口 find(e, n, p) 代表在 p 這個節點的前 n 個節點中，找尋最後的 e
- 複雜度為 O(n)
![](https://i.imgur.com/MnsHHuu.png)

### insert
- insertBefore(p, e)：在節點 p 之前插入一個節點 e
- 複雜度為 O(1)
- 在 linked-list 上實現 insertBefore，並使用 node 的函數 insertAsPred
    - ![](https://i.imgur.com/5Isj1e6.png)

- 在 node 上實現 insertAsPred
    - 其中 new ListNode(e, pred, this) 直接創造一個 node，data 為 e，前面指向 p 的前面，後面指向 p (this)
    
    - ![](https://i.imgur.com/08BpdDm.png)

### copyNodes
- copyNodes(p, n)：從節點 p 開始，複製往後的 n 個 node 到新的 list 中
- 在 linked-list 上實現 copyNodes
    - init() 只會創造 header 跟 trailer node
    - 接著陸續將每個節點 **插入新 list 的最末端** (insertAsLast)
    - insertAsLast 的實現即為 **insertBefore(trailer, e)**，即在 trailer 之前插入節點
    - ![](https://i.imgur.com/llyHMAy.png)

### remove
- remove(p)：刪除 p 節點
- 複雜度為 O(1)

![](https://i.imgur.com/u9wJWfx.png)

### destructor
- 刪除整個 linked list]
- 複雜度為 O(n)
- 先將對外可見的 node 刪除 (first~last)
    - 使用 remove，每次都刪除 first
- 再將外部不可見的 header, trailer 刪除

![](https://i.imgur.com/AF0LBvP.png)

### deduplicate
- 跟 vector 的原理一樣
- 從第二個 node (i=1) 開始，針對每個 node 的 data e ，對 e 前面的那些 node (prefix) 執行 find()，如果有找到則 remove 當前的 node，沒有找到就再看下一個元素
- 正確性的證明在 vector 中
- 時間複雜度 (跟 vecotr 一樣)：
    -   內層：每次 find + remove 累計消耗 O(n)
    -   外層：while 循環 O(n)
    -   總體為 **O(n^2^)**

![](https://i.imgur.com/95F2hgk.png)

## 有序列表
### uniquify
- 可以採用較直觀的算法，因為 linked list 中的 remove 是 **O(1)**
    - 只保留每個相等元素區間的第一個 node
    - 兩個 pointer p 及 q 分別指向 list 的 head 及 head 的下一個 node
    - 如果 q 與 p 的 data 相同，則 remove(q)，q  指向下一個 node
    - 如果 q 與 p 的 data 不同，則 p, q 都指向下一個 node
- **複雜度為 O(n)**
![](https://i.imgur.com/HZXkucK.png)

### search
- 由於無法透過 index 直接訪問，所以儘管已經排序，但還是得要逐一訪問
- 由右而左訪問，返回不大於 e 的最後者
- 複雜度是 O(n)，跟無序 list 一樣
- ![](https://i.imgur.com/FhYTgJM.png)

## Sorting 排序
### selection sort
- 每次都從未排序的區域中，選擇(select)出**最大**的元素，將其納入以排序區域中
- bubble sort 也是 selection sort 的一種
    - 因為 bubble sort 每個循環也是將最大的元素歸位
    - 但 bubble sort 效率太低 O(n^2^)
    - bubble sort 效率低的原因在於，要透過兩兩元素的交換，才能將最大的元素擺在後面
    - selection sort 則改進了 bubble sort 的缺點，每次循環中只會做一次 **交換** 的動作
- selectionSort(p, n): 對 p 開始的連續n個元素做排序
    - 下面算法中，會在未排序期間中找出最大的元素 M (selectMax)，將其從原本的位置刪除(remove)，再放進已排序區(insertBefore(tail))
    - 儘管邏輯是對的，但實際上操作 insert 跟 remove 時，會有 new 跟 delete 的操作，這兩類操作十分耗時，應該避免
    - 故進一步的改進方法為：將 M 跟 tail 前面的 node 互換位置，用 swap 的概念取代 remove + insert 的概念
    - ![](https://i.imgur.com/X8GMCbq.png)
- selectMax
    - 從前而後完整掃瞄過一次 list，最時紀錄當前最大的數值
    - 又稱為 painter's algroithm
        - 因為紀錄當前`最大數`的變量會一直被覆蓋過去
    - 如果有大小相同的 element，我們應該要找位置在**後面的**，藉此保證算法的**穩定性**(相同大小的元素，排序前跟排序後的相對位置不變)
        - 由於現在用 list 實作，無法由後而前掃瞄（無法找到 p+n 那個 node）
    - ![](https://i.imgur.com/cXT9JTX.png)
- 時間複雜度：**O(n^2^)**，不分好壞 
    - 總共迭代 n 次
    - 在第 k 次的操作中，selectMax為 O(n-k)
    - remove 跟 insert 均為 O(1)
    - 由於最好最壞都是 O(n^2^)，所以也可以寫 **theta(n^2^)**
    - 儘管和 bubble sort 的複雜度ㄧ樣，但實際上元素 **移動** 的操作遠遠少於 bubble sort (因為 bubble sort 要一直 swap)
    - O(n^2^)主要來自 **比較** 操作


### insertion sort
- 就跟整理撲克牌一樣
- 取出未排序的第一個元素，插入已排序的元素中的適當位置
- ![](https://i.imgur.com/0vcAw5E.jpg)
- 與 selection sort 的差別
    - selection sort 已排序的部分在後面，未排序在前面。 insertion sort 則相反
    - selection sort 未排序的部分中，每一個元素必小於已排序的所有元素。insertion sort 則沒有限制

- insertionSort (p, n):對 p 開始的連續n個元素做排序
    - 藉由 search(p->data, r, p) ，以 p 之前 r 個元素為區間，找到不大於 p 的最大元素
    - 再使用 insertAfter，在適當的位置插入一個 node，值為 p 的 data
    - 之後再 `remove(p)`
    - 僅使用 O(1) 空間，為一種 **in-place algroithm 就地演算法**
    
![](https://i.imgur.com/NUjWoyP.png)

- 時間複雜度
    - ![](https://i.imgur.com/t5riYJQ.png)
    - 是一個**輸入敏感** (input sensitive) 的演算法，因為表現好壞相差懸殊，跟輸入有很大的關係
    - 跟 selection sort 不同，selection sort 不論好壞都是 O(n^2^)
- 平均性能：
    - 使用 Backward analysis
    - 我們發現期望的**平均性能仍是 O(n^2^)**，因為僅管 insertion sort 在最好情況是 O(1)，但其發生機率極低
    - ![](https://i.imgur.com/LDZZjBP.png)

- 如果用 vecotr 實現 insertion sort
    - 在比較階段，可以改用 binary search 或其他的 search 方法，只需 O(logn)
    - 但在 insert 時，需要付出 O(n) 的代價
    - 總體仍需要 O(n^2^)，最壞時 O(n^2^)，最好時 O(n)

## Vector 與 List 之效能
![](https://i.imgur.com/CFezx6t.png)
## Note-3.1
> Vector 中的 call by rank，就跟 RAM 模型相等，可以用 O(1) 的時間訪問到某個 index

> Linked-list 中的 call by position，就跟圖靈機(TM) 模型相等，每次只能在當前節點上向左/向右


# 第四章 Stack and Queue
## Stack
- LIFO: Last In First Out
- 只能在 top 端進行操作

### 基本接口
- push：從 top 塞入一個 element
- pop：從 top 拿出一個 element
- top：查看 top 的 element
- size
- empty

### 實現
- stack 是 vector 或 linked-list 的特例
- 可以直接由 vector 或 list 定義 stack
- 由 vector 定義 stack
    - 定義 vector 的**末端**是 stack 的 top 
    - **不論是 pop, push , top 都只需要 O(1) 的時間！**
    - 如果我們將 vector 的 **首端** 定義為 stack 的 top，則 pop 跟 push 的時間複雜度都會提升至 O(n)
    - ![](https://i.imgur.com/tYF4rlv.png)

### Stack 的應用
#### 典型應用場合
![](https://i.imgur.com/JuPrqpz.png)

#### 逆序輸出 - 進位法的轉換 conversion
- 例如 將 2019 (10進位) 轉換成 2 進位
- 作法：將 2019 不斷除以 2，紀錄餘數，並將餘數的結果 **相反順序輸出** 即為答案
- 適合使用 stack

![](https://i.imgur.com/ZzhXDZT.png)

#### 遞迴嵌套 - 括號匹配 parenthesis
- 判定一段程式碼是否有匹配的括號表達
- 可以利用 stack 的特性
    - 遇到左括號則 push
    - 遇到右括號則 pop
    - 其餘字符忽略

![](https://i.imgur.com/rUNig9C.png)

- 為何用 stack ：
    - 如果只判定一種符號 (如小括號)，其實不需要用到 stack，直接用一個 counter (int) 來取代 stack 就好。當遇到左括號則 counter ++，右括號則 counter - 1，效果跟 stack 完全一樣
    - 但 stack 可以方便判斷**多種符號**，且這個是使用多個 counter 辦不到的。例如 `[(])` 就是一個非法的程式碼，用 stack 可以輕易判斷出錯誤，但用兩個 counter 無法
    - 如此可以用 stack 輕易檢查 HTML tag 是否匹配

#### 遞迴嵌套 - stack permutation
- 與上面的`括號匹配`有點類似
- ![](https://i.imgur.com/xScbgTE.png)
- SP(n)：stack permutation 的總數
    - 一個長度為 n 的 stack ，所能排出的 stack permutation 種數，定義為 SP(n)
    - **SP(n) = sigma( SP(k-1) * SP(n-k) )**, k 從 1~n , 又 SP(1) = 1
    - 上述為著名的 **Catalan(n)** 卡特蘭數，為 **SP(n)=(2n)! / [(n+1)! * n!]**
- 判別：某一個排列是否為 stack 的 sp 呢
    - 對於任何在原始 stack 中的元素順序 1 <= i < j < k <=n，則 [...., k,...., i,...., j,...] 必然不是其 sp
    - 例如：原始 stack 中為 [ 1,2,3 ]，則 [3, 1, 2] 不是他的 sp (開口端在右邊)
    - 且經證明，只要一個 stack 中不存在上述的逆序，則他就是一個合法的 sp
    - 且經更近一步的證明，可得以下結論
    - ![](https://i.imgur.com/MalEZVm.png)
    
    - **故最快的方法是：直接創 A,B,S 三個 stack，模擬看看是否可行**
- stack permutation 與 括號匹配：
    - n 個元素的 sp 有多少種 = SP(n) = n 個括號能構成的合法表達式
    - ![](https://i.imgur.com/69DWmrL.png)

#### 延遲緩衝 - 中綴表達式求值 evaluation
- 表達式求值

![](https://i.imgur.com/rd0706d.png)
- 構思：
    - 當線性掃描過表達式時，某些符號還無法判斷是否可以操作 (例如 +-)，某些符號則可能可以優先操作(* /)，所以需要用 stack 來做 buffer
    - 每個運算符都需要遇到下個(優先權較低)的運算符後，才能操作
    - 需要時做一張運算符的**優先順序**表格
    - 為了方便，應該將數字跟運算符分為兩個 stack 操作
- 中綴表達式 infix：運算符在中間，例如 1+2
- 算法
    - 其中 readNumber 的功能：因為讀入時都是單一 char，當連續遇到兩個數值時，計算出真正的大小（將前面的 * 10 + 後面的）。例如連續遇到 8,9，那代表應該是 89，因此會計算 8 * 10 + 9 再放入 stack 中

![](https://i.imgur.com/TE7BM7E.png)

![](https://i.imgur.com/z96v2b6.jpg)

#### 棧式計算 - RPN 逆波蘭表達式
- RPN： reverse polish notation
- 是一種後綴表達式 postfix
- 延續上面的 `中綴表達式求值` 可以發現需要搭配 運算符(加減乘除等) 跟括號，才能表達出優先順序。但若要用程式計算就頗麻煩
- RPN 是一種**不使用括號** 即能表達優先順序的方法，且一般的表達式都能轉換成 RPN。計算起來非常**高效率**
- 如果一個中綴表達式需要重複計算，可以先處理成 RPN，提升之後計算的效率
- 方法：
    - 只需要一個 stack 來儲存數值
    - 一看到運算符就(從 stack拿出數值)馬上運算，運算完再放入答案回 stack 中
    - 完全不需要查表 (運算符的優先順序表)
##### 轉換成 RPN：infix to postfix
- 手工轉換
 ![](https://i.imgur.com/N4rh0RA.png)
- 可以發現：**運算符**的位置可能與原先**不同**、但數值的位置一定會維持相同
- 也就是說，所有`數值`在 **RPN表達式 跟 infix表達式的順序一致**
- 算法：即為 **infix 中綴表達式的 evaluate** 算法稍加修改(還是要查表)

 ![](https://i.imgur.com/nM5RZLi.png)

## Queue
- FIFO: First In First Out
- 只能在 bottom 端 (尾端) 進行插入 /查詢 : enqueue() / rear()
- 只能在 top 端 (頭端) 進行刪除 /查詢 : dequeue() / front()

### 基本接口
- enqueue：從 bottom 塞入一個 element
- dequeue：從 top 拿出一個 element
- reat ：查看 bottom 的 element
- top：查看 top 的 element
- size
- empty

### 實現
- queue 是 vector 或 linked-list 的特例
- 可以直接由 vector 或 list 定義 queue
- **由 list 定義 queue**
- **不論是 enqueue, dequeue, reat, top 都只需要 O(1) 的時間！**

![](https://i.imgur.com/1T589uA.png)

# 第五章 Tree
- 之前介紹的結構都是 **線性的**： vector, list, stack, queue
- 如先前所說：
    - vector 擅長靜態操作 (search) 但不擅長動態操作 (insert, remove)
    - list 不擅長靜態操作 (search) 但擅長動態操作 (insert, remove)
- Tree：**擅長動態及靜態操作**
- 可透過遍歷的次序 (preorder, inorder, postorder) 轉換成線性
- 可以理解為**二維的 List**
- ![](https://i.imgur.com/hWtKp4K.png)

## 特性
- 樹是一種圖的特例
- vertex 是圖中的**頂點**, edge 則是連結節點的**邊**
- vertex 不等於 node，但 vertex 在計算機中是以 node 的形式被表示
- 有根樹：有 root

![](https://i.imgur.com/UPWoaBd.png)


- 有序樹：有定義不同 sibling 之間的順序的樹

![](https://i.imgur.com/XbSeowh.png)

- 可證明樹的 edge 數量 = node 數量 -1 = O(n) 
    - 衡量複雜度時，以 node 量 n 為參考
- 樹與圖的差別
    - 樹是連通的 (每個 node 都有辦法(通過1或1個以上的 edge)到達另一個 node)
    - 樹是無環的 (不存在 cycle, loop)
    - 綜合：**任一節點 v 與根 r 之間存在唯一路徑**，path(v, r) = path(v) = v 到 r 所需的 edge
 
 - 深度與層次
     - 每個節點 v 都必然有唯一的父
     - 每個節點 v 未必有唯一的子
     - 每個 node 都有高度跟深度
         - **深度**：由 root 節點**往下**數到該 node 的 edge 數量
         - **高度**：由該（子）樹的 leaf **往上**數到該樹的 root 的 edge 數量
             - 只有一個 node 的樹的高度是 0
             - 空樹(null) 的高度是 -1
     
 ![](https://i.imgur.com/w44AuOq.png)
 ![](https://i.imgur.com/GGvGBqJ.png)


## ADT
![](https://i.imgur.com/pNNyRXF.png)

## 表達法
- 長子兄弟法
    - 每個 node 都只需要紀錄兩個 pointer，即為他的 **firstchild**, 及 **nextsibling**
    - 如此的方法可以讓每個 node 都紀錄ㄧ樣個數(2個)的 node , 為 O(1)
    - ![](https://i.imgur.com/yesoFJr.png)

## Binary Tree
- 是一種特例的樹，但卻可以用來表示**所有的樹**
- 每個 node 度數不超過 2 (可能為 0, 1, 2)
- 每個 node 的孩子分為 leftchild, rightchild
- 深度為 k (層)的 node, 至多 2^k^個

![](https://i.imgur.com/Yt5IeJd.png)

- full binary tree
    - 所有internal node都有兩個subtree(也就是兩個child pointer)
    - 所有leaf node具有相同的height。
    - n = 2^h+1^ -1
    - ![](https://i.imgur.com/LQiFaeQ.png)

- complete binary tree
    - 若一棵樹的node按照Full Binary Tree的次序排列(由上至下，由左至右)，則稱此樹為Complete Binary Tree。 
    - ![](https://i.imgur.com/Jwqbi5Y.png)

- proper binary tree 真二元樹
    - 每個 node 度數都是偶數 (可能為 0, 2)

### 用 Binary tree 表示 多叉樹
- 將一般的樹用 `長子兄弟表示法` 表示後
    - 每個 node 都會有 firstChild()、nextSibling()
    - 此時剛好可以對應到 binary tree 的 leftChild(), rightChild()
    - 如此所有的樹都可以用 binary tree 來表達
    - 故 binary tree 是我們討論樹的重點

![](https://i.imgur.com/6hvWK83.png)

## 實現
### Binary Node
- template
    - succ 指的是 inorder 遍歷下的直接後繼，不一定是 left / right child
![](https://i.imgur.com/x1jbem5.png)

- 實現 template
![](https://i.imgur.com/OT5e8K1.png)

    - insertAsLC 跟 insertAsRC 都是 O(1)
    - size 需要遍歷後面的所有 node，為 O(n)

### Binary Tree
- template
    - 其中 updateHeight 是一個 virtual 方法
    - 因為不同的 binary tree 家族(紅黑樹..等等) 對於 updateHeight 可能有不同的實作方法，所以留給不同類別的Btree去實現
    
![](https://i.imgur.com/LFPV4eQ.png)

- 實現 template
    - updateHeight
        - 一般的樹，對於 height 的定義為 leaf 到該 node 的 edge 長
        - 只有一個節點的話，h = 0
        - 空樹(Null) 的話，h = -1
        - 一個 node 的高度 = max (lc的高度, rc的高度) + 1
        
    ![](https://i.imgur.com/f2l1Kwo.png)
    
    - insertAsRC：在樹中某 node 插入 rightChild
    ![](https://i.imgur.com/7glNeBm.png)


## Traverse
- 按照某種次序訪問 bin tree, 每個節點都要恰好被訪問一次
- 可以透過 traversal 將**樹**由半線性轉換成線性結構
- preorder: **V** -> L -> R
- inorder: L -> **V** -> R
- postorder: L -> R -> **V**
- 不管 v 的順序在哪，都是先左再右

![](https://i.imgur.com/SNqc43A.png)

## Preorder

### 遞迴實現
- 但是遞迴太慢了 ，需要改成用 loop 的方式
![](https://i.imgur.com/zQfI1pP.png)

### 迭代 loop 實現
- 觀察 preorder 的規律：
    - 先自上而下訪問左側鍊上的 node，
    - 再自下而上訪問他們的右子樹 (放入 stack 中後進先出)
![](https://i.imgur.com/DC4ML6i.png)


- 程式碼
    - 自上而下訪問左側分支的函數 (把右子樹都放入 stack 中)
![](https://i.imgur.com/EjXqyFO.png)

    - 整個 preorder 的 code
    
    ![](https://i.imgur.com/L8loG11.png)
 
## inorder
- 觀察
    - 最先被訪問的會是**最左邊**的 node
    - 故每個中間的節點都會被放入 stack 中後進先出
    - ![](https://i.imgur.com/VBOLKOL.png)
- 程式碼
    - 自上而下把中間 node 放入 stack的函數
    ![](https://i.imgur.com/YmKzmn8.png)
    - 整個 inorder 的 code
    
    ![](https://i.imgur.com/pSzn2oA.png)

## postorder
- 略過，與 preorder 相似

## 層次遍歷
- 自上而下，自左而右遍歷 **同一深度(同一層)** 的 node
- 之前的三種方式(preorder, inorder, postorder) 都會發生 **後代優於祖先**被訪問的情形
- 層次遍歷則不會，**祖先一定優於後代**
- 為 **圖的 BFS (廣度優先搜尋)** 的一個特例
- 由於嚴格遵守順序性，因此我們應該使用 Queue

- 程式碼
![](https://i.imgur.com/sdfNcwo.png)

## 重構
- 從已知的 preorder 、inorder 及 postorder 來**重建**出樹的樣子

### 一般的 binary tree (不保證為 proper)
- 需要透過 **inorder +  (preorder || postorder)** 來重構
- 無法透過 preorder + postorder 完成重構，因為此時某 node 只有左子樹的情形，會跟他只有右子樹的情形長ㄧ樣，我們無法判定他原先是左還右
- 透過 inorder 搭配 preoder (或 post)，可以透過抓出共同的 root node，來在 inorder 中區分出左樹或右樹
![](https://i.imgur.com/2SzVFfH.jpg)

### proper binary tree 
- 可以透過 preorder + postorder 完成重構，我們可以判定他原先是左還右
![](https://i.imgur.com/VRVEmJi.jpg)


## Note-5.1
> 使用 迭代實現的版本，時間複雜度是線性的 O(n)
> 儘管使用 遞迴的版本，時間複雜度也是線性的 O(n)，但其常係數比迭代**大很多**


# 第六章 Graph

## 基本定義
- 頂點 vertex 共有 n 個
- 連邊 edge 共有 e 個
- 關係
    - adjacency 鄰接關係：頂點與頂點有連接 (v~v)
    - incidence 關聯關係：頂點與邊有連接 (v~e)

- list 跟 tree 都是 graph 的特例
    - list 只有前後節點有連接
    - tree 只有 parent 跟 child 有連接

- 自環：vertex 自己有一條 edge 連到自己 (課程中不討論)

- 有向 vs 無向
    - 無向邊 undirected edge：edge 沒有方向性
    - 無向圖 undirected graph：整個圖的 edge 都沒有方向性
    - 相反為 有向邊 directed edge，及 有向圖 digraph
    - 若 vertex B -> A，則 A 為 head, B 為 tail

- path
    - simple path 簡單路徑：某路徑中不會經過重複的 vertex

- cycle
    - path 的起點跟終點是重合的 (同一個 vertex)

- **DAG**
    - directed acyclic graph
    - 重要
    - 有向無環圖

## 重要的圖
- **Eulerian tour** 歐拉路徑
    - 即為 (一筆畫問題)
    - 為一個 DAG
    - 稱為 **歐拉路徑** 
    - 一條通過圖中所有「邊」的封閉路徑 (一筆畫問題)

- **hamiltonian tour** 哈米爾頓路徑
    - 為一個 DAG
    - 經過每個 vertex 一次 (恰好一次)
    - 一條通過圖中所有「點」的封閉路徑

## 圖的表達
### adjacency matrix 與 incidence matrix
- 使用 adjacency matrix / incidence matrix 來表達一張圖
- adjancency matrix:
    - 為 n * n 的矩陣
    - A(i, j) = 1 代表 vertex i -> j 之間有 edge , 反之為 0
    - 如果 edge 有權重 w， 則可記 A(i, j) = w
    - 如果是無向圖，則 A(i,j) = A(j, i)
    - 無自環：A(i, i) = 0
- incidence matrix:
    - 為 n * e 的矩陣
    - 紀錄第 i 個 vertex 是否與第 j 個 edge 關聯
    - 每個 edge 只會跟 兩個 vertex 關聯

- **我們主要使用鄰接矩陣(adjacency matrix)**

![](https://i.imgur.com/V2NCmB6.png)

- adjacecny matrix 的實例
![](https://i.imgur.com/4zm1LfN.png)

## 圖的實現
### Vertex
- VStatus:有三種，未被發現、已發現、已訪問。初始為未被發現
- dtime, ftime，被發現時間，被訪問完畢的時間
- inDegree, outDegree：指向進來的 edge 數量，指向出去的 edge 數量

![](https://i.imgur.com/JxvbPcS.png)


### Edge
- Etype:有五種，未決定、樹邊、Cross 邊（sibling 之間）、forward （祖先指向後輩）backward（後輩指向祖先）

![](https://i.imgur.com/Wnw2oRO.png)

### Graph matrix (adjacency matrix)
- 基於**鄰接矩陣** (adjacency matrix) 來表達的 graph
- V 頂點集：由一組頂點構成的向量 (長度為 n)
- E 邊集：由一組 「邊構成的向量」所構成的向量 (大小為 n^2^)，即為 **adjacency matrix**, 其中 E[i, j] 指向一個 edge 實例 or NULL

![](https://i.imgur.com/VM7fS9p.png)

### Graph 的操作

#### edge 讀寫
![](https://i.imgur.com/41qalFU.png)

#### edge 插入
- 讓 E[i, j] 指向一個新的 edge 實例
- 再更新對應頂點的 outdegree, indegree

![](https://i.imgur.com/gLVevvQ.png)

#### edge 刪除
- 釋放要刪除的 edge 實例
- 讓 E[i, j] 指向 NULL
- 再更新對應頂點的 outdegree, indegree

![](https://i.imgur.com/fVMsVfE.png)


#### vertex 的讀寫
![](https://i.imgur.com/urjiGqy.png)

#### 相鄰 vertex neighbor 的枚舉
- 複雜度為 O(n)

![](https://i.imgur.com/1W4MwPb.png)

#### 插入新 vertex
- 因為 n 變大，所以 V(頂點集) 跟 E(邊集,鄰接矩陣) 的大小都要調整
- 調整包含以下四個步驟

![](https://i.imgur.com/AsEnIw4.png)

#### 刪除 vertex

![](https://i.imgur.com/mKJZ61D.png)

### 效率 (adjacency matrix 表示法)

#### 優點
![](https://i.imgur.com/rNfRB9X.png)


#### 缺點
- 實際的邊遠遠少於 n^2^，會浪費空間
- 平面圖：不相鄰的邊 **不能相交**
- 可以用**尤拉公式** 表示 PG (平面圖)
    - v 頂點數
    - e 邊數
    - f 平面區域數
    - c 聯通域數
- 由此可以證明，平面圖的 e <= O(n), 但我們卻準備了 n^2^ 的空間 (鄰接矩陣的大小)，空間利用率極低

![](https://i.imgur.com/dFbo37W.png)

## 圖的 Traversal 遍歷與 Search 搜索 
- 先前我們透過 traversal 將**樹**由半線性轉換成線性結構
- 現在我們透過 traversal 將**圖**由非線性轉換成半線性結構（**樹**）

### Breadth-First Search（BFS，廣度優先搜尋）
#### 概念
![](https://i.imgur.com/HLJK0O9.png)

- 遍歷的過程要注意，不要形成**環路**
- 因為如此訪問圖的不同階層的鄰接頂點
    - 故**樹的層次遍歷** 及為其一特例
    - 故**樹的層次遍歷**也稱**樹的廣度優先遍歷**
- 對圖採用 BFS 會構造出一棵 BFS Tree, 也稱該圖的 spanning Tree

#### 實現

![](https://i.imgur.com/wRttBG3.png)


- 其中 u 的狀態分為幾種狀態：
    - UNDISCOVERED ：若 u 尚未被發現，則加入 queue 中，記錄這條邊為**樹邊**，並記錄 u 的 parent (做為 Tree 結構中的 parent)
    - DISCOVERED 與 VISITED：代表 u 已經在 queue 中，所以紀錄這條邊為**CROSS**邊

![](https://i.imgur.com/B514YxD.png)

- 時間複雜度：
    - while 執行 n 次
    - 內層 for loop 掃描 n 次 (但很快)，實質進入 for 中的只有 e 次 (因為有 e 條邊)
    - 用 **Adjacency matrix 鄰接矩陣** 來實現的複雜度看似 O(n^2^)，但其實比較接近 O(n+e)
    - 若改用 **Adjacency List 鄰接表** 來實現，可以直接達到 O(n+e)
#### 多個聯通域
- 一個 graph 中可能有多個聯通域，彼此之間沒有聯通
- 對此，我們只需要在每個聯通域啟動一次 BFS 搜尋
    - 即為檢查圖中所有頂點，並對 UNDISCOVERED 的 vertex 進行 BFS
    
![](https://i.imgur.com/5OkapPh.png)

### 最短路徑問題
- 樹
    - 樹結構中，每個點 v 到 root 的距離定義為 **v的深度** = dept(v)
- 圖
    - 圖結構中，每個點 v 到 起點 s 中的路徑可能有多條
    - v到s的距離 = dist(v, s) 定義為 v到s 最短的路徑，可以簡寫為 dist(v)
    - BFS 中，從 S 開始層次遍歷時 v 的層數，就是 dist(v)，即為 v到s的最短路徑
    - 代表 BFS 樹中， s(root) 到 v 的通路就是最短通路

### Depth-First Search（DFS，深度優先搜尋）
- 比 BFS 複雜但更強大

#### 概念
![](https://i.imgur.com/hCg7L80.png)

- 訪問完會獲得 DFS tree, 也稱該圖的 spanning Tree
- DFS 的模式類似於 binary tree 的 **先序訪問** preorder

#### 實現
![](https://i.imgur.com/fbDov6L.png)

- BFS 可能的狀況

![](https://i.imgur.com/3xp1JBn.png)

- 邊的種類
    - forward：祖先指向後代的邊
    - backward：後代指向祖先的邊，如果有 backward 代表出現循環
    - cross：同輩 (sibling) 中的邊
- 狀態的改變
    - UNDISCOVERED -> DISCOVERED：發現某個 vertex，並訪問該 vertex
    - DISCOVERED -> VISITED：該 vertex 的 DFS 訪問完畢
- 時間標記
    - dtime：該 vertex 開始進行 DFS 的時間
    - ftime：該 vertex 完成 DFS 的時間

#### 時間標記
- 可以很快速的分辨兩 vertex 之間的關係
    - 只需要 O(1)
    - 而不需要從 v1 追溯到 v2

![](https://i.imgur.com/7VeGSUz.png)


#### 多個聯通域

![](https://i.imgur.com/tDrcANH.png)

![](https://i.imgur.com/YqH357A.png)

#### DFS 森林
![](https://i.imgur.com/G5YuQaY.png)

## 應用
![](https://i.imgur.com/GoyZyPg.png)


# 第七章 BST (Binary Search Tree)
## 優勢
- 一維結構無法滿足所有需求，各有缺點
![](https://i.imgur.com/CFezx6t.png)
- binary tree 本身結合 vector 跟 list 的優點，可以當作是2維的list
- BST 繼承了 binary tree 的優勢
- **BST 同時也繼承了 sorted vector 的特點跟優勢**
- 其中 **BBST Balanced Binary Search Tree 平衡二元搜尋樹** 是 BST 中一個充滿智慧的子集



## Entry
- 每個 node 中都有一個 enrty，entry 中會紀錄 key, value
- key 之間可以互相比大小、比較是否相等
- call-by-key

![](https://i.imgur.com/NNPYuPk.png)

## 定義
- 在觀念上，可以簡化 node, entry, key 為一者
- 順序性：**任一節點均不小於/不大於其左/右後代** （所有後代）
- 為了簡化，我們限定 key 為唯一
- 單調：如果以 inorder 遍歷方法，則訪問結果為 **sorted** （單調數列）
- 判斷是否為 BST：**用 inorder 方法** 遍歷一棵樹，如果是單調的就是 BST
- n 個數字最多可以構成 **Catalan number C(n) = (2n)!/(n+1)! * n!** 顆 [BST](https://www.geeksforgeeks.org/total-number-of-possible-binary-search-trees-with-n-keys/) 
## BST 模板
- 用 virtual：因為不同 BST 的實現方式不一樣
- public：繼承 bintree 類別
![](https://i.imgur.com/T8jx8hR.png)

- protected
![](https://i.imgur.com/qBfWA68.png)

## Call-by-key

### Search
- 在 BST 中的 search 過程，其實就是在 **sorted vector** 中的 **binary search**

![](https://i.imgur.com/VucLha8.png)

- 最壞的時間複雜度為 O(n), 因為有可能該樹都只有單邊分支，變成一個一維 list

- hot 的意義
    - hot 會指向剛訪問完的那個非空 node
    - 若 search 成功，hot 就是所找的 node 的 parent
    - 若 search 失敗，hot 就是最後一個訪問的 node
    - ![](https://i.imgur.com/IcUgm5f.png)

- 可以發現，若查找失敗，則 hot 會指向 **該節點應該所在位置** 的 parent，而 search() 會返回**該節點應該所在位置**，如此可以方便 insert 的實現

### Insert
- 假設要 insert 的值目前不存在於 BST 中
- search() 會返回**該節點應該所在位置**，如此可以方便 insert 的實現
![](https://i.imgur.com/1onXANM.png)

- 實現
    - 透過 search 返回該 node 應該所在的位置 pointer
    - 使 pointer 指向一個新 node，key 為 e，父親為 hot
    - 複雜度：為 O(h)，即不超過樹的高度
![](https://i.imgur.com/X6UXhqj.png)

### Remove
- 主算法
- 複雜度：為 O(h)，即不超過樹的高度
![](https://i.imgur.com/5mtGgH6.png)

#### removeAt
要分成兩種狀況處理：只有一顆子樹、有兩顆子樹

- 如果要刪除的 node 只有一顆子樹（只有leftchile or rightchild），則用這個 child 來取代要被刪除的 node

![](https://i.imgur.com/vml6A79.png)

- 如果要刪除的 node 有兩顆子樹，則需要化繁為簡，把狀態換成只有一顆子樹
    - 找到要刪除的 node 的**直接後繼**，即為中序遍歷的直接後繼(不小於當前節點的最小節點) 並交換
        - BinNode::succ()來達成
    - 此時要刪除的 node 最多只會有一顆**右子樹**，再用第一種方法刪除
![](https://i.imgur.com/Sq42jBA.png)

    ![](https://i.imgur.com/MUuE4f1.png)

## BBST
- **以上討論 n node, 高度為 h 的 BST，在 search, insert, remove 的最壞表現都是 O(h)**
- 但有 n node 的 BST，log~2~n <= h <= **n** (都只有單邊的情況)
- 如此算法複雜度最壞還是有 O(n)

### 達成理想
- ndoe 樹固定時，兄弟子樹的高度越相近 (**越平衡**)，則整個樹的**高度更低**
- n 個 node 的 BST，高度最低為 log~2~n
    - **當高度為 log~2~n 時，稱作理想平衡** (可遇不可求)
    - complete binary tree：leaf node 只會出現在最底層或次底層
    - full binary tree：最好的狀況，高度為logn，即為每個 node 都有兩個子樹 (可遇不可求)。然而只要進行 insert, remove 後就會被破壞
    - **因此我們追求漸進意義上的平衡(高度漸進的不超過 logn)，這種適度平衡的 BST，即為 BBST 平衡二元搜索樹(Balanced Binary Search Tree)**
    - 儘管一個 BBST 可能在進行操作後變成 BST，但我們願意以**不超過 O(logn)** 的代價將它重新調整為 BBST
![](https://i.imgur.com/HpVIZbS.png)

- BBST 有多種實現
    - AVL tree
    - 伸展樹
    - B Tree
    - 紅黑樹

- 將失衡的 BBST 重新調整為 BBST 的過程稱為 **rebalance**
- BBST 實現的重點
    - 如何界定是一顆 BBST
    - 如何 relbance

### 等價 BST
- 不同的 BST 可能有相同中序遍歷結果，我們稱這兩個 BST 等價
- 可以透過調整 BST 的結構轉換成 BBST，即使 BBST 進行了 insert, remove 的動作後，可以再調整回 BBST

- 規則
    - 上下可變：祖先-後輩關係可能改變
    - 左右不換：中序遍歷維持不變
- 等價轉換
    - zig(順時針) and zag(逆時針)
    - 每次操作複雜度 O(1)
    - 每次轉換累計操作次數**不超過 O(logn)**
    - ![](https://i.imgur.com/48pwS4v.png)

### AVL Tree
- AVL Tree 是 BBST 的一種實現

#### 界定標準
- 平衡因子
    - **空子樹高度為 -1，只有一個節點的樹高度為 0**
![](https://i.imgur.com/8DXporS.png)

- AVL 適度平衡的證明
    - 可證明**高度為 n 的 AVL Tree 高度至多為 logn**
![](https://i.imgur.com/egjT7zz.png)

#### 接口
沿用 BST 的類
- 只需要重寫 insert 跟 remove 接口

![](https://i.imgur.com/QsgrM5J.png)

#### Rebalance
- 在 AVL Tree 剛插入一個節點後，失衡節點個數最多為 O(logn)
    - 從包含**祖父**上去的祖先都因此失衡
    - 除了祖先之外的節點都不會失衡
    - 但實際上，插入操作比較容易實現，因為只要調整好最深的失衡祖先，以上的祖先也都會恢復平衡
- 在 AVL Tree 剛刪除一個節點後，失衡節點個數最多為 O(1)
    - 只可能導致一個祖先節點失衡
    - 但實際上，刪除操作的實現更複雜

- Insert 
    - 單旋
        - 圖中的三個祖先 vector, father, grandfather 由右到左，稱為 zagzag (需要 zag 旋轉)
        - 若相反從左到右，稱為 zigzig  (需要 zig 旋轉)
        - ![](https://i.imgur.com/X1dntZo.png)

    - 雙旋
        - 若為 zigzag（如圖）或zagzig，需要雙旋轉
        - ![](https://i.imgur.com/jLn73jB.png)

    - 實現
        -  複雜度 search 則為 O(logn)，其中實質插入的部分是 O(1)
        - ![](https://i.imgur.com/K11OBqt.jpg)

- Remove
    - 單旋
        - 圖中的三個祖先 vector, father, grandfather 由左到右，稱為 zigzig (需要 zig 旋轉)
        - 儘管旋轉一次後局部會恢復平衡，但有可能會造成之前祖父的失衡
        - 最壞需要做 O(logn) 次調整
        - ![](https://i.imgur.com/ozGBh8W.png)
    - 雙旋
        - 若為 zagzig（如圖）或 zigzag，需要雙旋轉
        - 最壞需要做 O(logn) 次調整
        - ![](https://i.imgur.com/HlxrUcJ.png)
    - 實現
        - 複雜度為 O(logn)，不論好壞
        - ![](https://i.imgur.com/hMrWpon.png)

- RotateAt 實現
    - 在要實現 rotate 時，**並不需要** 像上述那樣按部就班的做旋轉，旋轉的過程僅是幫助理解
    - 實際上我們可以直接拆解局部並重新拼接
    - 不論插入或刪除的單雙旋，調整過後都應該變成下圖中的樣子
    - 因為是3個節點，4個子樹，所以又稱作 3+4 算法
    - ![](https://i.imgur.com/NYfGYsP.png)
    
    - 實作 3+4
    
    - ![](https://i.imgur.com/UY5shyK.png)

    - 實作 rotateAt：我們可以清楚知道四種狀況下(zigzig, zigzag, zagzag, zagzig)，要傳入 3+4 的順序
    - ![](https://i.imgur.com/3uJgMQZ.png)

#### 評價
- 優點：
    - 插入、搜尋、刪除都是 O(logn)
- 缺點：
    - 要另外維護平衡因子
    - 實作性能跟理論上有差距，尤其刪除很麻煩
    - 插入跟刪除的成本**不對等**，尤其刪除後可能要調整 logn 次
    
![](https://i.imgur.com/4lRNKzG.png)


# 第八章 高級搜索樹
- AVL 樹過於嚴格：需要不停維護平衡因子
- 需要將條件放寬鬆一點


## 伸展樹 Splay Tree
### 局部性
- 伸展樹利用了局部性的特點

![](https://i.imgur.com/196vU0o.png)


- 以 List 為例子，某種做法是將剛被查找完的 node 搬移到最前方，因為該 node 很可能馬上又要被查找。如此 list 前端便會集中一區「較常被訪問的 node」藉此提高效率

### 自我平衡 - 逐層伸展
- 在伸展樹中，我們則將被訪問到的 node 搬移到樹根的位置，因為樹根的訪問最方便
    - 透過不斷進行 **zig 或 zag 旋轉**，將剛被訪問完的 node 搬移到樹根(一層一層往上爬)
    - 成為能夠自我平衡的二元搜尋樹

- 最壞情況
    - 效率上比不上 BST 的 log(n)
    - 已經退化成 list: log(n)
    - 所以算法需要改進
![](https://i.imgur.com/6rzeZe3.png)

### 自我平衡 - 雙層伸展
![](https://i.imgur.com/dUcqIA1.png)

#### zigzag / zagzig (無改變)
- 與逐層調整一樣，也與 AVL 雙旋一樣
![](https://i.imgur.com/XPhJ4TQ.png)

#### zagzag / zigzig (改變)
- 與原先(圖中上半部)不同，改成**兩次都在 祖父 node 上進行旋轉**
- 看似只是圖的方向改了，但實際上有很大的差異 (折疊效果)
![](https://i.imgur.com/YXeN59J.png)

- 如果沒有祖父 node (父親就是 root)

 ![](https://i.imgur.com/XPpcMl8.png)

- 折疊效果
    - 調整能使樹高降低！
    - 當惡意訪問「退化成單鏈伸展樹」的最深節點，高度甚至近乎減半
    - 有對壞節點的修復作用
    - 分攤後**單趟伸展操作複雜度不超過 O(logn)**
![](https://i.imgur.com/8C6QMvX.png)

### 接口
- 重寫 search(因為找到後要改變結構), insert, remove
- 還需要一個實作伸展 splay 的方法
![](https://i.imgur.com/NSvTVwk.png)

### 實作
#### 伸展 splay
![](https://i.imgur.com/dsWVYCr.png)

- 其中對於四種情況的實作 (zigzig, zigzag, zagzag, zagzig)
    - 一樣略過旋轉的過程，直接拼接
    - 以 zigzig 為例
    ![](https://i.imgur.com/5KrmhTw.png)

#### Search 
- 因為查找完後還會調整，所以 Search 為動態操作
- ![](https://i.imgur.com/Ddoyp2y.png)

#### Insert
- insert 後 node 應該要在 root
- 先調用一次 search，search 會將 hot 移至 root
- 接著再依情況將要插入的 node 再插置 root
(下圖 t 即為 hot)
![](https://i.imgur.com/715hoVM.png)

- 插入操作的分攤複雜度比 AVL 低

#### Remove
- 先 search 後待刪除的元素就在 root
- 刪除 root
- 在右子樹中找到最小的 node (即為最左末端的 node)，該 node 比右子樹都小、比左子樹都大，故可以提升當作 root
- ![](https://i.imgur.com/hetruIZ.png)

### 評價
- 比 AVL 容易實現，不需要另外維護平衡因子
- 有 cache 的概念
    - 在有局部性的情況下，長期使用後可以感受到效能提升
    - 如平時使用電腦，常開的文件與軟體是差不多的
    - 規模為 n 個伸展樹，若訪問的節點只有 k 個，長期下來訪問的分攤複雜度為 O(logk)
- 單次效率仍可能很差
    - 注重單次效率的操作不適用
- ![](https://i.imgur.com/5wOhajl.png)

## B-Tree
- 物理上不一定是 BST (可能多於兩個分岔)，但邏輯上是 BST
- 所有底層節點的深度要一致
- B-tree 比一般的 BST 更寬、更矮
- **可以實現高效 I/O**

### 內存(memory)日趨不足
- 雖然內存日益增加，但相對現在的應用而言，其實是不斷縮小
- 為什麼不把內存做大？因為需要顧及**速度**，內存越大速度越慢
![](https://i.imgur.com/Qdwmytc.png)

### 高速緩存
![](https://i.imgur.com/EXYqlMS.png)

![](https://i.imgur.com/xfzAlNa.png)

- 結論：若要向外存訪問，則ㄧ次就要訪問/讀寫多一點數據，如此才能優化

### 定義
- 平衡的多路(multi-way)搜索樹
    - 其實本質上跟二叉搜索樹是一樣的
    - 如下圖是每兩代合併（父子合併），共會有四路後代，且合併後的超級節點有 3 個 key
    - ![](https://i.imgur.com/lfJ3sNK.png)
    - 簡化後即為下圖
    - ![](https://i.imgur.com/SoUrmSO.png)

![](https://i.imgur.com/MaHao4q.png)

- m 階 B tree = m 路平衡搜索樹
- m 常取 256
- 葉節點的深度要統一
    - 外部節點：葉節點之下的那一層空節點
    - 既然葉節點深度統一，外部節點的深度也會統一
- 樹高：是從 **外部節點**算起，跟一般樹從葉節點算起不一樣
![](https://i.imgur.com/qnbxvTS.png)

- m 階的樹，每個內部節點不超過 m 個分支，且不小於 m/2 (無條件進位)個分支
    - 樹根可以除外，樹根不能少過 2 個分支
    - 又稱 (m/2, m) 樹
    - 也可用樹的分支上下限來稱呼，如 (3,5), (3,6)樹
    - 其中 (2,4)樹有重要的地位，與紅黑樹也有關
- ![](https://i.imgur.com/wJSRpVu.png)

- 外存操作：節點間上下層的移動 (慢) （垂直的移動）（I/O操作）
- 內存操作：節點內 vector 中的移動與搜尋 (快）（水平的移動）

### 優點
- 可以大大降低 I/O
- 因為 B-tree 結構上較矮，可以減少 I/O 次數
- 且一層訪問都以**超級節點**為單位，可以批量訪問一組 key
- 1kb/4 byte 大約為 200~300，即一組超級節點大約為 200~300 個原始節點拼成 (假設一個節點 4 byte)，常取 256
- ![](https://i.imgur.com/ZvTtYVD.png)


### 實現
#### BTNode 
- 一個超級節點的內部是**兩個向量 vector**
    - 一個存放 n 個 key
    - 一個存放 n+1 個 分支pointer
    
![](https://i.imgur.com/RFKr5kJ.png)


#### BTree
- 實作 search, insert, remove 
- 實作當 BTree 違法時的解決方法 `solveOverflow`, `solveUnderflow`

![](https://i.imgur.com/q97fkHU.png)

#### Search
- 只載入需要的節點，盡可能減少 I/O
- 每往下一層就需要一次 I/O time (外存操作)
- 根節點一開始放置於 RAM 中
- 失敗查找必終止於 **外部節點**
![](https://i.imgur.com/viJeUVP.png)

- 實例
![](https://i.imgur.com/WqrIWUd.png)

- 實現
    - 如果當前節點沒有找到，且內部 vector 的 search 返回的 rank 為 r，則深入 **(r+1)** 這一個分支進入下一層 （即為 r 的右分支）
    - 如果查找失敗則會返回 NULL
![](https://i.imgur.com/xmlsaWe.png)

- 複雜度
    - 來自於 逐層的I/O (樹的高度h) 及 當層 vector 的順序查找
    - 當層 vector 為什麼不使用 binary search?
        - 因為其優化的影響遠不及 I/O 操作
        - 在一般的 B-Tree 中，一個超級節點大約有 200~300 個 key，有研究顯示這樣的大小使用 Binary search 反而可能有害

#### 效能
- 最大樹高
    - 若 m=256, 最大樹高是 BBST 的 1/7
    - 若 m=128, 最大樹高是 BBST 的 1/6
    - 以此類推
![](https://i.imgur.com/xKDvcAW.png)


- 最小樹高
    - 若 m=256, 最小樹高是 BBST 的 1/8
![](https://i.imgur.com/2ZUulFm.png)

- 可知 B-tree 的樹高大小相差不大
    - Omaga(log~m~N)  至 O(log~m~N)  (最小與最大)


#### Insert
- 先 search，若不存在則返回 NULL，hot 會指向底層的 node
- 再在 hot 指向的 node 中尋找插入的位置 (在 vector 中尋找)
- 最後要處理overflow 的情況

![](https://i.imgur.com/Ee1EDR8.png)

#### 處理 overflow
- 分裂
    - 對於 overflow 的那個 node，找到其**中位數**的那個值，以其為中心左右分裂成兩個分支，並將該值**提升至上一層**，可能會造成上一層的 overflow
    - 可以確定分裂後，左右分支都符合 Btree 的限制 (m/2 ~ m)
    
    - ![](https://i.imgur.com/o6EaDTL.png)

- 再分裂
    - 一路 overflow 的話，就按照上述的方法處理
    - root 的 overflow 需要另外處理
        - 提升成為一個新的 root
        - 整個 B tree 高度也會增高一層
        - 是 B tree 會增高的**唯一情況**
        - 此時新的 root 只會有兩個分支（符合定義）
    - ![](https://i.imgur.com/pjJMMxI.png)

- **Insert 的複雜度最壞 O(h)**

![](https://i.imgur.com/ZTycGND.png)
![](https://i.imgur.com/Duo0NYl.png)


#### Remove
- 先 search，並返回該刪除的 node v
- 再在 node 的 vector 中找到要被刪除的 index r
- 若該 node v 不在 leaf，則要將要被刪除的值與他的後繼互換，**確保 v 換到底層成為 leaf**
- 刪除 v 及他的外部節點
- 最後要處理 underflow 的情況

![](https://i.imgur.com/Gf4kIl5.png)

#### 處理 underflow
- 利用旋轉與合併兩個技巧
    - 旋轉：當 underflow 的 node 的左右分支足夠借出一個 key 時
    - 合併：當 underflow 的 node 的左右分支都無法夠借出一個 key 時
    - 
- 旋轉  
    - 剛 underflow 時，會恰好包含 m/2 -1 個分支
    ![](https://i.imgur.com/y4YNRfk.png)

- 合併
    ![](https://i.imgur.com/lymIC98.png)

- 若為狀況 3，有可能導致其父親節點也 underflow
    - 若一路 underflow 到 root 的孩子，則原本的 root 將被合併
    - 是 B tree 會降低的**唯一情況**

- **Remove 的複雜度最壞 O(h)**
- 程式碼
![](https://i.imgur.com/c3virBy.png)
![](https://i.imgur.com/XYnkT6e.png)
![](https://i.imgur.com/6OdoYkl.png)
![](https://i.imgur.com/SKGuOwH.png)



## 紅黑樹
### 優點
- 先前的樹 (AVL, B tree , 伸展樹等)在進行動態操作後，狀態就會隨即改變
- Persistent Structure：支持對於歷史版本的訪問，如 insert(ver, key)
- 紅黑樹可以較有效的達成版本的紀錄
    - 比起每個版本都重新紀錄一棵樹，紅黑樹採用「引用」的方式節省空間
    - 為了達到高效率，我們希望任何一次動態操作引發的**結構變化量**(如旋轉、合併、分裂等等)，不致超過 O(1)
    - 然而 AVL, SPlay 等無法達到，如 AVL 插入可以 O(1),但刪除 O(logn)
    - **紅黑樹則可以達到每次 insert, remove 後結構的變化都不超過 O(1)**

### 定義
- 跟 B Tree 一樣，要增設外部節點 (Null 節點)
- **高度：只計算黑節點的高度**
![](https://i.imgur.com/BrY99t0.png)

#### 規則
![](https://i.imgur.com/8Yh007Z.png)
![](https://i.imgur.com/LM9WOrB.png)

#### 紅黑 = B Tree
- 提升 Lifting：將紅黑樹內部的紅色節點提高至與父親（必為黑節點）同層
- 每顆紅黑樹經過提升之後，就是一顆 **(2,4) B tree**
- 我們可以透過（概念上）提升變換將紅黑樹轉換為 Btree 後，來理解應該怎麼進行新增與刪除，並直接取其結論
![](https://i.imgur.com/ACw9l3H.png)

### 接口
- 高度：只計算黑節點的高度
- Search：跟 BST 一樣，不需要重寫
![](https://i.imgur.com/mlvG8DF.png)


### 實現
#### 插入

- 算法
![](https://i.imgur.com/2ZA55Zl.png)


- 雙紅缺陷
    - 相鄰的兩個父子節點都為紅色
    - 解法
        - 需要分為**父的兄弟為紅**、**父的兄弟為黑** 兩種狀況解決
![](https://i.imgur.com/E4GR0u2.png)

    - 若 u (**父的兄弟)為黑**
        - 需要 O(1) 的拓墣調整 (3+4重構)、O(1) 染色
        - ![](https://i.imgur.com/hZaXNyG.png)
        - ![](https://i.imgur.com/VJkUFKG.png)
    - 若 u (**父的兄弟)為紅**
        - 可能需要 O(logn) 的染色，不需要拓墣結構的調整
        - 如此提升後的 B Tree 會 overflow，就使用之前討論過分裂過程 （不需直接操作，而是幫助理解）
        - 實際上只需要直接將 p 與 u轉黑，g 轉紅
        - ![](https://i.imgur.com/GWmiWnr.png)
        - 由於在 B Tree 的層次中發生分裂，代表可能造成整棵樹的**高度加一**
        - 但在紅黑樹的層次中，只需要將節點改變顏色，即使可能要改色的節點高達 O(logn)，但實際拓墣上仍然沒有變化
        - 如果提升達到樹根
        - ![](https://i.imgur.com/GlH8Dfa.png)
        - ![](https://i.imgur.com/IWk3aST.png)

- 複雜度
![](https://i.imgur.com/cHriguX.png)

#### 刪除
- 算法
![](https://i.imgur.com/RJfa5GJ.png)

![](https://i.imgur.com/7uZ00ku.png)

- 需要分為四種情況處置
    - 見講義

- 複雜度
    - 總合最多需要 O(logn) 的染色、或 O(1) 的染色加上 O(1)的結構調整
- ![](https://i.imgur.com/fc1skSP.png)

- 紅黑樹優於 AVL 樹，因為減少了很多結構調整

## 綜合比較
- B tree: 適合大規模的數據（不能全部放在內存中）的存取
- 伸展樹：易於實現，個接口的分攤複雜度為 O(logn)、有局部性 cache
- 紅黑樹：擴充後可以支持對歷史版本的訪問
- kd-樹(額外)：處理和己和有關的問題

# 第九章 Dictionary 辭典

## Call By Value
- Vector: call-by-rank
- List: call-by-position
- BST: call-by-key
- Dictionary: call-by-value
- Priority queue: call-by-priority
## 原理
- bucket：直接或間接存放一個 entry
- hash table = bucket array
- 透過 **Hashing 雜湊** 來達到 call by value
    - 需要有一個 hash function，將 key -> &entry (entry 的地址)
    - hash(key) = &entry
    - 透過 hash function, 可以將輸入的 key hash 後直接對應到 hash table 中的 bucket，接者再訪問該 bucket 中 (直接或間接) 的 entry

- 如此作法有兩個優點
    - 找到 key 對應的 entry 只需要 O(1) 的時間 (hash + 查找)
    - 只要 hash 選擇得當，可以提高空間效率

- Load Factor 裝填因子 (Lambda)： 
    - N/M
    - N :實際存放在 hash table 中的數量
    - M : hash table 的容量

### hash collision 雜湊碰撞
- 時常發生多個 key 經過 hash() 後得到相同的 &entry
    - 例如 hash(x) = x % 20，則 25 跟 85 將發生 hash collision
    - 可以透過提升 M , 降低 load factor 減緩，但還是會發生
    - 因為我們試圖將很大的定義域(key)投射到 hash table 中，所以衝突幾乎無法避免

### Hash table 的達成
需要設計兩個部分
1. Hash function 本身
2. hash collision 發生時如何排解

## Hash 的設計
### 評價
- 什麼是好的 hash function
    - determinism: 同一個 key 要被 mapping 到同一個地址
    - efficiency: 要有效率
    - surjection (滿射): 盡可能充分利用整個 hash table 的空間
    - uniformity: key mapping 到 hash table 各個地址的機率要相近，可以避免 clustering（群聚）的現象

### 除餘法
- hash(key) = key % M
- M 為**質數**時，數據對 hash table 的覆蓋最為充分、分布較均勻
- 缺點
    - 存在不動點：不論 Ｍ 多大，需要將 hash(0) 定義為 0 （否則是非法除法）
    - **低均勻性**：相鄰的 key 的地址也會相鄰 (在某些應用下會成為優點)

- 在某些特定場合下，會希望有低均勻性，稱為 Locality-Sensitive Hashing
### MAD法
- 是除於法的推廣
    - 希望鄰近的 key 在 hash table 中的地址不要鄰近

- MAD = multiply-add-divide
    - M 為質數，a>0, b>0, a%M 不為 0
    - b 是偏移量 offset，可以**消除不動點**
    - a 是 步長 step, 可以**消除低均勻性**，讓相鄰的 key 在 hash table 中的地址不要相鄰
    - hash(key) = [(a * key) + b] % M

### 更多 Hash function
#### selecting digits and mid-square
![](https://i.imgur.com/bVA5omu.png)

#### folding and XOR
![](https://i.imgur.com/SJqRkdN.png)

#### 隨機數法
- 直接採用 rand(key)，因為 rand 本身是一個已經實現 hash function，每一個隨機數都可以由上一個隨機數推得
    - rand 中使用的 key 時常是系統時間
- 符合確定性、高效性、滿射、均勻
- **缺點：不同平台上、不同歷史版本的的 rand 算法時常不同！將會導致 hash() 不能直接移植**

![](https://i.imgur.com/nHTRvJs.png)

#### 多項式法
- 若輸入的 **key 不是整數**，就需要先將 key 做預處理，轉換成整數形式的 **hashcode**，之後才能將 hashcode 經過 hash() 轉換成 bucket address
- 如何將 key 轉換成 hashcode 
    - 時常使用**多項式法**
- **string 時常使用多項式法進行轉換**

![](https://i.imgur.com/Cw2f3LU.png)

- 多項式法看似複雜，確有其必要性
    - 如果採用比較簡單的映射（而不是多項式法）
    - 將每個英文字母對應於某個數字，再將 string 中每個對應數字累加
    - 缺點：時常會發生衝突，因為 string 中的文字順序不影響 hash code。就算是不同的 string 也很容易衝突

## Hash collision 排解
### 使用 Vector （多槽位法）
- 每個 bucket 裡都是一個 vector
    - 無法判斷要預留的 vector 長度，可能過多或過少
![](https://i.imgur.com/IamrDRN.png)

### 使用 List （獨立鍊）
- 每個 bucket 裡都是一個 pointer 指向一個 list
- closed addressing
- 缺點：因為 list 在物理空間上不是連續分布的，系統難以預測訪問方向，難以透過緩存功能加速查找
    - 當資料很大，需要 I/O 時，更加慢
![](https://i.imgur.com/iIaG1TP.png)

### 開放定址
- open addressing (closed hashing)
- 不依賴額外空間，都在一塊物理上連續的空間內完成
- 若有必要，每一個 bucket 都可以裝任意的 key
- 每個 bucket 及他的備用 bucket 稱為**查找鍊**
- 每個 key 會先檢查他原本所屬的 bucket 是否為空，若是空就進入該 bucket，若不是空則依**Probing sequence 查找鍊**的順序到下一個 bucket 中查看

- ![](https://i.imgur.com/palvyM2.png)

#### 查找鏈的順序
- Linear Probing 線性
    - 每次都找後一緊鄰後繼
    - 缺點：先前的衝突排解，會導致後續的衝突
    - ![](https://i.imgur.com/KREA60j.png)
    - ![](https://i.imgur.com/kVzfUjr.png)
    - 懶惰刪除 Lazy removal
        - 依上述線性試探為代表的開放定址策略，在刪除 key 時要格外小心
        - 因為如果直接刪除，造成中間有空的 bucket，那查找鍊就會被截斷
        - lazy removal 只做刪除標記
            - 查找時看到做了刪除標記的 bucket 則不應該中斷，要忽略這個 bucket 繼續查找
            - 要插入新的 key 時，則可以直接覆蓋掉有刪除標記的 bucket
            - ![](https://i.imgur.com/F2R6rU0.png)

- Quadratic Probing 平方
    - 以平方數為距離 (0, 1, 4, 9, 16...)
    - ![](https://i.imgur.com/kn0PzKX.png)
    - ![](https://i.imgur.com/x7LTw1W.png)

    - 優點：緩解數據聚集的現象
    - 缺點：
        - 如果有外存，I/O 增加 
            - 假設緩存頁面為 1kb，每個 bucket 是 4 byte，則需要連續 16 次衝突才會發生 I/O
        - 因為每次都是用平方距離移動
            - 有可能發生「還有空桶卻找不到」的情形
                - 如果 M 是質數且 load factor <= 0.5 就不會發生
                - 如果 M 是質數且 load factor > 0.5 就可能會發生
                - 故當 M 為質數，為了使平方查找每次都能成功， load factor 需要少於 50%，代表有一半的空間無法運用
                - ![](https://i.imgur.com/p7uPKeo.png)

- 雙向平方試探
    - 一樣以平方樹為距離，但每個平方樹都要向前向後試探 (0, 1, -1, 4, -4, 9, -9...)
    - 只要 M 是質數，就不會發生「還有空桶卻找不到」的情形
    - 然而就算 Ｍ 是質數，也可能發生向前、向後有公共的 bucket
        - 所有質數除了2之外，都可以分成兩類：除以4餘3,除以4餘1
        - ![](https://i.imgur.com/ultM3Us.png)
        - 經證明：M 應該要取 **4k+3 的質數**，如此必然可以**保證查找鍊前 M 項互異**

## 應用 
### [Bucket Sort 桶排序](https://ithelp.ithome.com.tw/articles/10201707)
- 性能不完全取決於輸入的規模 n (帶排序的序列長度)
- 也取決於待排序元素的**取值範圍** M, 如 `[0, M)`
    - 例如大學共有 n=13,000個學生
    - 如果按照誕生日期(包含年份)排序，則 M=1,500
    - 如果按照生日排序，則有 M = 365
    - 代表含有大量重複的元素
- 時間複雜度是 **O(n+M)**，也就是 O(max(n, M))
    - 當 M 小，就**有可能在 O(n) 的時間內完成排序！**
- 例子：對一組大寫英文字母的排序
    - 因為 M = 26 << n ，存在大量的重複
    - 先掃描一次 O(n)，統計每個英文字母出現的數量
    - 先掃描一次 O(M)，統計每個英文字母出現的累積量
    - 依照上面兩個表，如此就可以知道每個英文字母在排序後應該所處的範圍，便可以排序完畢
    - ![](https://i.imgur.com/UXDmir6.jpg)


# 第十章 Priority Queue 優先級隊列
- 不是先進先出，而是按照 priority 訪問
- Call By Priority
- 如作業系統中的多任務調度，可能有多個資源要調用 CPU，會按照 priority 處理
- ![](https://i.imgur.com/hzlSPtt.png)

## ADT
![](https://i.imgur.com/KNbAaXx.png)
- 有三個函數，依照實際 priority Queue 使用情境再加以實現
    - getMax(): 取出 priority 最高的 entry
    - delMax(): 刪除 priority 最高的 entry
    - indert(): 依照 priority 順序插入 entry
- stack 跟 queue 都是 PQ 的特例，其 priority 就是插入的順序
    - stack：越晚到的 priority 越高，LIFO
    - queue：越晚到的 priority 越低，FIFO

## 實現
- 要考慮效率與成本
### Vector
![](https://i.imgur.com/Kjna7Pc.png)
- 只有 insert快，剩下兩個接口都很慢


### Sorted Vector

![](https://i.imgur.com/Xoi3rAC.png)
- insert 慢，要先查找再插入

### List
![](https://i.imgur.com/hmFjHQp.png)

### Sorted List
![](https://i.imgur.com/i6JjGBQ.png)

### BBST
- 上述的DS都不適合用來實現 PQ，因為效率太差
- 使用 BBST 可以高效率完成各個接口，都是 O(logn)
- 然而使用 BBST 是**大材小用**，用太過高級的數據結構來實現功能簡單的 PQ
- 需要找到一種新的實現方式，能同時保有高效率且易維護的優點
- ![](https://i.imgur.com/QMBk4O1.png)

### Complete Binary Heap (完全二叉堆)
- 一種結合 vector(形) 及 tree (神) 的結構
    - **邏輯上等同一顆 Complete Binary Tree （完全二元樹）**
    - **物理上使用 Vector**
- complete binary tree 的節點必由左而右填滿


![](https://i.imgur.com/G1SSntJ.png)
- 用 vector 實現 complete binary tree，其中 **index** 為 i 的元素
    - parent 會是 (i-1) / 2
    - 左孩子： 2 * i + 1
    - 右孩子：(i+1) * 2

- 實現
    - 同時繼承 Vector 跟 PQ 兩個 class

![](https://i.imgur.com/I2vQccY.png)

- 需要滿足**堆序性**
    - 孩子節點的 **priority** <= 父親節點的 **priority**
    - 故 maxPriority 必定在 root


## 實現 Complete Binary Heap
### 插入
- 上濾 percolate up: 節點 e 與其父親交換的過程
- 插入的過程就是：插入到最後（底層），上濾
![](https://i.imgur.com/RE2E4kH.png)
![](https://i.imgur.com/1AdbcQy.png)

- 實現
    - insert 本身就會將元素插入最後
![](https://i.imgur.com/BuzytuU.png)

- 效率
    - 規模為 n 的 complete binary heap，**插入的時間複雜度為 O(logn)**，因為樹高為 log n

![](https://i.imgur.com/F3c8Jex.png)

### 刪除
- delMax 刪除最大 Priority (不會刪除其他孩子)
- 必在 root，雖刪除容易，但要調整結構
- 下濾 percolate down: 節點 e 與其 priority 較大的孩子 交換的過程
- 刪除的過程就是：刪除 root，把最後的節點 e 移致 root，下濾
![](https://i.imgur.com/cw7dQsN.png)
![](https://i.imgur.com/l2sONda.png)

- 實現
- ![](https://i.imgur.com/XU7Y1N2.png)

- 效率
    - 規模為 n 的 complete binary heap，**刪除的時間複雜度為 O(logn)**，因為樹高為 log n
    - 實質上下溢比上溢更複雜一些，因為下溢時需要跟兩個孩子比較
    - ![](https://i.imgur.com/Sr2bJP8.png)

## 批次建堆 Heapification
- heapify 方法：將 n 個元素構建成完全二叉堆
- 自上而下的上濾建立規模為 n 的 complete binary heap
    - (樹中)由上而下、(vector中)由前而後、不斷插入節點並上濾
    - 為對個節點 **深度** 的總和
    - 最壞時間複雜度是 O(nlogn)，這個效率不夠好
- 改由自下而上的下濾
    - ![](https://i.imgur.com/FQegeza.png)
    - (樹中)由下而上、(vector中)由後而前、不斷插入節點並下濾
    - 葉子節點都不需要下濾
- 例子

![](https://i.imgur.com/U5zbmmH.png)

- 效率
    - **為 O(n)**
    - 為對個節點 **高度** 的總和
    ![](https://i.imgur.com/wll89Ra.png)

## Heap Sort
- 用 Heap 取代 selection sort 時的 vector
- 概念
    - 先用 heapify 把序列整理成 complete binary heap，需要 O(n)
    - 再不斷使用 delMax，把 root 的元素一直拿出來，一次需要 O(logn)
    - **共需要 O(nlogn)**
    - 比起 selection sort 的 O(n^2^)來得快，且邏輯一樣所以正確性毋庸置疑

![](https://i.imgur.com/AjlUeHI.png)

- 且 Heap Sort 是**in-place algroithm 就地演算法**
    - 空間複雜度為 O(1)
    - 因為 Complete binary Heap 本身就是 vector 的形式
    - 

- 算法
    - 先用 heapify 把序列整理成 complete binary heap
    - 重複以下步驟直到排序完成
        - 將第零個元素 (root) 與未排序的最後一個元素 swap
        - 下濾
    - 除了 swap 時需要輔助空間之外，不需要額外的輔助空間

![](https://i.imgur.com/eJguK8S.png)

- 實現
![](https://i.imgur.com/ChALapd.png)

> Note-10.1
> Heap sort 是一個時間複雜度為 O(nlogn)，空間複雜度為 O(1) 的演算法
> 

## Leftist Heaps 左式堆、左堆
- 向左側傾斜
- 有較高效的**合併**效率
### 堆合併
-  Merge Heap A and Heap B
-  設 A 的規模為 n ，B 的規模為 m
-  方案一
    -  將兩個 heap 中的元素先合併，再使用 heapify(n+m) 來建堆
    -  需要 O(m+n)
    -  結果並不滿意，因為作為輸入的兩個 heap 本身已經排序，卻沒有利用到這個優點
-  方案二
    -  使用左式堆，可以在 O(logn)完成操作

### 概念
- 單側傾斜
    - 節點偏向左側
    - 合併操作在右側
- 會破壞結構(Complete binary heap)，但保有本質
- ![](https://i.imgur.com/MbucBku.png)

#### NPL
- 用來度量**傾斜性**
- 先將原本的 binary tree 加上外部節點
- npl(NULL) = 0 ，外部節點的 NPL 為 0
- npl(x) = 1 + min (npl(x->lc), npl(x->rc))，即為取左右子樹中，npl較小者 再加一
    - 與 height 對應，height 是 1 + max (height(x->lc), height(x->rc))
- npl 也是 x 到外部節點的**最短距離**
- ![](https://i.imgur.com/iorZmfW.png)

#### 左式堆
- 任意節點 x ，左孩子的 npl >= 右孩子的 npl
    - 因此 npl(x) = 1 + npl(x->rc)，因為右邊的 npl 必較小

![](https://i.imgur.com/GTiw5N6.png)

#### 右側鍊
![](https://i.imgur.com/prxMIdX.png)

### Template
- 與 Complete Binary Heap 不同，因為左式堆不滿足結構性，因此物理結構上不在保有緊湊性
    - 改用繼承 **Bintree 而非 Vector**

![](https://i.imgur.com/ShiB70r.png)

### 算法
- 遞迴
    - 假設 A 的 root > B 的 root，兩者都是左式堆
    - 拿 A 原先的右子堆跟 B 合併，合併的結果當作 A 的右子堆 (遞迴步驟)
    - 檢查 A 的左右子堆 是否符合 NPL，不符合則互換位置

### 實現
#### Merge
- 算法沿著右側鍊進行
    - 因為右側鍊長度為 O(logn)
    - 所以時間複雜度為 O(logn)
![](https://i.imgur.com/Ofio0D5.png)

### Insert
- **插入即是合併**
- **O(logn)**
- 待插入的元素就是一個 **以自己為唯一成員** 的左式堆，且為 root
- ![](https://i.imgur.com/jvi905b.png)

### Remove
- **刪除也是合併**
- **O(logn)**
- 刪除 max 後 (root)，剩下的兩個左右孩子透過合併成為新的左式堆
- ![](https://i.imgur.com/RLlo6Pt.png)


# 第十一章 String 
- 結構上是 vector

## 術語
![](https://i.imgur.com/5NPyPrm.png)

## ADT
- 其中 indexOf 是功能較強的函數，接著對討論如何有效率的實現
![](https://i.imgur.com/QNchXUd.png)

## String-Matching 字串匹配
![](https://i.imgur.com/zgonqLp.png)
- T 要被搜尋的文本，長度為 n
- P 要被搜尋的模式(pattern)，長度為 m
- n >> m >> 2
- 有幾個關心的方向
    - p 是否有出現
    - p 首次在哪裡出現 （核心，可以解其他問題）
    - p 總共出現幾次
    - p 各出現在哪裡


### 蠻力法 Brute-force
![](https://i.imgur.com/Rd1i6A0.png)

- 版本一
    - 透過檢查返回值 (i-j) 是否合法，來判斷 matching 是否成功
    - ![](https://i.imgur.com/6q0IQe2.png)

- 版本二
    - 與版本一很像，只是 i, j 的意義不同
    - ![](https://i.imgur.com/WwfQTZs.png)
- 效率
    - **複雜度為 O(mn)**
    
![](https://i.imgur.com/8LgEBLg.png)

### KMP 算法
- 一種經典的 string matching 算法
- 蠻力法失敗的原因：太多重複匹配的前綴
- KMP 算法的改善
    - 記憶力：我們會記憶在**失配前已經成功匹配的字串**，並透過這些已知的訊息來提高效率、大幅度的省略重複匹配
    - 預知力：可以透過預處理來提高效率

#### 概念
- 實作一張查詢表
    - 實現記憶力與預知力的方法
- 概念
    - 製作一張預處理表 next，next 大小只跟 pattern 的長度 m 有關
    - i 為 text 中的 counter, j 為 pattern 中的 counter
    - 在任一位置 P[j] 匹配失敗後，將**對齊位置 j 替換為 next[j]**
    - ![](https://i.imgur.com/Dj2tnBW.png)

#### 主算法
- 跟蠻力算法很像，差在有 next 表
- next 表只跟 pattern 有關，不受 text 影響
- 每一次匹配失敗之後，**text 不需要回頭，直接往下比對**

![](https://i.imgur.com/ryCe0he.png)

- 實例
    - 最上方是 next 表
    - 比對後，在 p[7] 發生失配 (l的位置)
    - 查詢 next[7] = 3，因此把 j 更新成 3
    - i 則不用後退
    - 透過 next 表聰明的省略了 3 次不必要的匹配
- ![](https://i.imgur.com/hnXlUfP.png)

#### 查詢表 next[]
- 用來排除許多不必要的匹配
- 概念
    - 分析 P 字串，在 P[j] 的前綴 `P[0,j)` 中找到所有匹配的前綴與後綴長度 t，形成集合 N(P,j)
    - 且在集合中，應選裡面最大的 t
    - 若 j > 0，則 N(P,j) 中必有 0
    - 若 j = 0，則定義 N(P,j) 為 -1，即 next[0] = -1
    - ![](https://i.imgur.com/biBiFDJ.png)
    - ![](https://i.imgur.com/PLI0ovg.png)
    - ![](https://i.imgur.com/ODZuj1t.png)

- 構造
    - 流程跟 ＫＭＰ本身算法流程是一樣的
    - next[0] = -1
    - next[j] 下一個嘗試的位置是 next[next[j]]
    - ![](https://i.imgur.com/VUQSLl8.png)
    - ![](https://i.imgur.com/R5S8boP.png)

- 實現
    - ![](https://i.imgur.com/PngDpvR.png)

- 實例
    - ![](https://i.imgur.com/TTgnbXw.png)

#### 效率
- 使用分攤分析
    - 建立 next 表的複雜度 O(m)
    - ＫＭＰ (建表外)算法的複雜度 O(n)
    - 共  **O(m+n)**

#### 缺點
- 以下狀況時，ＫＭＰ 效率可能比蠻力算法更差
    - 因為 KMP 並沒有紀錄哪些嘗試已經失敗 (0跟1比)
    ![](https://i.imgur.com/9UyXbjO.png)

- 改進 Next 表
    - 還要確定現在的 P[j] 跟 P[t] 不一樣，才將 Next 表的值賦與 t
    - 否則當作失敗
    - ![](https://i.imgur.com/vtRAKKz.png)

### BM 算法 - BM_BC (BM_Bad Character)
- 壞字符算法
- 判斷兩個 string 相等 跟不等的代價是不一樣的
    - 判斷相等：需要完全相等
    - 判斷不等：只要發現一個不等就好了，只需要常數時間

- 策略：盡可能快速的**發現失敗**
    - 越靠後面出現的失配教訓越大，希望越早發現 (因為花了很多額外的成本去做前段的匹配)
    - 且越靠後面的失配，可以排除更多靠前的對齊位置的比對
    - 匹配失敗的機率比成功大
    - 所以應該將 P 的匹配的順序顛倒，改成自後向前

- 概念
    - ![](https://i.imgur.com/MbkNPg9.png)
    - 與 KMP 相同，需要建立一個 bc 表，其紀錄遇到某壞字符時，文本可以位移多少
    - ![](https://i.imgur.com/MUfYi0S.png)
    - ![](https://i.imgur.com/o9Hcyd4.png)
    - ![](https://i.imgur.com/6FsiB2W.png)

- 建造 BC 表
- ![](https://i.imgur.com/0ujfmZT.png)


- 最好效率
    - ![](https://i.imgur.com/57GBV7H.png)
    
- 最壞效率
    - ![](https://i.imgur.com/k7p6nHR.png)

### BM 算法 - BM_GS (BM_Good Suffix)
- 跟 BC 互補
- BC 的錯誤是沒有利用經驗，可能導致最壞情況
- BM_GS 則是利用已經匹配成功的後綴來加速
    - 需要建立 GS 表

- 概念
![](https://i.imgur.com/8v555aj.png)
![](https://i.imgur.com/WBUWWJD.png)
![](https://i.imgur.com/OyyaHhI.png)

- 實例

![](https://i.imgur.com/ZVnab98.png)

- 建立 GS 表
- 需要 O(m) 的時間
![](https://i.imgur.com/oC34m5H.png)

## 綜合性能分析
### BM 之性能 (綜合使用 BC, GS)
- 需要 BC, GS 表的建造
    - BC 表要 O(字母表的規模)
    - GS 表要 O(m)
    - 共 O(子母表規模 + m)
- 查找
    - 最好 O(n/m)
    - 最差 O(n+m)

### 縱覽
- 縱軸是運行時間
    - 刻度由低而高是：n/m , n+m, n*m
    - 橫軸是單次比對的成功概率，當字母表越大，成功概率大致越低
- 由圖可見，KMP 的優勢在**成功概率越大時** (字母表越小時)
- BM 使用 BC+GS 時，效率最好

![](https://i.imgur.com/z1PXC5v.png)

## Karp-Rabin 算法
- 另類的 string matching 算法
- 將每一個 string 都轉換為**整數**，如此兩個 string 就很好比較

### 串亦為數
- 把 string 轉換為整數
- 字母表的大小 = d = 使用 d 進位制
- 如大寫英文表時， d = 26，使用 26 進位制
- ![](https://i.imgur.com/jYwo29v.png)

### 算法
- 困難
    - d太大時，數字會過大導致無法儲存
    - 
![](https://i.imgur.com/Y0cPWfl.png)

- 解決
    - 使用 hash 將過大的數字 mapping 到較小的域中
    - 將 p 使用 hash(p) 轉換成整數
    - 在 t 中使用蠻力方法，陸續將長度為 p 的字串用 hash 轉換為整數
    - ![](https://i.imgur.com/YkBlYVI.png)

- hash collision 及解決
    - 不同的 hash 值可能對應到相同的 bucket
    - 因此就算兩個 string 的 hash 值相同，還是要直接做一次字串的比對
    - ![](https://i.imgur.com/JRsei8e.png)

- 加速 hash
![](https://i.imgur.com/MMbMqBs.png)


# 第十二章 Sorting 排序

## QuickSort
- 跟 mergesort 一樣，使用 divide and counqure 的策略
    - merge sort 難點在於合
    - quick sort 難點在於分
- ![](https://i.imgur.com/ExIRPRJ.png)

- 算法
    - 重點在 partition 如何辦到
    - ![](https://i.imgur.com/grNnt1R.png)
    

### partition
- 透過 partition 來構造軸點
- Pivot 軸點 在正確位置上的點
    - 在他左側的元素，均不比他更大
    - 在他右側的元素，均不比他更小


- 如何選擇 pivot
    - 原序列中未必有軸點存在
    - 我們可以透過適當的交換，使**任一元素成為軸點**

- 構造 pivot
    - 通常選擇序列中第一個元素為 pivot
    - 把比 pivot 小的元素都放到左邊，大的都放到右邊，如此 pivot 就歸位了
    - ![](https://i.imgur.com/avTVvt2.png)
    - L 跟 G 都會一直增加，U 會一直縮短，直到 U 只剩一個位置時，便把 pivot 放入該位置


- 概念
    - ![](https://i.imgur.com/1Bialuj.png)

- 實例
    - 構造第一個元素 6 為軸點
    - ![](https://i.imgur.com/ZvyLePS.png)


- partition 算法本身需要 O(n) 時間
### 性能分析

- quicksort 是 **不穩定的**
    - 原序列中相同的元素，排序之後不會依照原本的先後順序排列
- 是就地演算法 in-place
- pivot 的選擇好壞會影響效率
    - 最好情況：每次 pivot 越均衡（軸點越靠近中央），效能 O(nlogn)
    - 最壞情況：每次 pivot 極不均衡（軸點是最大或最小的元素），效能 O(n^2^)
    - 平均情況：依計算，時間複雜度約為 1.39 * nlogn
    - 然而 pivot 的選擇是隨機的，無法杜絕最壞情況

## QuickSort 變種
- partition
    - pivot 一樣用第一個元素
    - 區段從原本的 L, U, G 變成 L, G, U
    - 並由 lo, mid, k , hi 四個點來區分 L, G, U
        - k 指向 U 中要被歸入 L 或 G 的元素
    - ![](https://i.imgur.com/9sUl0P3.png)
    - 如果 k 不小於軸點，則歸入 G 中。相反則歸入 L 中
    ![](https://i.imgur.com/g6NvSIO.png)

    - 最後則將 pivot 跟 mid (L 的末元素) 兩個元素交換
    - 實現
        - ![](https://i.imgur.com/jPhuCkm.png)
    - 實例
        - ![](https://i.imgur.com/atCS2vK.png)
    - partition 算法本身需要 O(n) 時間，每個元素都是 O(1)


- 效能
    - 是 in-place 演算法
    - 依然是不穩定的
        - 因為是滾動後移，會有一個 swap 的動作

## Selection 問題
- 由排序問題推廣的問題
- 從一個可排序的數組中，找到某些特定元素
    - 例如第 k 大的元素
    - 中位數
    - 眾數
- 解法之一是排序完後再來尋找，但因為排序本身成本高，所以希望找出其他方法

### k-selection
- 由小至大，找到次序為第 k 個元素
- median，長度為 n 的有序列 S 中，中位數是 S[n/2] 那個元素。 (無條件進位)
- media 是 k-selection 的特例，也是 k-selection 中難度最高的

### majority
- 無序向量中，有**一半以上**元素同為 m，則稱 m 為眾數 (跟平常的定義不太一樣)
    - 如果5個數中，有 3 個 3，則 3 為眾數
    - 如果6個數中，有 3 個 3，則 3 **不為眾數**，因為沒有一半以上
- 可能的解法
    - 可以發現，**眾數若存在，他必然也是中位數**，因此要解決眾數問題，可以先解決中位數問題
        - 找到中位數後，可以很快的在 O(n) 驗證他是否也是眾數
        - 然而找到中位數的成本過高
    - 除此之外，**眾數若存在，他必然也是頻繁數 mode**
        - 頻繁數 mode：**出現最多次的數**
        - 然而找到頻繁數的成本過高
    - 希望找到另一個較寬鬆、計算成本較低的必要條件，再從之中選出真正的眾數

- 利用 decrease and conquer
    - 想法
    - ![](https://i.imgur.com/X8CpekQ.png)
    - 先將 P 中的第一個元素作為 x，接著一直掃描，直到 x 佔了掃描元素中的一半
    - 算法
    - ![](https://i.imgur.com/HVN7GC5.png)

### k-selection 的通用算法
- quickSelect
    - 應用 quick sort 中的 partition 方法
    - 選到的 pivot 在歸位後如果在 k，就會是第 k 大的數
    - 最幸運：一開始選擇的 pivot 就是 k-th
    - 一般情況：第一個 pivot 歸位後，則可以依照 k 與 pivot index 的大小，排除掉 L 或 G 中的元素
    - ![](https://i.imgur.com/aR6RVMV.png)
    - **最壞的情況下，需要 O(n^2^)**

- linearSelect
    - 理論上只需要 O(n) 的時間，但其實常系數很大
    - 使否能用在 quick sort 上
        - 我们在本节中已经得到了最坏时间复杂度为O(n)的中位数选取算法，若把它用于快速排序的轴点选取，得到的快速排序最坏时间复杂度为O (nlogn)
        - 但實際上這個**方法不可行**，因為 linearSelect實際效率非常低，因為常係數很大
    - 是 quickSelect 的改進
    - 步驟
        - 需要一個常數 Q
        - 如果 n < Q，則直接採用一般的 select 算法
        - 否則，把序列分成 n/Q 段，並分段排序 (不需考慮排序效能，例如使用 insertion Sort)
        - 找到每個分段的中位數，總共會有 n/Q 個中位數
        - 透過遞迴，找到中位數們中的中位數
        - 以中位數為基準，把所有元素分類成小於、等於、大於中位數 (L, E, G)
    - 算法
        - ![](https://i.imgur.com/09a1Mep.png)
    - 複雜度
        - ![](https://i.imgur.com/58TICEQ.png)
        - ![](https://i.imgur.com/7ddGudg.png)
        - ![](https://i.imgur.com/oWCiqr4.png)
        - ![](https://i.imgur.com/ofSbH9c.png)
        -  m 是每小段序列中的中位數，M 是整個個中位數
        -  故白色部分是 1/4, 灰色也是 1/4
        -  每次問題規模都會減縮至 3/4

## Shell Sort
- 也稱作遞減增量法
- 把一維陣列當作二維矩陣來 sort
- 矩陣寬度(橫的方向)是依據 step sequence 來選
    - 每次都依照**行**(直的方向) 來排序，將 h-sorting 轉換成 h-orderED
    - 隨著排序次數，矩陣會越來越窄
    - 直到 step = 1，排序完成成為 1-ordered 後即為**有序序列**
- step sequence 會依據選擇不同，成為不同的算法
![](https://i.imgur.com/D4MO724.png)


- 實例
- ![](https://i.imgur.com/Aj89Ocb.png)

- 實作
    - 實際上不需要真的使用二維向量，僅透過一為向量就可以達到
    - ![](https://i.imgur.com/qiNsRoF.png)
    - 分段排序演算法：**要輸入敏感** (插入排序)
    - ![](https://i.imgur.com/Na4YYe6.png)
- step sequence 的選擇
    - 會大大影響 shell sort 的效率
    - shell sequence
        - 古老的序列，但有其缺點在
        - shell sequence = {1, 2, 4, 8, ...2^k^}
        - 因為除了 1 之外都是偶數，所以可能導致最壞情況發生，將使用 O(n^2^) 時間
        - 好的序列應該要**彼此互質**
        - ![](https://i.imgur.com/2Ddeqt4.png)
    - 後人還有研發許多其他序列(PS, Pratt 等)
- shell sort 的精妙之處
![](https://i.imgur.com/fuv65xt.png)
![](https://i.imgur.com/f4EjZBX.png)

- 因為逆序對只會出現在 (g-1)(h-1) 之中，且會越來越減少
    - 所以使用**插入排序**，因為 insertion sort 的時間複雜度正比於逆序對的數量(輸入敏感)

## 排序演算法的整理
### 常見演算法
- bubble sort: 反复比较相邻元素，若为逆序则交换，直至有序
- selection sort: 不断从原序列中取出最大元素
- merge sort: 将原序列前半部分和后半部分，分别排序，再将这两部分合并
    - 需要額外的空間
    - 不論輸入好壞都是 O(nlogn)
- insertion sort: 抓扑克牌时人们常用的排序方法
    - 是**輸入敏感**的演算法，若輸入已排序好，將只需要 O(n) 的時間，與其他演算法不同
- quick sort: 将原序列以轴点为界分为两部分，递归地对它们分别排序
    - 不穩定的演算法
- heap sort (min heap/max heap): 不断从原序列中取出最小/大元素
    - 不論輸入好壞都是 O(nlogn)
    - 是 in-place 演算法，只需要 O(1) 的額外空間
- shell sort：
    - 使用不同的 step sequence 會得到不同的時間複雜度
    - 若使用 shell sequence 則最壞時間複雜度要花 **O(n^2^)**，最好是 O(n)
    - 不穩定的演算法

![](https://i.imgur.com/vXXV1jh.png)

### 延伸
- Introsort 内省排序
    - 为了防止快速排序因为总是选取到不平衡的轴点而变得低效，除了"三者取中"之外还可以用其它方法。查阅快速排序的一个变种——内省排序(Introsort)是如何解决这个问题的，它将**堆排序**与**快速排序结合**（它是STL中sort()函数的主要实现方法）
    - 是由David Musser在1997年设计的排序算法。这个排序算法首先从快速排序开始，当递归深度超过一定深度（深度为排序元素数量的对数值）后转为堆排序。采用这个方法，内省排序既能在常规数据集上实现快速排序的高性能，又能在最坏情况下仍保持 O ( n l o g n ) {\displaystyle O(nlogn)} O(nlogn)的时间复杂度。由于这两种算法都属于比较排序算法，所以内省排序也是一个比较排序算法。 