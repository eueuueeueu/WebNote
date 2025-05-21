# 1、谈谈你对于cookie、 localStorage、sessionStorage三者的理解

**相同点：**三者都是用于`客户端`数据存储 且 都受到`同源策略`的限制

**不同点:**

1. 存储的数据量不一样，`cookie`存储的数据少(4kb左右)，`localStorage`、`sessionStorage`存储的数据量更多(约5MB左右)
2.  三者的数据有效期不一样，`cookie`只在设置的有限期内有效。`localStorage`：持久化存储，始终有效，窗口或浏览器关闭也一直保存。`sessionStorage`：仅在当前浏览器窗口关闭之前有效；
3.  `cookie`在浏览器和服务器间来回传递，而`sessionStorage`和`localStorage`不会自动把数据发送给服务器，仅在本地保存。
4. 作用域不同，`cookie` 和`localstorage`在所有同源窗口中都是共享的,`sessionStorage`即使是在同一个页面,数据也不会共享。

**实际开发中的使用**

> -  [js-cookie ](https://www.npmjs.com/package/js-cookie/v/2.2.1)
> -  [store.js](https://www.npmjs.com/package/storejs)
> -  [localforage ](https://github.com/localForage/localForage)

![cookie、 localStorage、sessionStorage](images/cookie&sessionStorage&localstorage.png)

# 2、JS中简单数据类型和引用数据类型的区别？

- **简单数据类型**：值直接存储，赋值时复制值，比较时比较值。
- **引用数据类型**：值存储在堆内存中，赋值时复制引用地址，比较时比较引用地址。

![](images/基本数据类型和引用数据类型区别.png)

# 3、什么是深拷贝浅拷贝，如何实现？

 **1. 浅拷贝（Shallow Copy）**

仅复制对象的**第一层属性**。如果属性是基本类型（如 `number`, `string`），直接复制值；如果属性是引用类型（如 `object`, `array`），则复制其内存地址（新旧对象共享嵌套的引用）

**实现方式**：

- `Object.assign()`：

```js
const obj = { a: 1, b: { c: 2 } };
const shallowCopy = Object.assign({}, obj);
```

- 扩展运算符 `...`：

```js
const shallowCopy = { ...obj };
```

- 数组方法（如 `slice`, `concat`）：

```js

const arr = [1, 2, { a: 3 }];
const shallowArrCopy = arr.slice();

```

**2. 深拷贝（Deep Copy）**

递归复制对象的所有层级属性，完全断开与原对象的引用关系。无论属性是基本类型还是引用类型，都会创建全新的副本。

**实现方式**：

- `JSON.parse(JSON.stringify(obj))`

```js

const obj = { a: 1, b: { c: 2 } };
const deepCopy = JSON.parse(JSON.stringify(obj));

```

**局限性**：无法处理函数、`undefined`、`循环引用`、`Symbol` 等类型。

- 递归实现：

```js
function deepClone(source) {
  if (typeof source !== 'object' || source === null) return source;
  const target = Array.isArray(source) ? [] : {};
  for (const key in source) {
    if (source.hasOwnProperty(key)) {
      target[key] = deepClone(source[key]);
    }
  }
  return target;
}
```

- 使用第三方库（如 `lodash.cloneDeep`）：

```js
import _ from 'lodash';
const deepCopy = _.cloneDeep(obj);
```

# 4、箭头函数和普通函数的区别

1. **语法层面**

- **普通函数**：用 `function` 关键字定义，有具名和匿名形式，函数体用花括号包裹，多条语句时不能省略。
- **箭头函数**：用箭头 `()=>` 定义，参数括号和花括号在特定情况可省略，语法更简洁。

2. `this` **指向 **

- **普通函数**：`this` 值在调用时动态确定，一般情况下谁调用指向谁
- **箭头函数**：没有自己的 `this`，继承自外层函数的 `this` 值，且不随调用方式(call,apply,bind)改变。

3. `arguments` **对象**

- **普通函数**：函数内部有 `arguments` 对象，是类数组，包含调用时的所有参数。
- **箭头函数**：没有自己的 `arguments` 对象，使用时引用外层函数的 `arguments` 对象。

4. **构造函数使用**

- **普通函数**：可以用 `new` 关键字作为构造函数创建对象实例。
- **箭头函数**：不能用 `new` 调用，因为无自己的 `this` 和 `prototype`，不是构造函数。

# 5、说说new操作符具体干了什么？

**1、是什么**：在`JavaScript`中，`new`操作符用于创建一个给定构造函数的实例对象

```js
function Person(name, age){
    this.name = name;
    this.age = age;
}
const person1 = new Person('Tom', 20)
console.log(person1)  // Person {name: "Tom", age: 20}
t.sayName() // 'Tom'
```

**2、流程**

1. 创建一个新的对象`obj`
2. 将对象与构造函数通过原型链连接起来
3. 将构造函数中的`this`绑定到新建的对象`obj`上
4. 根据构造函数返回类型作判断，如果是原始值则被忽略，如果是返回对象，需要正常处理

<img src="images/new操作流程图.png" style="zoom:80%;" />



**3、手写new操作符**

```js
function mynew(Func, ...args) {
    // 1.创建一个新对象
    const obj = {}
    // 2.新对象原型指向构造函数原型对象
    obj.__proto__ = Func.prototype
    // 3.将构造函数的this指向新对象
    let result = Func.apply(obj, args)
    // 4.根据返回值判断
    return result instanceof Object ? result : obj
}
```

# 6、（原生AJAX）XMLHttpRequest (XHR)发送get/post请求的步骤

1. 通过调用其[构造函数](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/XMLHttpRequest)创建新的 `XMLHttpRequest` 实例`xhr`
2. 通过调用 [`XMLHttpRequest.open（）`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/open) 方法对其进行初始化，并提供请求的方法和请求 URL
3. 给`xhr`实例添加事件监听以获取请求的结果，例如[`load`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/load_event) 事件在请求成功完成时触发，而 [`error`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/error_event) 事件在各种错误情况下触发。
4. 通过调用 [`XMLHttpRequest.send（）`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/send) 发送请求。

```js
const url =
  "https://raw.githubusercontent.com/mdn/content/main/files/en-us/_wikihistory.json";  
const xhr = new XMLHttpRequest();
  xhr.open("GET", url);
  addListeners(xhr);
  xhr.send();
```

# 7、cookie跨域共享如何实现

跨域Cookie共享的实现需要克服浏览器的同源策略限制，以下是主要方法

1. 子域名共享
    如果多个子域名属于同一个主域名，可通过设置 Cookie 的 `domain` 属性实现 Cookie 在子域名间共享。

  ```js
  // 在a.example.com和b.example.com设置Cookie
  document.cookie = "name=value; domain=.example.com; path=/";
  ```

  此时所有子域（如a.example.com、b.example.com）均可读取该Cookie。

2. CORS（跨域资源共享）
    服务器通过设置响应头允许跨域请求，并支持在请求中携带 Cookie
    **前端**：发送请求时设置`withCredentials: true`

  ```js
  fetch('https://another-domain.com/api', {
    credentials: 'include'
  });
  ```

  **后端**：配置响应头：

  - `Access-Control-Allow-Origin: 具体域名`（不可为`*`）
  - `Access-Control-Allow-Credentials: true`
  - 可选`Access-Control-Expose-Headers`暴露自定义头。

3. 反向代理服务器
	**原理**：通过同源代理服务器转发请求。
	例：将`/api`请求代理到`https://another-domain.com`

	```nginx
	location /api {
	  proxy_pass https://another-domain.com;
	}
	```

	搭建中间代理服务器，客户端请求先发送到代理服务器，代理服务器再转发请求到目标服务器，并处理 Cookie 传递。
	代理服务器和客户端处于同一源，客户端请求代理服务器时可正常携带和接收 Cookie。代理服务器将请求转发到目标服务器，并将目标服务器的响应返回给客户端。

4. 前端跨窗口通信（PostMessage）

	- 域A通过`window.open()`或`iframe`打开域B的页面。
	- 域B通过postMessage发送Cookie数据到域A。

# 8、JS的继承方式有哪些？如何实现

## 1、原型链继承

每个构造函数都有一个原型对象（prototype），每个实例对象包含一个指向原型对象的指针（\__proto\__)。这样，每当代码读取实例的某个属性时，都会首先在实例上搜索这个属性，如果没有找到，则会搜索原型对象。
*==代码示例如下==*

```js
	function Person(name) {
            this.name = name
        }
 	Person.prototype.sayHello = function () {
 	    console.log('hello,my name is ' + this.name);
 	}

	let person1 = new Person('Alice')
	let person2 = new Person('Bob')
	person1.sayHello()//hello,my name is Alice
	person2.sayHello()//hello,my name is Bob
```

原型链继承的一个主要问题是包含引用类型值的原型属性会被所有实例共享。换而言之，如果一个实例改变了该属性，那么其他实例的该属性也会被改变。
比如以下代码就会出问题：我明明只想修改了 child1 的arr，我不想修改 child2 的 arr。但是实际结果却是， child2 的 arr **也被修改了**。

```js
		// 定义父类
        function Parent() {
            this.arr = [1, 2, 3];
        }

        // 定义子类
        function Child() { }

        // 子类继承父类，这里是关键，实现原型链继承
        Child.prototype = new Parent();
		
        // 实例化子类
        var child1 = new Child();
        var child2 = new Child();
        
        child1.arr.push(4);

        console.log(child1.arr);//[1, 2, 3, 4]
        console.log(child2.arr);//[1, 2, 3, 4]
```

## 2.构造函数继承

```js
       function Person() {
            this.myFun = function () {
                console.log('我是Person构造函数的实例方法');
            }
            this.arr = [1, 2, 3]
        }
	
        Person.prototype.sayHello = () => {
            console.log('我是Person构造函数的原型方法 sayHello');
        }
        function Child() {
            // 构造函数中的this指向通过该构造函数实例化的对象
            // 子类构造函数中执行父类构造函数,这样子类实例对象中就有了父类的实例方法/属性
            // 指向父类中的构造函数（修改this的指向）
            Person.call(this)
        }
	
        const child1 = new Child();
        const child2 = new Child();
        console.log(child1.arr === child2.arr);//false
        console.log(child1.myFun === child2.myFun);//false

        child1.sayHello()//sayHello is not a function
        child2.sayHello()//sayHello is not a function
```

构造函数继承也有缺点：子类无法继承父类原型对象上的属性/方法

so=> 我们有了组合继承 =原型链继承+构造函数继承

## 3.组合继承

组合继承 =原型链继承+构造函数继承

```js
// 组合继承可以理解为 原型链继承 + 构造函数继承
        // 父类
        function Parent() {
            // 实例方法 sayHello
            this.sayHello = function () {
                console.log("Hello");
            };
        }
        // 原型属性 
        Parent.prototype.a = "我是父类prototype上的属性";
        // 原型方法
        Parent.prototype.sayHello = () => {
            console.log('我是Person构造函数的原型方法 sayHello');
        }

        // 子类
        function Child() {
            // 子类构造函数中执行父类构造函数,这样子类实例对象中就有了父类的实例方法/属性
            Parent.call(this);
        }
        // 将子类构造函数的原型对象指向 通过父类构造函数实例化的一个对象
        // 子类能够继承父类中的实例方法/属性+原型方法/属性
        Child.prototype = new Parent();
        var child1 = new Child();
        console.log(child1.a);
        child1.HaHaHa()
```

这种方式也有缺点，它调用了2次Parent()，第一次是在 创建child1对象时，Child构造函数中

 执行了 Parent.call(this)，第二次是  Child.prototype = new Parent();

## 4.寄生组合式继承

```js
        // 父类
        function Parent() {
            this.sayHello = function () {
                console.log("Hello");
            };
        }
        Parent.prototype.a = "我是父类prototype上的属性";
        // 子类
        function Child() {
            Parent.call(this);
        }
        Child.prototype.childFunc = function () {
            console.log('我是Child原型对象上的方法');
        }

        // 创建一个没有实例方法的父类实例作为子类的原型
        // Object.create() 静态方法以一个现有对象作为原型，创建一个新对象。
        Child.prototype = Object.create(Parent.prototype);
        // 修复构造函数的指向
        // Object 实例的 constructor 数据属性返回一个引用，指向创建该实例对象的构造函数。
        Child.prototype.constructor = Child;
        var child1 = new Child();
        console.log(child1);
       
        child1.childFunc()//child1.childFunc is not a function
```

寄生组合继承也有缺点，子类原型对象上的属性/方法会丢失

# 9、跨域问题具体限制了什么

1. 限制DOM 访问
  1. 跨域 iframe 操作

如果父页面和子页面不同源，父页面无法通过 JavaScript 访问子页面的 DOM（如 `contentWindow` 或 `contentDocument`）。

  2. 跨窗口通信

`window.open` 或 `window.opener` 打开的跨源窗口无法直接访问彼此的属性和方法。

2. 限制网络请求

	1. AJAX / Fetch 请求
		浏览器默认阻止跨域 AJAX 请求（通过 `XMLHttpRequest` 或 `Fetch API`）

3. 限制存储访问

	1. **Web Storage（LocalStorage、SessionStorage）**
		不同源的页面无法互相读取或修改存储数据。
	2. **Cookies**
		Cookie 的同源策略基于域名，默认仅允许同源访问。
	3. **IndexedDB**
		不同源的页面无法共享同一数据库。

4. 脚本与资源加载

	1. **JavaScript 文件加载**
		可以加载跨域的 JavaScript 文件（如 CDN 资源），但浏览器会限制对跨域脚本的错误捕获

	2. **图片、CSS、字体等静态资源**
		可以加载跨域资源，但通过 JavaScript 操作资源内容时可能受限

	3. **Web Workers**
		主线程可以加载跨域的 Worker 脚本，但 Worker 内部发起的请求仍受同源策略限制

		

# 10、什么是跨域，遇到跨域问题你是如何解决的？

**跨域问题** 是指浏览器基于 **同源策略（Same-Origin Policy）** 的安全限制，阻止网页从一个源（协议 + 域名 + 端口）向另一个源发起请求或访问资源的行为。如果两个 URL 的协议、域名或端口不同，则属于跨域，浏览器会默认拦截跨域请求，导致开发者无法直接获取跨域资源或数据

常见跨域解决方案有：

1. CORS（跨域资源共享）
    服务器设置响应头 `Access-Control-Allow-Origin`，允许指定源的请求

2. JSONP（仅支持 GET 请求）
    利用 `<script>` 标签不受同源策略限制的特性，通过回调函数获取数据。

  JSONP 的核心思想是通过动态创建 `<script>` 标签，从服务器加载一段 JavaScript 代码，这段代码通常是一个函数调用，函数的参数就是需要获取的数据。

  ```js
  //客户端代码如下
  function handleResponse(data) {
    console.log("Received data:", data);
  }
  
  // 动态创建 <script> 标签
  const script = document.createElement("script");
  script.src = "https://example.com/api/data?callback=handleResponse";
  document.body.appendChild(script);
  
  
  //服务器返回的代码
  handleResponse({ name: "John", age: 30 });
  ```

3. 同源代理服务器(Proxy)
    通过同源服务器转发请求，避免浏览器直接跨域(VPN原理)

4. postMessage API
     适用于需要在不同源的窗口或 iframe 之间传递数据的场景

  **发送消息**：

- 使用 window.postMessage() 方法向目标窗口发送消息。


  **接收消息**：

  - 在目标窗口监听 `message` 事件，通过事件对象获取发送的消息。

```js
// 发送消息
targetWindow.postMessage(message, targetOrigin);

// 接收消息
window.addEventListener("message", (event) => {
  if (event.origin !== expectedOrigin) return; // 验证消息来源
  console.log("Received message:", event.data);
});
```

postMessage 的具体实现

场景：父页面与嵌入的跨域 iframe 通信
	  父页面代码（https://parent.com）

```js
<iframe id="iframe" src="https://child.com"></iframe>
<script>
  const iframe = document.getElementById("iframe");

  // 向 iframe 发送消息
  iframe.onload = () => {
    iframe.contentWindow.postMessage("Hello from parent!", "https://child.com");
  };

  // 接收 iframe 的消息
  window.addEventListener("message", (event) => {
    if (event.origin !== "https://child.com") return; // 验证消息来源
    console.log("Received from iframe:", event.data);
  });
</script>
```

 iframe 页面代码（`https://child.com`）：

```js
<script>
  // 接收父页面的消息
  window.addEventListener("message", (event) => {
    if (event.origin !== "https://parent.com") return; // 验证消息来源
    console.log("Received from parent:", event.data);

    // 向父页面发送消息
    event.source.postMessage("Hello from iframe!", event.origin);
  });
</script>
```



# 11、JavaScript prototype（原型对象）

`prototype`它为对象提供了继承和共享属性的机制。每个 JavaScript 对象都有一个与之关联的原型对象，通过原型对象，可以实现属性和方法的共享，从而减少内存占用。

所有的 JavaScript 对象都会从一个 prototype（原型对象）中继承属性和方法。

构造函数都有一个prototype 属性，它是一个对象，我们称之为 “原型对象”

1. 通过实例对象获取原型对象  `obj.__proto__`

2. 通过构造函数获取原型对象  `Fun.prototype`

## (1)对象的 __proto__ 属性

每个 JavaScript 对象（除了 null）都自动拥有一个隐藏的属性 __proto__，它指向该对象的原型对象。

```js
let obj = {};
console.log(obj.__proto__); // 输出: [object Object], 即 obj 的原型是 Object.prototype
```

当你使用构造函数创建一个对象时，构造函数的 **prototype** 属性会成为所有通过该构造函数创建的实例对象的原型。

```js
function Person(name) {
    this.name = name;
}

Person.prototype.sayHello = function() {
    console.log("Hello, my name is " + this.name);
};

let alice = new Person("Alice");
alice.sayHello(); // 输出: Hello, my name is Alice
```

在这个例子中，Person.prototype 是 alice 对象的原型，因此 alice 可以访问 sayHello 方法。

## (2)原型链

在 JavaScript 中，对象通过原型链（prototype chain）来实现继承。当一个对象尝试访问一个属性或方法时，JavaScript 会首先检查该对象自身是否有这个属性或方法。如果没有，它会沿着原型链向上查找。

```js
let obj = {};
console.log(obj.toString()); // 输出: [object Object]
// 这个 `toString` 方法实际上是从 `Object.prototype` 继承过来的
```

在上面的例子中，obj 对象没有定义 toString 方法，因此 JavaScript 沿着原型链查找，最终在 Object.prototype 中找到该方法。

## (3)修改原型

可以动态地修改对象的原型，这样可以影响到所有基于该原型创建的对象

```js
function Person(name) {
    this.name = name;
}

Person.prototype.sayHello = function() {
    console.log("Hello, my name is " + this.name);
};

let bob = new Person("Bob");
bob.sayHello(); // 输出: Hello, my name is Bob

// 修改原型
Person.prototype.sayGoodbye = function() {
    console.log("Goodbye from " + this.name);
};

bob.sayGoodbye(); // 输出: Goodbye from Bob
```

在这个例子中，我们在 Person.prototype 上添加了一个新的方法 sayGoodbye，bob 对象立即就可以访问到这个新方法。

## (4)Object.create 方法

Object.create 方法允许你创建一个新对象，并将其原型设置为指定的对象。

```js
let personPrototype = {
    sayHello: function() {
        console.log("Hello, my name is " + this.name);
    }
};

let alice = Object.create(personPrototype);
alice.name = "Alice";
alice.sayHello(); // 输出: Hello, my name is Alice
```

在这个例子中，alice 的原型是 personPrototype，因此 alice 对象可以访问 sayHello 方法。

## (5)prototype 继承

JavaScript 对象有一个指向一个原型对象的链。当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾

#  12、什么是重绘，重排(回流)，在开发中应注意什么？

1. **重绘**
	当元素的样式改变（如颜色、背景色、边框颜色等），但不影响其布局时，浏览器会重新绘制元素的外观，这个过程称为重绘。
2. **重排**
	当元素的几何属性（如尺寸、位置、布局）发生变化时，浏览器需要重新计算元素的几何信息，并更新渲染树，这个过程称为重排。

**关系：**1. 重排必然触发重绘，但重绘不一定触发重排。
					 2.页面首次加载时必然触发一次完整的重排（构建渲染树）

 **开发中的注意事项**

1. 尽量**减少重排次数**

	1. **批量修改 DOM元素**

	- 使用 `documentFragment` 或离线 DOM（如 `cloneNode`）进行批量操作，再插入文档。
	- 使用 `display: none` 隐藏元素，修改后再显示（仅触发两次重排）。

	2. **避免频繁读取布局属性**

	- 如 `offsetTop`、`scrollHeight`、`getComputedStyle` 等会强制触发重排以获取最新值。应缓存这些值或集中读写。

	3. **使用 CSS 替代 JavaScript 操作样式**

	- 如用 `class` 批量修改样式，而非逐行修改 `style`

2. **优化 CSS 和布局**

	1. **减少层级复杂度**避免过于复杂的 CSS 选择器，减少布局计算时间。
	2. **使用 CSS3 动画**

	- 优先使用 `transform`、`opacity` 等属性（触发 GPU 加速，避免重排）。
	- 对动画元素使用 `position: absolute/fixed`，使其脱离文档流，减少对其他元素的影响。

3. **其他优化**

	- **防抖/节流**：对 `resize`、`scroll` 等高频事件使用防抖或节流，减少回调触发频率。
	- **避免逐行修改样式**：合并多次样式修改（如使用 `cssText` 或 `classList`）。

# 13、什么是闭包,有哪些应用场景？手写一个闭包？

## (1)是什么

在 JavaScript 中，闭包是能够访问另一个函数作用域中变量的函数。从本质上来说，闭包是函数和声明该函数的词法环境的组合。(嵌套函数能访问在其外部作用域中声明的变量。)

### 闭包的特点：

1. **函数嵌套**：外部函数包含内部函数。
2. **内部函数引用外部变量**：内部函数使用外部函数的变量或参数。
3. **外部函数返回内部函数**：内部函数被外部调用，保持对外部作用域的引用。

---

### 手写闭包示例
```javascript
function createCounter() {
  let count = 0; // 外部函数的变量
  return function() { // 内部函数形成闭包
    count++;
    return count;
  };
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
```
**解释**：  
`createCounter` 返回一个内部函数，该函数引用了外部作用域的 `count` 变量。每次调用 `counter()` 时，`count` 的值被保留，不会被垃圾回收。

---

### 闭包的应用场景
1. **封装私有变量**  
   通过闭包隐藏实现细节，只暴露接口：
   
   ```javascript
   const module = (function() {
     let privateVar = 0;
     const privateMethod = () => privateVar;
     return {
       publicMethod: () => {
         privateVar++;
         return privateMethod();
       }
     };
   })();
   module.publicMethod(); // 1
   
   //==============================================================
           const counter = (function () {
               let privateCount = 0
               function changeBy(val) {
                   privateCount += val
               }
               return {
                   increment() {
                       changeBy(1)
                   },
   
                   decrement() {
                       changeBy(-1)
                   },
                   value() {
                       return privateCount
                   }
               }
           })()
   
           console.log(counter.value()); // 0
   
           counter.increment();
           counter.increment();
           console.log(counter.value()); // 2
           
           counter.decrement();
           console.log(counter.value()); // 1
   ```
   
2. **函数柯里化**  
   将多参数函数转换为单参数链式调用：
   
   ```javascript
   function add(x) {
     return function(y) {
       return x + y;
     };
   }
   const add5 = add(5);
   console.log(add5(3)); // 8
   ```
   
3. **防抖（Debounce）与节流（Throttle）**  
   控制高频事件触发频率：
   
   ```javascript
   // 防抖：连续触发时，只执行最后一次
   function debounce(fn, delay) {
     let timer;
     return function(...args) {
       clearTimeout(timer);
       timer = setTimeout(() => fn.apply(this, args), delay);
     };
   }
   //=================================================
   <body>
       <div style="width: 200px; height: 200px;background-color: #ef4444;"></div>
       <script>
           let div = document.querySelector('div')
           function callBackFun(e) {
               console.log(e.target);
               console.log('我被点击了');
           }
           // 防抖：连续触发某事件 只执行一次
           function _debounce(fn, wait) {
               let timerId = null
               return function (e) {
                   // 此次点击前需先清除上一次的定时器
                   clearTimeout(timerId)
                   // wait 时间后执行 回调函数
                   timerId = setTimeout(() => {
                       // 预期的回调函数需要绑定this指向
                       fn.call(this, e)
                   }, wait);
               }
           }
           // div 被点击后  返回一个 函数 ，在这个函数内部，我们执行我们预期
           // 的回调函数
           div.addEventListener('click', _debounce(callBackFun, 300))
       </script>
   </body>
   ```
   
4. **循环中处理异步事件**  
   解决 `var` 导致的变量共享问题（ES5 方案）：
   ```javascript
   for (var i = 0; i < 5; i++) {
     (function(j) {
       setTimeout(() => console.log(j), 1000);
     })(i);
   }
   ```

5. **缓存计算结果（记忆化）**  
   避免重复计算，提升性能：
   ```javascript
   function memoize(fn) {
     const cache = {};
     return function(arg) {
       return cache[arg] || (cache[arg] = fn(arg));
     };
   }
   ```

---

### 注意事项
- **内存泄漏**：闭包可能导致外部函数变量无法释放，需及时解除引用（如置 `null`）。
- **性能影响**：过度使用闭包可能增加内存消耗。

闭包是 JavaScript 强大特性的体现，合理使用可提升代码的模块化和灵活性。



# 14、事件循环

## **是什么？**

事件循环（Event Loop）是让 JavaScript 实现异步任务之间非阻塞处理的核心机制

## **特性？**

JS是单线程的，为了防止一个函数执行时间过长阻塞后面的代码，所以会先将同步代码压入执行栈中，依次执行，将异步代码推入任务队列。任务队列有分为宏任务队列（Macro Task Queue）和微任务队列（Micro Task Queue）。

### 宏任务

常见的宏任务有 `setTimeout`、`setInterval`、`setImmediate`（Node.js 环境）、`I/O` 操作等。

### 微任务

常见的微任务有 `Promise.then`、`MutationObserver`、`process.nextTick`（Node.js 环境）等。

## 事件循环的工作流程

事件循环的主要工作就是不断地从任务队列中取出任务并放入调用栈中执行。相当于是一个任务的协调器，用于处理宏任务+微任务+渲染任务+动画帧的回调。

1. 调用栈为空时，事件循环会检查微任务队列。如果微任务队列中有任务，就依次将这些任务取出并放入调用栈中执行，直到微任务队列为空。
2. 微任务队列清空后，事件循环会从宏任务队列中取出一个任务放入调用栈中执行。
3. 该宏任务执行过程中如果产生了新的微任务，这些微任务会被添加到微任务队列中。
4. 这个宏任务执行完毕后，事件循环会再次检查微任务队列，重复第一个步骤。

另外，当事件循环执行到渲染阶段时，浏览器会根据当前页面的状态（包括 DOM 树、CSS 样式、JavaScript 操作等）计算出需要绘制的内容，并将其渲染到屏幕上。而`requestAnimationFrame`是一个在浏览器中用于请求动画帧的 API。它允许开发者告诉浏览器在下次重绘之前执行一个特定的函数。当调用`requestAnimationFrame`时，浏览器会将指定的回调函数添加到一个渲染任务的队列中。在事件循环的某个合适的时机（通常是在渲染阶段之前），浏览器会遍历这个队列，并依次执行这些回调函数。

---



1. 浏览器除了宏任务/微任务还有哪些操作(SLP，RAF)
2. 一般是从宏任务开始(<Script>引入)
3. 在第一个宏任务执行后又会产生

### **代码示例**

```javascript
console.log("Start"); // 同步任务

setTimeout(() => console.log("Timeout"), 0); // 宏任务

Promise.resolve()
  .then(() => console.log("Promise 1")) // 微任务
  .then(() => console.log("Promise 2")); // 微任务

console.log("End"); // 同步任务

// 输出顺序：
// Start → End → Promise 1 → Promise 2 → Timeout
```

[面试率 90% 的JS事件循环Event Loop，看这篇就够了！! !_面试率超高的js事件循环,看这篇就够了-CSDN博客](https://blog.csdn.net/m0_46374969/article/details/119969908)

[【前端面试】全网最全javascript Event Loop、任务队列解释_前端任务队列-CSDN博客](https://blog.csdn.net/weixin_43342290/article/details/141216016?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~PayColumn-1-141216016-blog-119969908.235^v43^control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~PayColumn-1-141216016-blog-119969908.235^v43^control)

# 15、网络请求中的常见状态码有哪些，表达了什么

---

### **HTTP 状态码分类**

| 状态码范围 | 类别       | 说明                           |
| ---------- | ---------- | ------------------------------ |
| 1xx        | 信息响应   | 请求已被接收，继续处理         |
| 2xx        | 成功       | 请求已成功被服务器接收并处理   |
| 3xx        | 重定向     | 需要客户端进一步操作以完成请求 |
| 4xx        | 客户端错误 | 请求包含语法错误或无法完成     |
| 5xx        | 服务器错误 | 服务器处理请求时发生错误       |

---

### **常见状态码详解**
#### **1xx 信息响应**
- **100 Continue**  
  客户端应继续发送请求体（用于大文件上传前的预检）。

- **101 Switching Protocols**  
  服务器同意切换协议（如 WebSocket 升级）。

---

#### **2xx 成功**
- **200 OK**  
  请求成功，返回响应数据（最常见状态码）。

- **201 Created**  
  资源创建成功（常见于 POST/PUT 请求）。

- **204 No Content**  
  请求成功，但无返回内容（常见于 DELETE 请求）。

---

#### **3xx 重定向**
- **301 Moved Permanently**  
  资源永久重定向到新 URL（浏览器会缓存新地址）。

- **302 Found**  
  资源临时重定向（浏览器每次都会请求原地址）。

- **304 Not Modified**  
  资源未修改（配合缓存头 `If-Modified-Since` 使用）。

---

#### **4xx 客户端错误**
- **400 Bad Request**  
  请求语法错误（如参数格式错误）。

- **401 Unauthorized**  
  未认证（需提供有效凭证，如登录后重试）。

- **403 Forbidden**  
  服务器拒绝执行（权限不足，如访问受限资源）。

- **404 Not Found**  
  资源不存在（URL 路径错误或资源已删除）。

- **429 Too Many Requests**  
  请求频率过高（API 限流时常见）。

---

#### **5xx 服务器错误**
- **500 Internal Server Error**  
  服务器内部错误（代码异常或配置问题）。

- **502 Bad Gateway**  
  网关错误（反向代理服务器与上游服务通信失败）。

- **503 Service Unavailable**  
  服务不可用（服务器过载或维护中）。

- **504 Gateway Timeout**  
  网关超时（代理服务器未及时收到上游响应）。

---

### **特殊状态码**
- **418 I'm a teapot**（彩蛋）  
  愚人节玩笑状态码，表示服务器拒绝冲泡咖啡（实际开发中不会使用）。

---

### **状态码应用场景**
| 场景                   | 推荐状态码              | 说明                 |
| ---------------------- | ----------------------- | -------------------- |
| 表单提交成功           | 200 OK                  | 返回提交结果         |
| 用户未登录访问私有资源 | 401 Unauthorized        | 提示需要登录         |
| 接口请求频率超限       | 429 Too Many Requests   | 客户端需降低请求频率 |
| CDN节点无法连接源站    | 502 Bad Gateway         | 检查源站服务状态     |
| 服务器维护页面         | 503 Service Unavailable | 显示维护倒计时页面   |

---

### **调试技巧**
1. **快速定位问题**：
   - 4xx 错误 → 检查请求参数、权限、URL
   - 5xx 错误 → 检查服务器日志、资源使用情况

2. **缓存控制**：
   ```http
   Cache-Control: max-age=3600
   If-None-Match: "xyzzy"
   ```
   配合 304 状态码实现高效缓存。

3. **重定向优化**：
   - 永久跳转用 301（SEO 权重转移）
   - 临时跳转用 302 或 307（保留原 URL 权重）

---

理解这些状态码能帮助开发者快速诊断网络问题，优化 API 设计，并提升用户体验。

# 16、JS中for、for … in 、for … of之间的区别

以下是 JavaScript 中 `for`、`for...in`、`for...of` 的对比表格：

| **特性**             | **`for`**                        | **`for...in`**                          | **`for...of`**                     |
| -------------------- | :------------------------------- | --------------------------------------- | ---------------------------------- |
| **用途**             | 通用循环控制                     | 遍历对象的**可枚举属性**                | 遍历**可迭代对象**的值             |
| **适用对象**         | 数组、字符串、自定义逻辑         | 对象（包括数组，但**不推荐**）          | 数组、字符串、Map、Set、NodeList等 |
| **遍历内容**         | 索引（需手动控制）               | 键名（字符串形式）                      | 值（直接获取元素）                 |
| **是否遍历原型属性** | 无关（完全手动控制）             | ✅ 默认遍历原型链属性（需过滤）          | ❌ 只遍历自身元素                   |
| **支持 `break`**     | ✅                                | ✅                                       | ✅                                  |
| **支持 `continue`**  | ✅                                | ✅                                       | ✅                                  |
| **性能**             | 最高（直接索引访问）             | 较低（需检查属性描述符）                | 较高（优化迭代协议）               |
| **稀疏数组处理**     | 可跳过空元素（需手动判断）       | 会跳过空元素                            | 会跳过空元素                       |
| **典型场景**         | 需要精确控制循环次数或索引的场景 | 遍历对象属性（需配合 `hasOwnProperty`） | 遍历数组/集合元素                  |
| **ES版本**           | ES1                              | ES1                                     | ES6                                |

---

### **补充说明**

#### 1. `for` 循环
```javascript
// 经典数组遍历
const arr = [10, 20, 30];
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]); // 输出：10, 20, 30
}
```
- **优势**：完全控制迭代过程，适合需要反向遍历、跳跃访问等复杂场景
- **注意**：循环变量作用域问题（推荐使用 `let` 而非 `var`）

---

#### 2. `for...in` 循环
```javascript
const obj = { a: 1, b: 2 };
for (const key in obj) {
  if (obj.hasOwnProperty(key)) { // 过滤原型属性
    console.log(key); // 输出：a, b
  }
}
```
- **陷阱**：遍历数组时可能得到非数字键（如添加的自定义属性）
- **建议**：不要用于数组遍历，优先用于普通对象

---

#### 3. `for...of` 循环
```javascript
const arr = [10, 20, 30];
for (const value of arr) {
  console.log(value); // 输出：10, 20, 30
}
```
- **优势**：简洁安全，直接获取元素值
- **扩展**：可遍历任何实现了 `[Symbol.iterator]` 接口的对象

---

### **选择指南**
- 需要索引 → `for` 循环
- 遍历对象属性 → `for...in` + `hasOwnProperty`
- 遍历集合值 → `for...of`
- 性能敏感场景 → `for` 循环

实际开发中，`for...of` 在大多数数组/集合遍历场景下是最佳选择。

# 17、JavaScript设计任务队列，控制请求最大并发数

场景: 前端页面中需要同时发送200个请求，但是服务端有限制，
					需要前端控制并发数，保证最多只能同时发送6个请求
		**要求:**    

1. 最多同时执行的任务数为6个
2. 当前任务执行完成后，释放队列空间，自动执行下一个任务
3. 所有任务添加到任务队列后，自动开始执行任务

首先，应该想到用Promise来模拟异步请求任务。然后，需要一个队列来管理这些任务，确保任何时候只有最多6个在执行，其他的在队列里等待。

```js
        // 1、创建200个异步任务
        function generateAsynTask(i) {
            return function () {
                return new Promise((resolve, reject) => {
                    const isSuccess = Math.random() > 0.5
                    setTimeout(() => {
                        isSuccess ? resolve(`第${i}个任务 success!`) : reject(`第${i}个任务 fail!`)
                    }, i * 200);
                })
            }
        }

        // 2、通过一个任务队列的类用来管理 异步任务
        class TaskQueue {
            #maxConcurrent; // 最大并发数（固定值）
            #taskQueue = []; // 任务队列
            #activeCount = 0; // 运行中的任务数
	
            // 默认最大并发数 6
            constructor(maxConcurrent = 6) {
                this.#maxConcurrent = maxConcurrent;
            }
            // 添加异步任务到任务队列
            addTask(task) {
                this.#taskQueue.push(task);
                this.#schedule(); // 添加任务时触发调度
            }
		
            #schedule() {
                //活跃任务数 < 最大并发数
                //任务队列不为空
                while (
                    this.#activeCount < this.#maxConcurrent &&
                    this.#taskQueue.length > 0
                ) {
                    // 从任务队列中取出任务执行
                    const task = this.#taskQueue.shift();
                    this.#activeCount++;
                    task()
                        .then(res => console.log(res))
                        .catch(err => console.error(err))
                        .finally(() => {
                            this.#activeCount--;
                            this.#schedule(); // 任务完成时继续调度
                        });
                }
            }
        }

        const taskQueue = new TaskQueue(6);
        for (let i = 0; i < 200; i++) {
            taskQueue.addTask(generateAsynTask(i))
        }
```

# 18、手写一个具有迭代器属性的对象

ES6 规定，默认的 Iterator 接口部署在数据结构的`Symbol.iterator`属性，或者说，一个数据结构只要具有Symbol.iterator属性，就可以认为是“可遍历的”（iterable）。Symbol.iterator属性本身是一个函数，就是当前数据结构默认的遍历器生成函数。执行这个函数，就会返回一个遍历器。至于属性名Symbol.iterator，它是一个表达式，返回Symbol对象的iterator属性，这是一个预定义好的、类型为 Symbol 的特殊值，所以要放在方括号内。

更多内容请参考    [Iterator 和 for...of 循环 - ECMAScript 6入门](https://es6.ruanyifeng.com/#docs/iterator#默认-Iterator-接口)

```js
        let obj = {
            name: '张安',
            age: 45,
            school: 'WUIT',
            slogan: '加油加油',
            arr: [1, 2, 3, 4, 5],
            key: ['slogan', 'arr', 'name', 'age', 'school'], // 修正了 key 的定义
            [Symbol.iterator]() {
                let count = -1; // 初始化为 -1 以便从第一个元素开始迭代
                let keys = this.key; // 获取 key 数组的引用
                return {
                    next() {
                        count++;
                        return {
                            // 使用 obj[keys[count]] 访问对象属性
                            value: obj[keys[count]], 
                            // 设置 done 属性为 count 达到 key 数组长度时为 true
                            done: count >= keys.length 
                        };
                    }
                };
            }
        };

        for (const ele of obj) {
            console.log(ele);
        }
```

上面代码中，对象`obj`是可遍历的（iterable），因为具有`Symbol.iterator`属性。执行这个属性，会返回一个遍历器对象。该对象的根本特征就是具有`next`方法。每次调用`next`方法，都会返回一个代表当前成员的信息对象，具有`value`和`done`两个属性。

原生具备 Iterator 接口的数据结构如下。

- Array
- Map
- Set
- String
- TypedArray
- 函数的 arguments 对象
- NodeList 对象

# 19、JS数据类型以及区别

基本数据类型：字符串（String）、数字(Number)、布尔(Boolean)、空（Null）、未定义（Undefined）、Symbol。

引用数据类型：对象(Object)、数组(Array)、函数(Function)，还有两个特殊的对象：正则（RegExp）和日期（Date）

基本数据类型是按值访问的，值存储在栈内存中，访问时访问的是值。

引用数据类型是按地址访问的，值存储在堆内存中，栈中存储的是值的引用地址。

# 20、浏览器跨页面数据通信的方法有哪些？

### 1、localStorage` 和 `storage 事件

`localStorage` 是浏览器提供的持久化存储机制，同一域名下的所有页面共享同一个 `localStorage` 对象。当 `localStorage` 中的数据发生变化时，会触发 `storage` 事件，其他页面可以通过监听该事件来获取更新的数据。

```js
// 页面A
localStorage.setItem('key', 'value');
// 页面B
window.addEventListener('storage', (event) => {
    if (event.key === 'key') {
        console.log('New value:', event.newValue);
    }
});
```

### 2、`BroadcastChannel` API

`BroadcastChannel` 是 HTML5 提供的一种跨页面通信机制，允许同一域名下的不同页面通过同一个频道进行消息传递

```js
// 页面A
const channel = new BroadcastChannel('my_channel');
channel.postMessage('Hello from Page A');

// 页面B
const channel = new BroadcastChannel('my_channel');
channel.onmessage = (event) => {
    console.log('Received:', event.data);
};
```

### 3、`window.postMessage`

`window.postMessage` 方法允许在不同窗口或 iframe 之间传递消息。通常用于跨域通信。

```js
// 页面A
const targetWindow = window.open('pageB.html');
targetWindow.postMessage('Hello from Page A', '*');

// 页面B
window.addEventListener('message', (event) => {
    console.log('Received:', event.data);
});
```

4、`SharedWorker`

`SharedWorker` 是一种可以在多个页面之间共享的 Web Worker。它允许不同的页面通过共享的 Worker 进行通信。

```js
// 页面A
const worker = new SharedWorker('worker.js');
worker.port.postMessage('Hello from Page A');

// 页面B
const worker = new SharedWorker('worker.js');
worker.port.onmessage = (event) => {
    console.log('Received:', event.data);
};
```



# 21、简单介绍一下Promise常见的静态方法和实例方法

## 1、静态方法

(1)Promise.resolve()

**`Promise.resolve()`** 静态方法传入一个要被该Promise对象解决的参数，返回由给定参数解决的Promise。如果传入的值是一个Promise对象，则返回该对象。

(2)Promise.reject()

**`Promise.reject()`** 静态方法返回一个已拒绝（rejected）的 `Promise` 对象，拒绝原因为给定的参数。

(3)Promise.all()

**`Promise.all()`** 静态方法接受一个 Promise 可迭代对象作为输入，并返回一个 [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)。当所有输入的 Promise 都被兑现时，返回的 Promise 也将被兑现（即使传入的是一个空的可迭代对象），并返回一个包含所有兑现值的数组。如果输入的任何 Promise 被拒绝，则返回的 Promise 将被拒绝，并带有第一个被拒绝的原因。

(4)Promise.race()

**`Promise.race()`** 静态方法接受一个 promise 可迭代对象作为输入，并返回一个 [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)。这个返回的 promise 会随着第一个 promise 的敲定而敲定。

## 2、实例方法

(1)Promise.prototype.catch()

[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 实例的 **`catch()`** 方法用于注册一个在 promise 被拒绝时调用的函数。

(2)Promise.prototype.finally()

[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 实例的 **`finally()`** 方法用于注册一个在 promise 敲定（兑现或拒绝）时调用的函数。

(3)Promise.prototype.then()

[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 实例的 **`then()`** 方法最多接受两个参数：用于 `Promise` 兑现和拒绝情况的回调函数。它立即返回一个等效的 [`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise) 对象，允许你链接到其他 Promise 方法，从而实现[链式调用](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Using_promises#链式调用)。

# 22、echarts常见的图形有哪些

柱状图、折线图、饼图、散点图、地理坐标图、K线图、雷达图、关系图、热力图等

# 23、echarts如何设置渐变色

- 使用 RGB 表示颜色，比如 `'rgb(128, 128, 128)'`，如果想要加上 alpha 通道表示不透明度，可以使用 RGBA，比如 `'rgba(128, 128, 128, 0.5)'`，也可以使用十六进制格式，比如 `'#ccc'`。

- 渐变色或者纹理填充

	```ts
	// 线性渐变，前四个参数分别是 x0, y0, x2, y2, 范围从 0 - 1，相当于在图形包围盒中的百分比，如果 globalCoord 为 `true`，则该四个值是绝对的像素位置
	{
	  type: 'linear',
	  x: 0,
	  y: 0,
	  x2: 0,
	  y2: 1,
	  colorStops: [{
	      offset: 0, color: 'red' // 0% 处的颜色
	  }, {
	      offset: 1, color: 'blue' // 100% 处的颜色
	  }],
	  global: false // 缺省为 false
	}
	// 径向渐变，前三个参数分别是圆心 x, y 和半径，取值同线性渐变
	{
	  type: 'radial',
	  x: 0.5,
	  y: 0.5,
	  r: 0.5,
	  colorStops: [{
	      offset: 0, color: 'red' // 0% 处的颜色
	  }, {
	      offset: 1, color: 'blue' // 100% 处的颜色
	  }],
	  global: false // 缺省为 false
	}
	// 纹理填充
	{
	  image: imageDom, // 支持为 HTMLImageElement, HTMLCanvasElement，不支持路径字符串
	  repeat: 'repeat' // 是否平铺，可以是 'repeat-x', 'repeat-y', 'no-repeat'
	}
	```

# 24、echarts如何动态更新一个图表

### 1. 动态更新数据

当需要更新图表的数据时，可使用 `setOption` 方法。该方法能够更新 ECharts 实例的配置项，从而实现数据的动态更新。

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ECharts 动态更新</title>
    <!-- 引入 ECharts 文件 -->
    <script src="https://cdn.jsdelivr.net/npm/echarts@5.4.2/dist/echarts.min.js"></script>
</head>

<body>
    <!-- 为 ECharts 准备一个具备大小（宽高）的 DOM -->
    <div id="main" style="width: 600px;height:400px;"></div>
    <button id="updateButton">更新数据</button>
    <script>
        // 基于准备好的dom，初始化echarts实例
        var myChart = echarts.init(document.getElementById('main'));

        // 指定图表的配置项和数据
        var option = {
            xAxis: {
                type: 'category',
                data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
            },
            yAxis: {
                type: 'value'
            },
            series: [{
                data: [820, 932, 901, 934, 1290, 1330, 1320],
                type: 'line'
            }]
        };

        // 使用刚指定的配置项和数据显示图表。
        myChart.setOption(option);

        // 为按钮添加点击事件监听器
        document.getElementById('updateButton').addEventListener('click', function () {
            // 生成新的数据
            var newData = [1000, 1100, 1200, 1300, 1400, 1500, 1600];
            // 更新配置项中的数据
            option.series[0].data = newData;
            // 使用 setOption 方法更新图表
            myChart.setOption(option);
        });
    </script>
</body>

</html>    
```

### 2. 动态更新配置项

除了更新数据，还能动态更新其他配置项，例如修改图表的标题、颜色等。

```js
// 假设已经有了一个 ECharts 实例 myChart 和配置项 option
// 更新图表的标题
option.title = {
    text: '新的图表标题'
};
// 更新系列的颜色
option.series[0].itemStyle = {
    color: 'red'
};
// 使用 setOption 方法更新图表
myChart.setOption(option);
```

### 3. 定时动态更新

若要实现定时动态更新，可使用 `setInterval` 函数。

```js
// 假设已经有了一个 ECharts 实例 myChart 和配置项 option
setInterval(function () {
    // 生成新的数据
    var newData = [];
    for (var i = 0; i < 7; i++) {
        newData.push(Math.random() * 1000);
    }
    // 更新配置项中的数据
    option.series[0].data = newData;
    // 使用 setOption 方法更新图表
    myChart.setOption(option);
}, 2000); // 每 2 秒更新一次
```



# 25、如何准确的判断数据类型

### 1. `typeof` 操作符

`typeof` 操作符能返回一个表示数据类型的字符串，不过它只能判断基本数据类型，对于对象、数组、`null` 等，返回值都是 `object`，不够精确。

### 2. `instanceof` 操作符

`instanceof` 操作符用于判断对象是否是某个构造函数的实例，常用于判断自定义对象类型，但它不能判断基本数据类型，并且在不同的窗口或 iframe 中使用可能会有问题。

### 3. `Object.prototype.toString.call()` 方法

`Object.prototype.toString.call()` 方法是一种比较准确的判断数据类型的方法，它可以返回一个包含数据类型信息的字符串。

### 4. `Array.isArray()` 方法

`Array.isArray()` 方法专门用于判断一个值是否为数组，它比 `typeof` 和 `instanceof` 更可靠。



# 26、你所了解的ES6的新特性有哪些？

### 1. **块级作用域与 `let` / `const`**

- **`let`**：声明块级作用域的变量，解决了 `var` 的变量提升问题。
- **`const`**：声明常量，不可重新赋值（但对象属性可以修改）。

------

### 2. **箭头函数（Arrow Functions）**

- 简化函数语法，自动绑定 `this`，适合回调函数和匿名函数。

javascript

```javascript
const add = (a, b) => a + b;
```

------

### 3. **模板字符串（Template Literals）**

- 支持多行字符串和嵌入表达式。

javascript

```javascript
const name = 'World';
console.log(`Hello, ${name}!`);
```

------

### 4. **解构赋值（Destructuring Assignment）**

- 从数组或对象中提取值并赋值给变量。

javascript

```javascript
const [a, b] = [1, 2];
const { name, age } = { name: 'Alice', age: 25 };
```

###  5.**默认参数（Default Parameters）**

- 为函数参数提供默认值。

javascript

```javascript
function greet(name = 'Guest') {
  console.log(`Hello, ${name}!`);
}
```

------

### 6. **扩展运算符与剩余参数（Spread / Rest Operator）**

- 

	扩展运算符
	
	：展开数组或对象。
	
	javascript
	
	```javascript
	const arr = [1, 2, 3];
	const newArr = [...arr, 4];
	```

- 

	剩余参数
	
	：将多个参数合并为数组。
	
	javascript
	
	```javascript
	function sum(...numbers) {
	  return numbers.reduce((acc, num) => acc + num, 0);
	}
	```

------

### 7. **对象字面量增强**

- 简写属性和方法。

javascript

```javascript
const name = 'Alice';
const obj = { name, greet() { console.log(`Hello, ${this.name}!`); } };
```

------

### 8. **类（Class）**

- 提供更清晰的面向对象编程语法。

javascript

```javascript
class Person {
  constructor(name) { this.name = name; }
  greet() { console.log(`Hello, ${this.name}!`); }
}
```

------

### 9. **模块化（Modules）**

- 使用 `import` 和 `export` 实现模块化开发。

javascript

```javascript
// math.js
export const add = (a, b) => a + b;

// main.js
import { add } from './math.js';
```

------

### 10. **Promise**

- 提供更优雅的异步编程方式，解决回调地狱问题。

javascript

```javascript
const fetchData = () => new Promise((resolve, reject) => {
  setTimeout(() => resolve('Data fetched!'), 1000);
});
```

------

### 11.**新的数据结构**

**`Set`**：存储唯一值的集合。

```js
const set = new Set([1, 2, 3]);
```

**`Map`**：键值对集合，键可以是任意类型。

```js
const map = new Map();
map.set('key', 'value');
```

### 12.*async / await*

- 提供更直观的异步编程方式。

```js
async function fetchData() {
  const data = await fetch('https://api.example.com/data');
  return data.json();
}
```







# 27、`var`定义的变量在window上，那么`let`定义的变量在哪里呢？

在 JavaScript 中，使用 `let` 定义的变量存储在**当前执行上下文的词法环境（Lexical Environment）**中，而不是全局对象（如 `window`）上。具体来说：

1. **作用域差异**：

  - `var` 在全局作用域中声明变量时，变量会作为全局对象（如 `window`）的属性。
  - `let` 声明的变量存在于**块级作用域**或全局词法环境中，但不会绑定到全局对象。

2. **环境记录类型**：

  - 全局作用域中，`var` 变量通过**对象式环境记录（Object Environment Record）**关联到 `window`。
  - `let` 变量通过**声明式环境记录（Declarative Environment Record）**存储，独立于全局对象，因此无法通过 `window.变量名` 访问。

3. **示例验证**：

  ```javascript
var a = 1;
let b = 2;
console.log(window.a); // 1（var 变量绑定到 window）
console.log(window.b); // undefined（let 变量不在 window 上）
  ```

4. **设计目的**：

  - `let` 的块级作用域和隔离存储机制避免了全局污染，解决了 `var` 的变量提升和重复声明问题。

**结论**：`let` 定义的变量存储在声明时的词法环境中（如全局或块级作用域），与 `window` 解耦，确保更严格的变量作用域控制。



# 28、Object.assign和 … 扩展运算符什么情况下是深拷贝？什么情况下是浅拷贝？

Object.assign和 … 扩展运算符都是用于浅拷贝。也就是仅拷贝对象的第一层属性，当属性值为基本数据类型则直接复制值，若是引用数据类型，则赋值对该属性的引用。也就是说如果对象中的属性值均为基本数据类型那么可以看做是深拷贝。

![](./assets/image-20250312142054158.png)

# 29、如何判断一个函数是普通函数还是异步函数

1. 通过 Object.prototype.toString 检测类型
    异步函数的类型标记为 AsyncFunction，而普通函数标记为 Function
2. 检查函数的构造函数名称
    异步函数的构造函数名称为 AsyncFunction，普通函数为 Function
3. 通过 async 关键字语法特征
    异步函数在定义时使用 async 关键字，可以通过检查函数定义时的特征
4. 根据返回值类型判断
    异步函数总是返回 Promise 对象，可以通过执行函数后检查返回值

```js
      const fn1 = async function () {};
      console.log(Object.prototype.toString.call(fn1));
      console.log('🚀 ~ index01.html:12 ~ fn1:', fn1.__proto__);
      const result = fn1();
      console.log("🚀 ~ index01.html:14 ~ result:", result)
```

# 30、从浏览器输入url到页面返回响应的过程具体发生了什么

**总的过程如下：**

**DNS 解析 ->TCP 连接 -> 发送 HTTP 请求 ->服务器处理请求并返回 HTTP 报文 -> 浏览器解析渲染页面 -> 连接结束**。

一、DNS域名解析

输入URL后，首先需要找到这个URL域名的服务器IP,为了寻找这个IP，浏览器首先会寻找缓存，查看缓存中是否有记录，缓存的查找记录为：浏览器缓存->系统缓存->路由器缓存，缓存中没有则查找系统的 hosts 文件中是否有记录，如果没有则查询 DNS 服务器

二、建立TCP连接

根据 IP 地址，客户端与服务端进行三次握手，建立连接。

- 客户端首先给服务端发送一个带SYN标志的数据包。
- 服务端收到后，回传一个带有SYN/ACK标志的数据包以表示正确传达，并确认信息。
- 最后，客户端再回传一个带ACK标志的数据包，代表“握手”结束。

三、传输数据

连接后，客户端向服务端发起 HTTP 请求，服务器接收到请求后，返回请求静态资源，并同时调用服务器请求接口数据。

四、关闭TCP连接

数据传输完成，客户端与服务端进行四次挥手，关闭连接。

- **第一次挥手**：主动关闭方发送一个FIN，用来关闭主动关闭方到被动关闭方的数据传送，也就是主动关闭方告诉被动关闭方，主动关闭方已经不会再给被动关闭方发送数据了（当然，在FIN包之前发送出去的数据，如果没有收到对应的ACK确认报文，主动关闭方依然会重发这些数据），但是，此时主动关闭方还可以接收数据。
- **第二次挥手**：被动关闭方收到FIN包后，给对方发送一个ACK，确认序号为收到序号+1（与SYN相同，一个FIN占用一个序号）。
- **第三次挥手**：被动关闭方发送一个FIN，用来关闭被动关闭方到主动关闭方的数据传送，也就是告诉主动关闭方，被动关闭方的数据也发送完了，不会再给主动关闭方发送数据了。
- **第四次挥手**：主动关闭方收到FIN后，给被动关闭方发送一个ACK，确认序号为收到序号+1，至此，完成四次握手。

五、渲染页面

对于浏览器根据服务端返回的静态资源，浏览器使用 Native GUI 引擎渲染 HTML 和 CSS ；使用 JS 引擎加载 JS 。

- 将 HTML 节点解析成 DOM 树结构
- 将 CSS 解析成 CSSOM 规则树
- 将 DOM 与 CSSOM 组合成 Render-tree（渲染树）
- 布局：计算出每个节点在屏幕中的位置
- 绘制：即遍历render树，并使用UI后端层绘制每个节点

六、加载 JavaScript 脚本

虽然 HTML\CSS 与 JS 是通过不同的引擎加载，但是却是互斥的，即加载 HTML\CSS 时，JS 会停止加载，相反亦然，这是因为 JS 引擎可以操作 DOM，改变样式、内容等。所以当执行了 JS 之后，渲染树要重新构建。





# 31、谈一谈Vue3中组件的生命周期(钩子函数)

生命周期/钩子函数(其实就是组件渲染时的一些回调函数)

常见的主要有以下8种：	

1. **onBeforeMount**
2. **onMounted**
3. **onBeforeUpdate**
4. **onUpdated**
5. **onBeforeUnmount**
6. **onUnmounted**
7. **onActivated**
8. **onDeactivated**

###  (1)onBeforeMount

注册一个钩子(其实就是一个回调函数)，在组件被挂载之前被调用

1. 先执行 setup  再执行 onBeforeMount

**使用阶段：**这个阶段一般用于初始化数据，自发的页面请求、定时器；二者触发时机差不多，但是setup执行更早一些，所以一般优先使用setup 因为不用导入；另外在此阶段获取不到DOM节点。

 **使用场景：**不需要获取模板节点需要尽早执行。

**注意：** 能够使用setup绝不使用onBeforeMount

```js
onBeforeMount(() => {

 const h1 = document.querySelector('#h1');

 console.log('🚀 ~ leftTimes.vue:23 ~ onBeforeMount ~ h1:', h1);

});
```

###  (2) onMounted

注册一个回调函数，在组件挂载完成后执行。

**使用时机：**onMounted模版已经渲染完成了，此时可以获取DOM节点也是==最早==可以获取到Dom节点的位置

**使用场景：**需要DOM节点的参与时

例如：

1. echarts  初始化

2. betterscroll 初始化

```js
onMounted(() => {
  // echarts  初始化
  // betterscroll 初始化
  // 基于准备好的dom，初始化echarts实例
  var myChart = echarts.init(document.getElementById('main'));
  
  // 绘制图表
  myChart.setOption({
    title: {
      text: 'ECharts 入门示例',
    },
    tooltip: {},
    xAxis: {
      data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子'],
    },
    yAxis: {},
    series: [
      {
        name: '销量',
        type: 'bar',
        data: [5, 20, 36, 10, 10, 20],
      },
    ],
  });
});
```



###  (3) onBeforeUpdate

注册一个钩子，在组件即将因为响应式状态变更而更新其 DOM 树之前调用。

 当前响应式数据变化 导致DOM 树更新的这个操作之前 执行

也就是说即便声明的响应式数据发生了变化但页面并未引用该响应式数据，那么onBeforeUpdate是不会触发的

###   (4)onUpdated

注册一个回调函数，在组件因为响应式状态变更而更新其 DOM 树之后调用。

###   (5)onBeforeUnmount

注册一个钩子，在组件实例被卸载之前调用。

当这个钩子被调用时，组件实例依然还保有全部的功能。所以可能会造成内存泄漏的问题，比如通过路由跳转到另外一个组件，当前组件的JS相关功能(例如`定时器`)并不会销毁。

###  (6) onUnmounted

注册一个回调函数，在组件实例被卸载之后调用。

一个组件在以下情况下被视为已卸载：

- 其所有子组件都已经被卸载。
- 所有相关的响应式作用 (渲染作用以及 `setup()` 时创建的计算属性和侦听器) 都已经停止。

可以在这个钩子中手动清理一些副作用，例如计时器、DOM 事件监听器或者与服务器的连接。

###  (7)onActivated

注册一个回调函数，若组件实例是`<KeepAlive>`缓存树的一部分，当组件被插入到 DOM 中时调用。

###   (8)onDeactivated

注册一个回调函数，若组件实例是 `<KeepAlive>` 缓存树的一部分，当组件从 DOM 中被移除时调用。



# 32、Vue3中的虚拟DOM(vNode)是什么？

在 Vue 3 中，**虚拟 DOM（Virtual DOM）** 是一个轻量级的 JavaScript 对象，它是对真实 DOM 的抽象表示。Vue 使用虚拟 DOM 来高效地更新和渲染页面。

一个典型的 vNode 对象可能包含以下属性：

```ts
{
  tag: 'div',              // 标签名
  props: {                 // 属性
    class: 'container',
    onClick: () => {}
  },
  children: [              // 子节点
    { tag: 'p', text: 'Hello World' },
    { tag: 'button', text: 'Click Me' }
  ],
  key: 'uniqueKey',        // 唯一标识（用于优化 Diff 算法）
  el: null                 // 对应的真实 DOM 节点
}
```



# 33、面试官：什么是防抖和节流？有什么区别？如何实现？

## 是什么？

本质上是优化高频率执行代码的一种手段

## 区别？

- 节流: n 秒内只运行一次，若在 n 秒内重复触发，只有一次生效
- 防抖: n 秒后在执行该事件，若在 n 秒内被重复触发，则重新计时

## 代码实现

### 节流

完成节流可以使用时间戳与定时器的写法

```js
		// 节流： n秒内重复触发，只执行一次 
        //  n秒内只运行一次，若在 n秒内被重复触发，只有一次生效
        function _thtottle(fn, wait) {
            let timerId = null
            //   startTime 记录函数第一次调用时的时间戳。
            let startTime = Date.now()
            console.log('函数第一次调用时的时间戳startTime:', startTime);

            //  函数返回一个内部函数，这个内部函数会在事件触发时被调用。
            return function (e) {
                //curTime 记录当前调用的时间戳
                let curTime = Date.now()

                console.log('curTime:', curTime);

                //计算从上一次调用到现在还剩余多少时间可以触发回调函数。
                let remainTime = wait - (curTime - startTime)

                // _this 保存当前的 this 指向，用于在执行回调函数时保持正确的上下文。
                let _this = this
                // args 保存当前调用时传递的所有参数
                let args = arguments
                // 先清除上一次的定时器
                clearTimeout(timerId)

                //等待时间已经过去或者刚到，此时执行回调函数 fn，
                // 并更新 startTime 为当前时间，以开始新的等待周期。
                if (remainTime <= 0) {
                    fn.apply(_this, args)
                    startTime = Date.now()
                    console.log('reseted startTime:', startTime);
                } else {
                    // 说明等待时间还未到，此时设置一个新的定时器，在剩余时间后执行回调函数 fn
                    timerId = setTimeout(fn, remainTime)
                }
            }
        }
```

### 防抖

```js
  // n 秒后再执行某事件，若在 n 秒内被重复触发，则重新计时
        function _debounce(fn, delay) {
            let timerId = null
            return function (...args) {
                // 重复点击先清除上一次的定时器
                clearTimeout(timerId)
                timerId = setTimeout(() => {
                    // 绑定 this 指向 (事件调用者) && 传入回调函数的参数
                    fn.apply(this, args)
                }, delay)
            }
        }
```



# 34、谈谈JavaScript原型，原型链 ?

### **原型（`prototype`）**

每个 JavaScript 函数（构造函数）都有一个 `prototype` 属性，它是一个对象，称为原型对象。原型对象用于存储共享的属性和方法。

当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾

### 原型链(`prototype chain`)

原型对象也可能拥有原型，并从中继承方法和属性，一层一层、以此类推。这种关系常被称为原型链 (prototype chain)，它解释了为何一个对象会拥有定义在其他对象中的属性和方法

# 35、谈谈JS的垃圾回收机制

JS的垃圾回收机制本质上是一种内存自动管理机制，它的主要任务是识别并释放那些在程序中不在使用的对象所占用的那部分内存，避免内存泄漏。同时，由于开发者无需手动管理内存，也减少了出现错误的概率。

垃圾回收机制有两种方式，一种是引用法，一种是标记法。

**引用法**

判断一个对象的引用数，引用数为0就回收，引用数大于0就不回收。

**标记法**

从根对象window（如全局对象、当前执行上下文等）开始，递归遍历所有可访问的对象，并标记为“存活”。那么没有被遍历到节点，会被认为没有被任何地方引用，就可以证明这是一个需要被释放内存的对象，可以被垃圾回收器回收。

# 36、事件冒泡、捕获(委托)

- **事件冒泡**指在在一个对象上触发某类事件，如果此对象绑定了事件，就会触发事件，如果没有，就会向这个对象的父级对象传播，最终父级对象触发了事件。
- **事件委托**本质上是利用了浏览器事件冒泡的机制。因为事件在冒泡过程中会上传到父节点，并且父节点可以通过事件对象获取到目标节点，因此可以把子节点的监听函数定义在父节点上，由父节点的监听函数统一处理多个子元素的事件，这种方式称为**事件代理**。

`event.stopPropagation()`  //阻止事件冒泡。



# 37、谈谈你对HTTP缓存的理解

HTTP 缓存是一种优化 Web 性能的技术，它通过存储资源的副本（如 HTML、CSS、JavaScript、图片等），在后续请求中直接使用缓存内容，减少网络请求和服务器负载。

HTTP缓存分为：强制缓存和协商缓存

强缓存通过响应头中的 `Cache-Control` 和 `Expires` 字段实现，浏览器直接从本地缓存中读取资源，不会向服务器发送请求。

协商缓存通过响应头中的 `ETag` 和 `Last-Modified` 字段实现，浏览器需要向服务器验证缓存是否有效。

**作用：**

- **提高性能**：减少重复资源的下载，加快页面加载速度。
- **降低服务器负载**：减少对服务器的请求次数，节省带宽和计算资源。
- **改善用户体验**：更快的页面加载和响应速度。

# 38、Vue的组件data为什么必须是一个函数?

new Vue是一个单例模式，不会有任何的合并操作，所以根实例不必校验data一定是一个函数。 

组件的data必须是一个函数，是为了防止两个组件的数据产生污染。 如果都是对象的话，指向同一个地址。 而如果是函数返回的对象，会产生两个空间对应的内存地址。



# 39、父子组件生命周期顺序？

子组件的生命周期嵌套在父组件的onbeforeMount和mounted之间

# 40、Vue3中如何监听子组件的生命周期？

### 1. **使用 `@vue:mounted` 监听子组件生命周期**

Vue 提供了一种简单的方式，通过 `@vue:mounted` 监听子组件的生命周期事件。这种方式适用于直接在模板中监听子组件的生命周期

```vue
<template>
  <div>
	<h1>父组件</h1>
    <Child @vue:mounted="onChildMounted"></Child>
  </div>
</template>

<script setup>
import Child from './components/Child.vue';
const onChildMounted = () => {
  console.log('子组件已经挂载');
};
</script>
```

```vue
<template>
  <div>子组件</div>
</template>
<script setup>
import { onMounted } from 'vue';
onMounted(() => {
  console.log('子组件的 onMounted 钩子被触发 ');
});
</script>

```



#### 输出结果：

- 子组件的 `mounted` 钩子被触发时，父组件的 `onChildMounted` 方法也会被调用。

- 子组件的 onMounted 钩子被触发 ;			子组件已经挂载

​	

### 2. **通过 `ref` 获取子组件实例并监听生命周期**

通过 `ref` 获取子组件的实例，然后手动监听子组件的生命周期方法。

### 3. **通过 `emit` 自定义事件监听子组件生命周期**

子组件在其生命周期钩子中通过 `emit` 触发自定义事件，父组件监听这些事件

### 4. **使用 `watchEffect` 或 `watch` 监听子组件的状态变化**

如果子组件的生命周期事件与某些状态变化相关，可以通过 `ref` 获取子组件的实例，并使用 `watchEffect` 或 `watch` 监听子组件的状态变化。

# 41、VUE3中 v-show和v-if的相同点和区别是什么？

相同点：`v-show` 和 `v-if` 都是用于条件性地渲染元素的指令，根据表达式的值来控制元素的显示和隐藏

不同点：

1. v-show 只是简单的控制元素的 display 属性，而 v-if 才是条件渲染（条件为真，元素将会被渲染，条件为假，元素会被销毁）；
2. v-show 有更高的首次渲染开销，而 v-if 的首次渲染开销要小的多；
3. v-if 有更高的切换开销，v-show 切换开销小；
4. v-if 有配套的 v-else-if 和 v-else，而 v-show 没有
5. v-if 可以搭配 template 使用，而 v-show 不行

# 42、在Vue3中，v-for和v-if哪个优先级更高？

在 Vue 3 中，当它们同时存在于一个节点上时，`v-if` 比 `v-for` 的优先级更高。这意味着 `v-if` 的条件将无法访问到 `v-for` 作用域内定义的变量别名。

```vue
<!--
 这会抛出一个错误，因为属性 todo 此时
 没有在该实例上定义
-->
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo.name }}
</li>
```

在外先包装一层 `<template>` 再在其上使用 `v-for` 可以解决这个问题 (这也更加明显易读)：

```vue
<template v-for="todo in todos">
  <li v-if="!todo.isComplete">
    {{ todo.name }}
  </li>
</template>
```

# 43、Vue3中v-for和v-if为何不推荐同时使用？

(1)优先级问题

在 Vue 3 中，`v-if` 比 `v-for` 的优先级更高。这意味着 `v-if` 的条件将无法访问到 `v-for` 作用域内定义的变量别名。

(2)可读性问题

将 `v-for` 和 `v-if` 放在同一个元素上会使模板变得复杂，降低代码的可读性和维护性。

(3)潜在的逻辑错误

由于 `v-for` 和 `v-if` 同时作用于同一元素，可能会导致逻辑上的混淆。

# 44、VUE3中组件之间的传值方式有哪些？

### (1)父组件向子组件传值（Props）

父组件能通过 `props` 把数据传递给子组件。子组件需要在 `defineProps` 里声明要接收的 `props`。

需要注意的是，这是一个单向传递的过程，子组件中不能修改父组件传递过来的数据。

### (2)子组件向父组件传值（自定义事件）

子组件可以通过触发自定义事件向父组件传递数据。在子组件中使用 `defineEmits` 定义事件，然后通过 `$emit` 触发事件。

```vue
<!-- 子组件 -->
<template>
  <button @click="sendMessage">发送消息给父组件</button>
</template>
<script setup>
import { defineEmits } from 'vue';

const emits = defineEmits(['childEvent']);

const sendMessage = () => {
  emits('childEvent', '来自子组件的消息');
};
</script>

//---------------------------------------------
<!-- 父组件 -->
<template>
  <ChildComponent @childEvent="handleChildEvent" />
  <p>{{ receivedMessage }}</p>
</template>

<script setup>
import { ref } from 'vue';
import ChildComponent from './ChildComponent.vue';

const receivedMessage = ref('');

const handleChildEvent = (message) => {
  receivedMessage.value = message;
};
</script>
```



### (3)跨层级组件传值（Provide/Inject）

`Provide/Inject` 适用于跨层级组件间传值，父组件通过 `provide` 提供数据，后代组件使用 `inject` 注入数据。

```js
<!-- 祖先组件 -->
<template>
  <ChildComponent />
</template>

<script setup>
import { provide, ref } from 'vue';
import ChildComponent from './ChildComponent.vue';

const sharedData = ref('共享数据');
provide('sharedData', sharedData);
</script>

<!-- 后代组件 -->
<template>
  <p>{{ injectedData }}</p>
</template>

<script setup>
import { inject } from 'vue';

const injectedData = inject('sharedData');
</script>
```

### (4)`v-model` 参数

在父组件上的 `v-model` 接受一个参数：

```vue
<MyComponent v-model:title="bookTitle" />
```

在子组件中，我们可以通过将字符串作为第一个参数传递给 `defineModel()` 来支持相应的参数：

```vue
<!-- MyComponent.vue -->
<script setup>
const title = defineModel('title')
</script>

<template>
  <input type="text" v-model="title" />
</template>
```

如果需要额外的 prop 选项，应该在 model 名称之后传递：

```js
const title = defineModel('title', { required: true })
```

# 45、讲一下Vue3中的computed和watch的区别是什么？

1. 在 Vue 3 里，`computed` 和 `watch` 都是用于响应式数据处理的
2. **computed**
	它适用于基于已有响应式数据计算出新值的场景。当依赖的数据发生变化时，computed 会自动重新计算结果。它本质上是一个 “计算属性”，就像数据的一个派生值，类似于普通的属性一样使用。
3. **watch**
	它主要用于在数据变化时执行特定的副作用操作，例如发送网络请求、修改 DOM、记录日志等。watch 侧重于监测数据的变化并做出相应的响应。

# 46、请说明Vue3中key的作用和原理？

在 Vue 3 里，`key` 是一个特殊的属性，它在渲染列表时起着关键作用

#### 1. 提高渲染效率

当列表中的元素发生增删改操作时，Vue 默认采用 “就地复用” 的策略。若没有 key，Vue 会尽可能复用已有的 DOM 元素，而不是重新创建。而给每个元素添加唯一的 key，Vue 就能精准识别每个元素，在更新列表时仅对发生变化的元素进行操作，避免不必要的 DOM 操作，从而提高渲染效率。

#### 2. 保持状态稳定

在渲染列表时，有时希望每个元素能保持自己的状态，例如表单输入框的内容。使用唯一的 `key` 可以确保每个元素的状态在更新时不会混乱，每个元素都能正确地保留自己的状态。

**原理：** 

Vue 使用虚拟 DOM 来提高渲染效率，当数据发生变化时，Vue 会生成新的虚拟 DOM 树，然后与旧的虚拟 DOM 树进行比对，找出差异并更新真实的 DOM。在这个比对过程中，`key` 作为每个元素的唯一标识符，帮助 Vue 快速判断哪些元素是新增的、哪些是删除的、哪些是移动的。

**Diff 算法优化**

Vue 的 Diff 算法利用 `key` 进行优化。当列表发生变化时，Vue 会根据 `key` 来匹配新旧虚拟 DOM 节点，从而确定哪些节点可以复用，哪些节点需要更新或删除。例如，当一个元素的 `key` 在新旧列表中都存在，且位置发生了变化，Vue 会移动该元素的 DOM 节点，而不是重新创建。



```vue
<template>
  <ul>
    <!-- 为每个列表项添加唯一的 key -->
    <li v-for="item in items" :key="item.id">{{ item.name }}</li>
  </ul>
</template>

<script setup>
import { ref } from 'vue';

const items = ref([
  { id: 1, name: 'Apple' },
  { id: 2, name: 'Banana' },
  { id: 3, name: 'Cherry' }
]);
</script>
```

在这个示例中，每个列表项都有一个唯一的 `id` 作为 `key`，这样 Vue 在更新列表时就能准确地识别每个元素，提高渲染效率。

# 47、简单讲一下 Vue3 响应式原理是怎么实现的？

Vue 3 的响应式原理是通过 **Proxy** 和 **依赖收集** 来实现的。

---

### 1. **Proxy 的作用**

Proxy 是 JavaScript 提供的一个原生对象，它能够拦截对象属性的读取和修改操作，从而实现数据的自动追踪和更新。Vue 3 使用 Proxy 来监听对象属性的读取（`get`）和设置（`set`），从而实现响应式。

**示例：**
```javascript
const target = { name: 'Vue' };
const handler = {
  get(target, key) {
    console.log(`读取属性 ${key}`);
    return target[key];
  },
  set(target, key, value) {
    console.log(`设置属性 ${key} 为 ${value}`);
    target[key] = value;
    return true;
  }
};
const proxy = new Proxy(target, handler);

proxy.name; // 输出：读取属性 name
proxy.name = 'Vue 3'; // 输出：设置属性 name 为 Vue 3
```

---

### 2. **依赖收集**

Vue 3 通过 **依赖收集** 来追踪哪些组件或计算属性依赖于某个响应式数据。当数据发生变化时，Vue 会通知所有依赖它的组件或计算属性进行更新。

**依赖收集的过程：**

1. 当组件或计算属性访问响应式数据时，Vue 会通过 `get` 拦截器记录当前依赖。
2. 当响应式数据发生变化时，Vue 会通过 `set` 拦截器通知所有依赖它的组件或计算属性进行更新。

---

### 3. **响应式实现流程**

以下是 Vue 3 响应式系统的简化流程：

#### **a. 创建响应式对象**

使用 `reactive` 函数将普通对象转换为响应式对象：
```javascript
import { reactive } from 'vue';

const state = reactive({ count: 0 });
```

#### **b. 追踪依赖**
当组件或计算属性访问 `state.count` 时，Vue 会通过 `get` 拦截器记录当前依赖：
```javascript
console.log(state.count); // 触发 get 拦截器，记录依赖
```

#### **c. 触发更新**
当 `state.count` 被修改时，Vue 会通过 `set` 拦截器通知所有依赖它的组件或计算属性进行更新：
```javascript
state.count = 1; // 触发 set 拦截器，通知更新
```

---

### 4. **`ref` 的作用**

`ref` 是 Vue 3 提供的另一个响应式 API，用于将基本类型数据（如 `number`、`string`）转换为响应式对象。它的实现原理与 `reactive` 类似，但内部会通过 `.value` 属性来访问和修改数据。

**示例：**
```javascript
import { ref } from 'vue';

const count = ref(0);

console.log(count.value); // 读取值
count.value = 1; // 修改值
```

---

### 5. **总结**

Vue 3 的响应式原理可以简单概括为：
1. 使用 **Proxy** 拦截对象属性的读取和修改操作。
2. 通过 **依赖收集** 记录哪些组件或计算属性依赖于某个响应式数据。
3. 当数据发生变化时，通知所有依赖它的组件或计算属性进行更新。

这种机制使得 Vue 3 的响应式系统更加高效和灵活，能够自动追踪数据变化并触发视图更新。

# 48、Vue3中是如何做到监测数组的变化的

在 Vue 3 中，**监测数组的变化**是通过 **Proxy** 和 **重写数组方法** 来实现的。

### 1. **Proxy 的局限性**

虽然 Vue 3 使用 **Proxy** 来监听对象的变化，但 Proxy 默认无法直接拦截数组的以下操作：

- 通过索引修改数组元素（如 `arr[0] = 1`）。
- 修改数组长度（如 `arr.length = 0`）。
- 调用数组的某些方法（如 `push`、`pop`、`splice` 等）。

因此，Vue 3 需要额外处理数组的变化。

------

### 2. **重写数组方法**

为了监测数组的变化，Vue 3 重写了数组的以下方法：

- `push`
- `pop`
- `shift`
- `unshift`
- `splice`
- `sort`
- `reverse`

当这些方法被调用时，Vue 3 会执行以下操作：

1. 调用原生的数组方法。
2. 触发响应式系统的更新通知。

**示例：**

```javascript
const arr = reactive([1, 2, 3]);

arr.push(4); // Vue 3 会拦截 push 方法并触发更新
```

------

### 3. **拦截数组索引修改**

Vue 3 通过 Proxy 的 `set` 拦截器来监测通过索引修改数组元素的操作。例如：

javascript

```javascript
const arr = reactive([1, 2, 3]);

arr[0] = 10; // Vue 3 会拦截 set 操作并触发更新
```

------

### 4. **拦截数组长度修改**

Vue 3 通过 Proxy 的 `set` 拦截器来监测数组长度的修改。例如：

javascript

```javascript
const arr = reactive([1, 2, 3]);

arr.length = 0; // Vue 3 会拦截 set 操作并触发更新
```



# 49、Vue为什么要用虚拟DOM？

1. 虚拟dom就是用js对象来描述真实Dom，是对真实Dom的抽象
2. 由于直接操作Dom性能低，但是js层的操作效率高，可以将Dom操作转化成对象操作。最终通过diff算法比对差异进行更新Dom
3. 虚拟Dom不依赖真实平台环境，可以实现跨平台

【虚拟DOM能够保证性能下限，实现最小量DOM更新】

# 50、Vue的diff算法原理是什么？

Vue的diff算法是平级比较，不考虑跨级比较的情况。内部采用深度递归的方式+双指针方式比较

1. 先比较两个节点是不是相同节点
2. 相同节点比较属性，复用老节点
3. 先比较儿子节点，考虑老节点和新节点儿子的情况
4. 优化比较：头头、尾尾、头尾、尾头
5. 比对查找，进行复用



# 51、谈谈你对keep-alive组件的理解？

keep-alive 用于缓存不活动的组件实例，避免重复创建和销毁组件，从而提高应用的性能和用户体验。

keep-alive 是一个组件缓存器，它能够缓存那些使用 <router-view> 或者条件渲染 (v-if, v-show) 的组件及其子组件实例。当组件切换时，keep-alive 会保留其状态和事件监听器，而不是销毁并重新创建。

我们一般用于性能优化中，例如对于复杂的表单或者包含大量数据的列表组件，使用 keep-alive 可以显著提升用户体验。

虽然 keep-alive 可以提高性能，但如果缓存了过多的组件实例，可能会消耗大量的内存。因此我们可以使用 include 和 exclude 配置来指定应该被缓存的组件名称或排除某些组件不被缓存。

# 52、谈谈Vue3中的性能优化有哪些？

1. 尽量减少data中的数据，合理的设置响应式数据
2. 使用数据时，缓存值的结果，不要频繁取值
3. 合理设置key，key保证唯一
4. v-show(频繁切换性能高)和v-if的合理使用
5. v-if和v-for不能连用 
6. 采用函数式组件 -> 函数式组件开销低
7. 采用异步组件 -> 借助webpack的分包策略
8. 使用keep-alive来缓存组件
9. 虚拟滚动、时间分片等策略
10. 数据冻结
11. 用路由懒加载、异步组件 
12. 防抖、节流 
13. 如果需要使用v-for给每项元素绑定事件时使用事件代理

# 53、esm规范和commonJS规范有哪些区别？

## (1) 语法与标准

`ESM`是原生JavaScript标准(ES6+),使用 `import/export`语法，浏览器和现代Node.js均支持。

`CommonJS`是Node.js 传统模块规范，使用 `require/module.exports`，仅 Node.js 原生支持，浏览器需工具（如 Webpack）进行转换。

## (2)加载方式

**ESM**的加载方式有静态加载和异步加载。

CommonJS的加载方式有动态加载





# 54、TS中type和interface的区别？

TypeScript 中 `type` 和 `interface` 的主要区别如下：

## (1)声明合并

**interface**：支持重复声明，自动合并属性。

```ts
interface User { name: string; }
interface User { age: number; }
// 合并为 { name: string; age: number; }
```

**type**：不可重复声明，重复定义会报错。

## (2)类型扩展

**interface**：通过 `extends` 继承。

```ts
interface Animal { kind: string; }
interface Dog extends Animal { bark: boolean; }
```

**type**通过交叉类型   `&`   扩展。

```ts
type Animal = { kind: string; };
type Dog = Animal & { bark: boolean; };
```

## (3)灵活性

**type**：可定义联合类型、元组、基本类型别名等复杂类型。

```ts
type ID = string | number;
type Coord = [number, number];
type Name = string;
```

**nterface**：仅描述对象结构，无法直接表达联合类型等。

## (4)使用场景

- 优先用 **interface**：对象类型、需声明合并、类实现。
- 使用 **type**：复杂类型（联合、交叉、工具类型）、基本类型别名。
