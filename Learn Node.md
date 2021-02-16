# Learn Node.js 

> 一直以来，JavaScript被认为只能在浏览器里运行，写一些前端的东西，然后，有很多构建脱离浏览器的JavaScript环境的尝试（off-browser），在2009年，Node.js的发明，取得了空前的流行，被很多大公司使用。

## Node.js是啥？

Node.js是一个JavaScript的运行环境，允许在浏览器之外执行JavaScript代码。

> 在运行时，把JavaScript转换成电脑可以执行的代码。

## Node REPL

REPL就是read-eval-print loop。在终端（terminal）里输入`node`，回车，就进入了node.js的REPL环境。

输入：`console.log(Object.keys(global))`，会打印出Node的全局对象（global object）：

```js
[ 'global',
  'process',
  'Buffer',
  'clearImmediate',
  'clearInterval',
  'clearTimeout',
  'setImmediate',
  'setInterval',
  'setTimeout',
  'console',
  'module',
  'require' ]
```

>  这里没有`Window`这个对象，这个是在浏览器里才有的。

## 用Node运行一个程序

> Node本来是设计的用来做server-side web development。

```bash
node myProgram.js
```

## Process Object

前面也看到了，`process` 是一个全局的object，它里面有很多有用的methods和信息哦。

process就是进程的意思嘛，其中有一个对象，`process.env`，它里面就是当前正在运行的进程的信息。

比如：

- `process.env.PWD`， 就是当前进程所在的目录。

  >  pwd： print working directory。

- 在开发过程中，运行环境是development，给用户的是production模式，我们可以在开发模式进行一些测试：

  ```js
  if (process.env.NODE_ENV === 'development') {
      console.log('Test, test.');
  }
  ```

- `process.memoryUsage()`

  返回这些：

  ```js
  	{ rss: 26247168,
   	heapTotal: 5767168,
   	heapUsed: 3573032,
   	external: 8772 }
  ```

  > `process.memoryUsage().heapUsed`代表的是当前进程用的内存字节数。

- `process.argv` 
  是啥呢？

  比如说输入这个：

  ```bash
  node myProgram.js testing several features
  ```

  那么`argv`就是**5**个元素的数组，`console.log(process.argv[3])`会打印`several`。



## [6/15] 核心模块和本地模块

> core modules and local modules

模块化很关键。（modularity）

本质来说，一个模块就是储存在一个独立的文件里面的一堆代码。

### 1. 核心模块

核心模块定义在Node.js源代码里面，存在`lib`这个文件夹里。用的时候，这样：

```js
let events = require('events');
```

### 2. 本地模块

`module.exports`是一个特殊的JavaScript object。

本地模块的话，先存到`module.exports`里面，然后require一个本地模块的路径。

## 第三方模块---NPM

NPM：node package manager, 一个在线的软件库。下载Node的时候，npm的命令行工具也有了。

npm 库里面有很多流行的框架，比如：`express`和`react`。

>  *nodemon*: 这个库可以监听你的项目里的文件，有变动时会自动重启你的应用。

## [7/15] 事件驱动架构

都说Node是event-driven architecture。啥意思？

我们写web应用时，经常写处理一些不知道什么时候发生的事件的逻辑，比如点击事件。Node在创建时，也给后端环境应用了同样的事件驱动原则。

>  真的吗？我不信，在哪儿？？😲

Node提供了一个`EventEmitter` 类， 通过核心模块`events`可以用它。

```js
// Require in the 'events' core module
let events = require('events');
 
// Create an instance of the EventEmitter class
let myEmitter = new events.EventEmitter();
```

emitter??? 姨妹特？？

意思是发射器，每一个*发射器* 实例（instance）都有一个`.on()`方法，接受两个参数：

- 事件名字 string
- 监听回调函数 listener callback function

还有一个方法：`.emit()`。这个会说话，通知你那个事件发生了。接受两个参数：

- 事件名字 string
- 数据，data。会传给那个监听回调函数。

## [8/15] Node和异步的Javascript

Node提供了很多APIs，进行异步的任务，接收一个callback函数作为参数。

比如： `setTimeout()`。

用的机制是**事件循环** ， event loop。

流程：？？？？？？？？？？？ 不清楚

反正要等同步的任务先完成，再执行异步的任务。

如果你同步的任务是while(true) {}, 那前面的异步任务永远也不会执行了。

## [9/15] 输入输出

`process.stdin`是一个EventEmitter实例。

