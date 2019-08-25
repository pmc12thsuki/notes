JS Design pattern
===
###### tags: `Design Pattern`

## CH1 全域與Module
### Problem with global scope
- 在直接撰寫前端的 script 比較會遇到
- 我們常常將所有的 function 定義在同一個層級裡 (有時是 global)
- 然而有一些 function 需要是 private 的，不應該被直接呼叫
- 例如下列都定義在 global scope，但我們不希望 echo 及 變數被直接呼叫
- 我們只想 export 出 `talk`、`replayYesNo`、`saySassyStuff`
    - echo 應該只能被其他 function 呼叫

```javascript=
var leadself= 'Me: ',
    leadcomputer= "PC: ",
    aSaid= ["This is a Cyber Chat"],
    msgYes= "Yes, that's a great idea.",
    msgNo= "No, that must be a mistake.",
    aSassyStuff= ["Like mold on books, grow myths on history.",
                        "She moved like a poem and smiled like a sphinx.",
                        "As long as we don’t die, this is gonna be one hell of a story.",
                        "She laughed, and the desert sang.",
                        "You’ve got about as much charm as a dead slug."];

function echo(msg) {
      aSaid.push(`<div>${msg}</div>`);
      const aSaidLength = aSaid.length;
      const start = Math.max(aSaidLength - 6, 0);
      let out = '';
      for (let i = start; i < aSaidLength; i++) {
        out += aSaid[i];
      }
      $('.advert').html(out);
      $('#talk span').text(msg);
    }

function talk(msg) {
  echo(leadself + msg);
}

function replayYesNo() {
  const msg = Math.random() > 0.5 ? msgYes : msgNo;
  echo(leadcomputer + msg);
}

function saySassyStuff() {
  const msg = aSassyStuff[Math.floor(Math.random() * aSassyStuff.length)];
  echo(leadcomputer + msg);
}
```
### Namespace / Object Litteral Pattern
#### Object literal design pattern
- 最簡單的 design pattern（常不被當成 design pattern）
- 把原本放在 global 的東西都放到一個 big object 中，減少 global 的污染
- 因為東西都被放進 object 中，所以 `function 會變成匿名 function`，function 跟其他變數都成位 obj 中的一個 field
- 要使用/呼叫 obj 中的其他 field 時，要**記得加上 this**

- 例如：
```javascript=
var o = {
// 省略定義常數，包含 aSaid, leadsel 等等
    echo: function (msg) {
          this.aSaid.push(`<div>${msg}</div>`);
          const aSaidLength = this.aSaid.length;
          const start = Math.max(this.aSaidLength - 6, 0);
          let out = '';
          for (let i = start; i < this.aSaidLength; i++) {
            out += this.aSaid[i];
          }
          $('.advert').html(out);
          $('#talk span').text(msg);
        },

    talk: function (msg) {
      this.echo(this.leadself + msg);
    },

    replayYesNo: function () {
      const msg = Math.random() > 0.5 ? this.msgYes : this.msgNo;
      this.echo(this.leadcomputer + msg);
    },

    saySassyStuff: function () {
      const msg = this.aSassyStuff[Math.floor(Math.random() * this.aSassyStuff.length)];
      this.echo(this.leadcomputer + msg);
    }
}

```


#### Namespace design pattern
- 避免 override 其他東西
- 例如我們上面叫 obj 為 `o`，會非常有可能複寫（override）全域中的其他變數
- 例如可能複寫其他引用的函式庫（如 jquery之類）的全域變數
- 解決方式是要取一個 **`較長且 unique`** 的 namespace
- create a unique path for your project that no one else have chance to override it
- 假設一個 namespace 是 `com.o2geek.packt.JSDP.sudoChat`
    - com 底下
    - 是為了 company domain/personal domain 寫的 (o2geek)
    - 這個 project 名稱 (packt)
    - project 的 folder 名稱 (JSDP)
    - 檔名 (sudoChat)
- 要確保 namespace 的上層物件都存在
- 因此上述範例會變成：

```javascript=
// 要先確定 namespace 的上層物件都存在
var com = com || {}; // 如果 com 存在就使用 com，不存在就 create
com.o2geek = com.o2geek || {};
com.o2geek.packt = com.o2geek.packt || {};
com.o2geek.packt.JSDP =com.o2geek.packt.JSDP || {};

var com.o2geek.packt.JSDP.sudoChat = {
// 省略，為原本 o 裡面的 field
}

```

### Module Design Pattern
- 在 JS 中難以將物件真正的變成 private
- 以上的 design pattern 解決了使用 global scope 的問題，但物件依然不是 private 的 (client 端依然可以直接呼叫 echo)
- 透過 Module Design Pattern 可以
    - 創造真正的 privacy contral
    - 可以創造 clear public API
    - 達到 true encapsulation (封裝)
- 建立一個 IIFE (Immediately Invoked Function Expression)
    - 把應該要是 private 的定義在 IIFE 的 function scope 中，就無法從外層直接呼叫
    - 只把想給外層呼叫的 function 定義在 return 回來的 obj 中

```javascript=
var chatModule = (function (){

    // 這些變數被定義在 IIFE 的 function scope 中，在外面是無法呼叫的
    // 在 function scope 中定義是直接用 var，跟定義在 obj 中不一樣

    var leadself= 'Me: ',
        leadcomputer= "PC: ",
        aSaid= ["This is a Cyber Chat"],
        msgYes= "Yes, that's a great idea.",
        msgNo= "No, that must be a mistake.",
        aSassyStuff= ["Like mold on books, grow myths on history.",
                            "She moved like a poem and smiled like a sphinx.",
                            "As long as we don’t die, this is gonna be one hell of a story.",
                            "She laughed, and the desert sang.",
                            "You’ve got about as much charm as a dead slug."];
                            
    // echo 不想被外界呼叫，所以也定義在這邊
    function echo(msg){

            aSaid.push("<div>" + msg + "</div>");

            var aSaidLength = aSaid.length,
                    start = Math.max(aSaidLength - 6,0),
                    out = "";

            for(var i=start; i<aSaidLength; i++){
                out += aSaid[i];
            }


            $('.advert').html(out);
            $('#talk span').text(msg);
        }

    // 只把要 export 出的 function 定義在 return 的 obj 中    
    return { 
            talk: function (msg) {
            echo(leadself + msg);
        },
            replayYesNo: function (){
                var msg = Math.random()>.5 ? msgYes : msgNo;
                echo(leadcomputer + msg);
        },
            saySassyStuff: function (){
                var msg = aSassyStuff[ Math.floor(Math.random()*aSassyStuff.length) ];
                echo(leadcomputer + msg);
        }

    };
})(); // IIFE

chatModule.replayYesNo(); // 只可以呼叫被 export 的函數
```

### Module Reveal Design Pattern
- **是上述 Module Design Pattern 的升級版**
- 在 Module Design Pattern 中，我們對待 public 跟 private member 是不一樣的，增加了 code 的 complexity
    - 要 export 出去的 function，我們用 obj 包起來，因此寫法會跟 private function 的定義方式不一樣
- 在 Module Reveal Design Pattern 中，我們把所有東西都定義在 IIFE 的 function scope 中，**並且使用 `_` 來區分 private/ public 變數**，再把要 export 的東西 return 給外面

- 缺點（？）是我們無法 override 那些被 export 出來的 public method 了，因為我們沒有辦法 access 這些 function，只能 access 到他們的 reference


- 缺點是我們依然將 chatModule 直接定義在 global 中
```javascript=
const chatModule = (function () {
    const _leadself = 'Me: ';
    const _leadcomputer = 'PC: ';
    const _aSaid = ['This is a Cyber Chat'];
    const _msgYes = "Yes, that's a great idea.";
    const _msgNo = 'No, that must be a mistake.';
    const _aSassyStuff = ['Like mold on books, grow myths on history.',
      'She moved like a poem and smiled like a sphinx.',
      'As long as we don’t die, this is gonna be one hell of a story.',
      'She laughed, and the desert sang.',
      'You’ve got about as much charm as a dead slug.'];
      
    function _echo(msg) {
      _aSaid.push(`<div>${msg}</div>`);
      const aSaidLength = _aSaid.length;
      const start = Math.max(aSaidLength - 6, 0);
      let out = '';
      for (let i = start; i < aSaidLength; i++) {
        out += _aSaid[i];
      }
      $('.advert').html(out);
      $('#talk span').text(msg);
    }
    
    function talk(msg) {
      _echo(_leadself + msg);
    }

    function replayYesNo() {
      const msg = Math.random() > 0.5 ? _msgYes : _msgNo;
      _echo(_leadcomputer + msg);
    }
    
    function	saySassyStuff() {
      const msg = _aSassyStuff[Math.floor(Math.random() * _aSassyStuff.length)];
      _echo(_leadcomputer + msg);
    }

    return {
      talk,
      replayYesNo,
      saySassyStuff,
    };
  }());
```