设置一个监听事件：

```js
process.stdin.on('data', (userInput) => {
  let input = userInput.toString().trim();
  console.log(input)
});
```

`data`是一个事件，用户输入然后点回车之后，会触发。

用户输入，也就是这个`userInput`是一个Node 的`Buffer`类的实例，要变成一个string打印。还会带一个回车字符，要去掉。

## [10/15] Errors

Node里面很多的异步APIs都会使用*error-first callback function*：回调函数把error作为第一个期待的参数，data作为第二个。

如果没有错误，那第一个参数就是`undefined`。

```js
const errorFirstCallback = (err, data)  => {
  if (err) {
    console.log(`There WAS an error: ${err}`);
  } else {
     // err was falsy
      console.log(`There was NO error. Event data: ${data}`);
  }
}
```

为啥？？？

`try...catch`抓不住异步的错误啦。

`api.async('input', errorFirstCallback)`。

## [11/15] 文件系统

Node在核心模块里提供了一个模块叫 `fs` 。 用来和 file system 进行交互。

`fs`里面的方法，每一个都有同步和异步的版本。

怎么用？ 比如 `.readFile()`方法：

```javascript
const fs = require('fs');
 
let readDataCallback = (err, data) => {
  if (err) {
    console.log(`Something went wrong: ${err}`);
  } else {
    console.log(`Provided file contained: ${data}`);
  }
};
 
fs.readFile('./file.txt', 'utf-8', readDataCallback);
```

1. 首先require我们的`fs`核心模块。

2. 搞一个error-first callback函数。

3. 调用fs里的readFile方法， 有三个参数：
   - 一个字符串string， 代表路径。
   - 文件的解码格式，通常是： `utf-8`。
   - 当异步读取文件时，要加一个错误优先的回调函数。

## [12/15] 可读流

> Readable Streams

有时候不想一次读完文件内容，内存不够。

`readline`是一个核心模块，他有一个方法：`.createInterface()`，这个方法，会返回一个`EventEmitter`的实例哦：

```js
const readline = require('readline');
const fs = require('fs');
 
const myInterface = readline.createInterface({
  input: fs.createReadStream('text.txt')
});
```

`fs.createReadStream('text.txt')`会从**text.txt** 文件里面创建一个stream。

现在`myInterface`是一个发射器的实例。我们给这个发射器搞一个监听函数，监听`line`事件。

这个`line`事件，每次有一行读取出来就会触发一次。

```js
myInterface.on('line', callback);

function callback(data) {
    console.log(data);
}
```

## [13/15] 可写流

和上一个很像，用这个： `fs.createWriteStream()` 。

```js
const fs = require('fs')
 
const fileStream = fs.createWriteStream('output.txt');
 
fileStream.write('This is the first line!'); 
fileStream.write('This is the second line!');
fileStream.end();
```

设置输出文件为`output.txt`，然后往里面写。可读流会在读完的时候停止，这里呢我们要自己去关上，不然它就一直一直是打开的。

## [14/15] 创建一个HTTP服务器

> Node在设计之初，就是把后端开发作为一个很高的优先级。其中的一个需求就是创建web服务器的能力，也就是搞一些进程去监听来自客户端的需求（requests），然后返回一些响应（responses）。

Node把这个需求设计在他的核心模块里面啦，叫做 `http` module。

`http.createServer()`这个方法会返回一个实例，叫做`http.server`。

`http.server`这个有一个方法`.listen()`，这个呢会导致服务器去监听即将到来的连接🔗。

用法：

```js
const http = require('http');
 
let requestListener = (request, response) => {
  response.writeHead(200, {'Content-Type': 'text/plain' });
  response.write('Hello World!\n');
  response.end();
};
 
const server = http.createServer(requestListener);
 
server.listen(3000);
```

主要是看一下这个createServer创建服务器的这个函数，的回调函数啦。

- 接收两个参数：request， response
- 这个服务器监听的是3000，每次有request向这个服务器发射的话，Node就会调用这个callback监听函数。传进去这两个参数。
- **职责**： 设置response的header和body。
- 必须发个信号：这个交互完成了，（通过`response.end()`）。

> 我们把requestListener传给createServer，和 `EventEmitter`的  `.on()`很像。每次HTTP请求发送到这个服务器的这个3000port的时候，就会执行这个listener回调函数。
>
> 即： *http://localhost:3000/*
>
> localhost指的是正在运行这个Node进程的💻。