### Controlling the Clobal Access Completely
- 上述的 Module Reveal Design Pattern 中，我們依然將 chatModule 定義在 global scope 中。而且沒有使用 unique 的 namespace 定義它
- 一個不想污染 global 的好方法是，我們不用定義 chatModule 在 global 中後再執行，而是直接定義一個 **`外層的 IIFE 在 global 中執行`**
    - 我們可以把全域的變數如 `window`、`document`、`jQuery`，傳進去給這個 IIFE 的 scope，避免 IIFE 直接向外查找到 global 中的 window ..等
    - 因為在 IIFE 中可以 access 到 window，所以在 export API 給 window 前，**還可以先檢查 window 中有沒有已經定義的 chatModule**！避免直接 override global ！！
    - 擁有 conditionally add element to the global scope 的能力
```javascript=
// global scope
(function (win, doc, $) { // 在 global 中直接執行 IIFE
// win, doc, $ 是 IIFE 執行時，從 global 傳進來的 window, document, jQuery

  const chatModule = (function () {
    const _leadself = 'Me: ';
    const _leadcomputer = 'PC: ';
    const _aSaid = ['This is a Cyber Chat'];
    const _msgYes = "Yes, that's a great idea.";
    const _msgNo = 'No, that must be a mistake.';
    const _aSassyStuff = ['Like mold on books, grow myths on history.',
      'She moved like a poem and smiled like a sphinx.',
      'As long as we don’t die, this is gonna be one hell of a story.',
      'She laughed, and the desert sang.',
      'You’ve got about as much charm as a dead slug.'];
    function _echo(msg) {
      _aSaid.push(`<div>${msg}</div>`);

      const aSaidLength = _aSaid.length;
      const start = Math.max(aSaidLength - 6, 0);
      let out = '';
      for (let i = start; i < aSaidLength; i++) {
        out += _aSaid[i];
      }
      $('.advert').html(out);
      $('#talk span').text(msg);
    }
    function talk(msg) {
      _echo(_leadself + msg);
    }
    function replayYesNo() {
      const msg = Math.random() > 0.5 ? _msgYes : _msgNo;
      _echo(_leadcomputer + msg);
    }
    function	saySassyStuff() {
      const msg = _aSassyStuff[Math.floor(Math.random() * _aSassyStuff.length)];
      _echo(_leadcomputer + msg);
    }
    return {
      talk,
      replayYesNo,
      saySassyStuff,
    };
  }());

  $(doc).ready(() => {
    chatModule.talk('this is great');
    chatModule.replayYesNo();
    chatModule.saySassyStuff();
  });
  
  // 依然可以 creat public API，這邊可以先檢查 window 中有沒有已經定義的 chatModule！避免直接 override global ！！ 
  // 如果名稱真的重複，還可以定義其他名稱來 export API
  if (!win.chatModule) win.chatModule = chatModule;
 
}(window, document, jQuery)); // end of IIFE
// 把全域的變數如 `window`、`document`、`jQuery`，傳進去給這個 IIFE 的 scope

// 在 global 中有了 chatMoudule API
console.log(window.chatModule);
```

## CH2 Creational Design Pattern
- 跟 Object 創造有關的 design patterns
- 包含
    - Singleton design pattern
    - Facroty design pattern
    - Abstract Factory design pattern
    - Builder design pattern
    - Prototype design pattern

- 原始 code
    - 在 window 中 click 一下就會在畫面中加上一個 circle
```javascript=
(function(win, $){
	$(win.document).ready(function(){
		$('.advert').click(function(e){
			var circle = $('<div class="circle"></div>');
				circle.css('left',e.pageX-25);
				circle.css('top',e.pageY-25)
			$('.advert').append(circle);
		});

	});

})(window, jQuery);
```
### 單例模式 Singleton Design Pattern
- 特點
    - 可以延遲產生 object 實例
        - delayed instantiation of the object
        - 當不想要 app start 時就 object instance 就馬上被 initialize，而是在特定的時間點發生
        - 例如：只想要在有 click event 發生時，才 create 負責產生 circle 的 object instance
    - 只會有一個 object 的實例存在
        - only one instance of the object is needed
        - only one instance in one application
        - 例如：如果已經有 click event 發生過，則下一次 click 發生時，都會拿到 **`已經存在的那個 instance`**
    - 有 global 的 interface 可以操作這個 object 
        - a constant interface to gain access to the object
        - 任何想要操作這個 object instance 的 item 都可以透過 global 的 interface 操作它
        - 例如：在 click event 跟 keypress event 都可以 access 到這個 object instance（而且是同一個 instance）
    
- **DONT OVER USE IT**，確保我們想到達到以上特點時才使用
- 範例
    - 整個 CircleGeneratorSingleton 只 return `getInstance` 這個方法，透過 getInstance 來確保 app 中都會拿到**唯一的一個 instance**
    - 只有在第一次呼叫 `getInstance` 時，object 實例才真正被創造
    - init 中，只 return 被 **instance 公開的 api 方法**
    - 要區別
        - getInstance 是 CircleGeneratorSingleton 的 API
            - 可以想像成 class
        - index, add, create 是 CircleGeneratorSingleton **的實例** 的 API 方法
            - 可以想像成 class 的實例
```javascript=
(function(win, $){ // 使用 CH1 的 Module Reveal Pattern

	var CircleGeneratorSingleton = (function(){
		var instance; // 唯一的 instance
		function init(){ // 把所有跟 instance 有關的方法都定義在 init 中
			var _aCircle = [], _stage = $('.advert');

			function _position(circle, left, top){
				circle.css('left',left);
				circle.css('top',top);
			}

			function create(left, top){
				var circle = $('<div class="circle"></div>');
				_position(circle,left, top);
				return circle;
			}

			function add(circle){
				_stage.append(circle);
				_aCircle.push(circle);
			}

			function index(){
				return _aCircle.length;
			}

			return { // instance 公開的 API
                           index:index,
			    create:create, // 創造一個 circle
			    add:add // 把 circle 加上畫面上
                        };
		}

		return {
			getInstance: function(){
                        // 如果沒有存在的 instance，才 init 一個
				if(!instance){ 
					instance = init();
				}
				return instance;
			}
		}
	})();

	$(win.document).ready(function(){
            $('.advert').click(function(e){
                var cg = CircleGeneratorSingleton.getInstance();
                var circle = cg.create(e.pageX-25, e.pageY-25);
                cg.add(circle);

            });

            $(document).keypress(function(e){
                if(e.key=='a'){
                    var cg = CircleGeneratorSingleton.getInstance();
                    var circle = cg.create(Math.floor(Math.random()*600),Math.floor(Math.random()*600)); 
                    cg.add(circle);
                }
            });
	});

})(window, jQuery);
```

### 工廠模式 Factory Design Pattern
- 方便創造多個 object instance
- 特點
    - When we need more control on customizing an item
        - 例如：我們想要創造紅色跟藍色的 circle
    - Factory 可以不知道實際怎麼產出（implement）這個 object，而是提供一個介面 
        - can remove the need for the factory (the implementing object) to know hot to implement
        - 例如：Factory 不需要知道怎麼實際產出紅色/藍色的 circle
    - easy to update and add more versions or update of the object
        - 例如：我們可以很輕易地加上「產出黃色的 circle」的方法
- 範例：
    - CircleFactory 不需要知道**怎麼創造**顏色 circle，只需要知道要創造哪個 circle
    - 在 CircleGeneratorSingleton 中，我們改透過 CircleFactory 來創造 circle
    - 如此可以透過 CircleGeneratorSingleton **創造 cg (circle generator)**
        - 在 app 中我們只需要一個 circle generator
    - 在 cg 中，再透過 CircleFactory 來創造 circle 各種 circle
        - circle generator 透過 CircleFactory 可以創造出多個不同種類的 circle
    - 我們把實際 create circle 的工作交給 Factory，降低了 circle generator 需要知道如何 create circle 的程度
    - 缺點：CircleFactory 還是需要決定要產生什麼顏色的 circle
```javascript=
(function(win, $){

    const RedCircle = function(){this.item = $('<div class="circle"></div>');}
    const BlueCircle = function(){this.item = $('<div class="circle" style="background:blue"></div>');}
    const CircleFactory = function(){
        this.create = function(color){
            if(color === 'blue') {
                // 注意，這邊是用 new ! 創造一個新的 circle obj
                return new BlueCircle();
            }else{
                return new RedCircle();
            }
        }
	};
    
   
    const CircleGeneratorSingleton = (function(){
        var instance;
        function init(){
            var _aCircle = [],
                _stage = $('.advert'),
                _cf = new CircleFactory();

            function _position(circle, left, top){
                circle.css('left',left);
                circle.css('top',top);
            }

            function create(left, top,color){
                var circle = _cf.create(color).item;
                _position(circle,left, top);
                return circle;
            }

            function add(circle){
                _stage.append(circle);
                _aCircle.push(circle);
            }

            function index(){
                return _aCircle.length;
            }

            return {
                index:index,
                create:create,
                add:add
            };
        }

        return {
            getInstance: function(){
                if(!instance){
                    instance = init();
                }

                return instance;
            }
        }

    })();

    $(win.document).ready(function(){
        $('.advert').click(function(e){
            var cg = CircleGeneratorSingleton.getInstance();
            var circle = cg.create(e.pageX-25, e.pageY-25,"red");
            cg.add(circle);

        });

        $(document).keypress(function(e){
            if(e.key=='a'){
                var cg = CircleGeneratorSingleton.getInstance();
                var circle = cg.create(Math.floor(Math.random()*600), Math.floor(Math.random()*600),"blue");
                cg.add(circle);
            }

        });

    });

})(window, jQuery);
```

### 抽象工廠模式 Abstract Factory Design Pattern
- 是 Factory 的加強版，通常會先實作 Factory，然後在有需要時再變成 Abstract Factory
- 特色
    - 當需要 Factory 變得更 Dynamic 時
    - Abstract Factory 的好處是可以為 Factory 加上更多 Feature，且不需要去修改 Factory 本身，因為 factory 本身完全不涉及實作
    - 通常可以先用 Factory 就好，能避免過於複雜，只有真正需要時再使用 Abstract Factory
    - 通常會用 Interface 實現，但 JS 中沒有這個功能，就用類似的概念 (prototype) 實作
        - object 之於 prototype，用來實現 class 之於 interface 的概念
        - 例如：透過實作 object.prototype.create，代表這個 object 有實作 create 這個 interface 
        - 我們在實現 interface 時，才真正去實作這個物件的 implementation
    - 透過實作 register 方法，我們把「決定」要註冊哪些 circle 的權力交到 CircleGeneratorSingleton 裡，這是可以被動態決定的
        - 透過 register，我們可以決定某個 'type' 要使用哪個 class 實現
        - 在 CircleGeneratorSingleton 裡，cf 註冊了當要產生 red 這個 type 時，會用 RedCircle 這個 class (function)，blue 同理
        - 如果之後我們需要另一個 cf，且 red 這個 type 要用別的 class 實作，也會非常容易達到
    - 分離了哪個 type 要用哪個 class 的邏輯，並交給使用者決定，而不是由 factory 決定
        

- 範例
```javascript=
(function(win, $){
	function RedCircle(){
		
	}
    // 使用 prototype 來實作 js 中 interface 的概念
    // 如果某個 object.prototype 有實作 create，代表這個 object 有實作這個 interface
    // 在實作 interface 時，才真正 implement 物件的實現內容
	RedCircle.prototype.create = function() {
		this.item = $('<div class="circle"></div>');
		return this;
	};
	
	function BlueCircle(){
		
	}

	BlueCircle.prototype.create = function() {
		this.item = $('<div class="circle" style="background:blue"></div>');
		return this;
	}; 	

	CircleFactory = function(){
			this.types = {};
			this.create = function(type){
            // 找到實現這個 type 的 function (class)，實體化並使用他的 create 方法
				return new this.types[type]().create();
			};

			this.register = function(type, cls){
            // 先確保 class (object) 有實作 interface (prototype)
				if(cls.prototype.create){
						this.types[type] = cls;
				}
			}
	};

	var CircleGeneratorSingleton = (function(){
		var instance;

		function init(){
			var _aCircle = [],
					_stage = $('.advert'),
                    // 這個 cf 被註冊了，當要產生 red 這個 type 時，會用 RedCircle 這個 class (function)，blue 同理
                    // 如果之後我們需要另一個 cf，且 red 這個 type 要用別的 class 實作，也會非常容易達到
					_cf = new CircleFactory();
					_cf.register('red', RedCircle);
					_cf.register('blue', BlueCircle);

			function _position(circle, left, top){
				circle.css('left',left);
				circle.css('top',top);
			}

			function create(left, top,type){
				var circle = _cf.create(type).item;
				_position(circle,left, top);
				return circle;
			}

			function add(circle){
				_stage.append(circle);
				_aCircle.push(circle);
			}

			function index(){
				return _aCircle.length;
			}

			return {index:index,
							create:create,
							add:add};
		}

		return {
			getInstance: function(){
				if(!instance){
					instance = init();
				}

				return instance;
			}
		}

	})();

	$(win.document).ready(function(){
		$('.advert').click(function(e){
			var cg = CircleGeneratorSingleton.getInstance();
			var circle = cg.create(e.pageX-25, e.pageY-25,"red");

			cg.add(circle);
				
		});

		$(document).keypress(function(e){
			if(e.key=='a'){
				var cg = CircleGeneratorSingleton.getInstance();
				var circle = cg.create(Math.floor(Math.random()*600),
															Math.floor(Math.random()*600),
															"blue");
				
				cg.add(circle);
			}
			
		});

	});

})(window, jQuery);
```