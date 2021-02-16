# Learn Express

> 

## [1/15] Introduction

### Express是啥？

是一个JavaScript框架，是一个Node的模块（module），用来创建web服务器和APIs。

### CRUD是啥？

*Create, Read, Update, Delete*。比如用Express实现一个API，他应该允许用户执行这些操作。

## [2/15] 启动一个服务器

```js
const express = require('express');
const app = express();
```

因为express是一个Node的module，要先引入一下。

之后调用`express()`，会返回一个Express应用的实例，这个应用呢可以用来启动一个服务器，指定服务器的行为。

服务器的作用就是监听需求嘛，然后执行一些代码满足这个需求，然后返回一个response。那怎么监听呢？

用一个方法：`app.listen()`。

```js
const PORT = 4001;
app.listen(PORT, () => {
  console.log(`Server is listening on port ${PORT}`);
});
```

这个listen方法，接收两个参数：

- where to listen for new requests: port number
- callback: 当服务器开始运行，会调用这个。

## [3/15] 写一个Route

什么是Route？

当我们的服务器开始监听（listening）之后，它会对任何的所有的requests做出响应。我们要针对不同的request，做出相应的响应，就需要注册（register）一系列的routes。

Routes根据request的**路径**（*path*）和**HTTP verb**，来定义不同的响应。

> *path* 不是完整的URL，是在hostname和port之后的部分。



Express使用`app.get()`来匹配 `GET` request，比如：

```js
const moods = [{ mood: 'excited about express!'}, { mood: 'route-tastic!' }];
app.get('/moods', (req, res, next) => {
  // Here we would send back the moods array in response
});
```

Express的routes，像`app.get()`，通常都有两个参数，一个`path`，一个callback function，负责处理request，然后发送响应response。

匹配到path之后，执行回调函数。回调函数有三个参数：

- **request**: the request sent to server.
- **response**: the response that the express server should eventually send to the client.
- next

如果没有routes匹配到客户端的request，Express服务器会发送404 Not Found响应。

## [4/15] 发送一个响应

>  HTTP遵守：一个request，对应一个response。

怎么发送 *response* ？

```js
const monsters = [
    { type: 'werewolf' }, 
    { type: 'hydra' }, 
    { type: 'chupacabra' }
];

app.get('/monsters', (req, res, next) => {
  res.send(monsters);
});
```

即：`.send()` 方法。这个方法会把输入放到 *response body* 里面。

> 也可以使用`res.json()` 方法。

## [5/15] Route匹配原则

**原则**：使用第一个匹配到的Route（path 和 HTTP verb），然后调用它的callback。

> 如果没有匹配到的，或者匹配到了，但是没有在callback里面发送response，都会自动发送一个404 Not Found的response。

## [6/15] 动态路由

通过路由参数（route parameters）来实现这个功能。

语法是这样的：parameters是 *path* 里面以 `:` 开头的一小段（path segment）。比如：`/monsters/:id` 会匹配 `/monster/1` 和 `.monster/45` 。

怎么实现的呢？

```js
const monsters = { 
    hydra: { 
        height: 3, 
        age: 4 
    }, 
    dragon: { 
        height: 200, 
        age: 350 } 
};

// GET /monsters/hydra
app.get('/monsters/:name', (req, res, next) => {
  console.log(req.params) // { name: 'hydra' };
  res.send(monsters[req.params.name]);
});
```

Express会解析（parse）任何的 parameters，抽取他们的实际值，把这个实际值加到一个object里面：`req.params` 。key就是parameter在route里面定义的名字（即：`/:xxx`，这个xxx），value就是来自request的实际值。

## [7/15] 设置状态码

默认情况下，`res.send()`会发送一个`200 OK`的状态码。

`res`这个object有一个`.status()` 方法，可以设置状态码：

```js
const monsterStoreInventory = { 
    fenrirs: 4, 
    banshees: 1, 
    jerseyDevils: 4, 
    krakens: 3 
};

app.get('/monsters-inventory/:name', (req, res, next) => {
  const monsterInventory = monsterStoreInventory[req.params.name];
  if (monsterInventory) {
    res.send(monsterInventory);
  } else {
    res.status(404).send('Monster not found');
  }
});
```

可以把`.send()`链接🔗在后面：`res.status(404).send('404 Not Found')` 。

## [8/15] 长路径匹配原则

假设有一个Request：`GET /expressions/1`。

第一个Route是：`app.get('/expressions', () => {})`。

第二个Route是： `app.get('/expressions/:id', () => {})` 。 会匹配这个啦。

## [9/15] 其他的HTTP方法

Express还提供了别的方法：

- `app.put()`
- `app.post()`
- `app.delete()`

## [10/15] 使用查询Queries

当我们要使用`app.put()`方法时，会更新相应的数据，这时候request里面会有更多的信息，这些信息的形式就是：查询字符（*query string*）。

**Query strings** 出现在URLs里面的 *path* 的末尾，用 `?` 表示。

比如：`/monsters/1?name=chimera&age=1` 

它的查询字符就是: `name=chimera&age=1` 

他的 *path* 是:  `/monsters/1/`

> 即： 查询字符**不**算作route path的一部分。Express把他们解析成object，加到request的body里面，作为 `req.query` 的value。
>
> request body 格式这样： 
>
> ```js
> {
>     ...,
>     query: {
>         name: 'chimera',
>     	age: 1
>     }
> }
> ```

怎么用呢？

```js
const monsters = { 
    '1': { 
        name: 'cerberus', 
        age: '4'  
    } 
};

// PUT /monsters/1?name=chimera&age=1
app.put('/monsters/:id', (req, res, next) => {
  const monsterUpdates = req.query;
  monsters[req.params.id] = monsterUpdates;
  res.send(monsters[req.params.id]);
});
```

## [11/15] 匹配HTTP Verb

as the title.

## [12/15] 新建 POST

`PUT`是用来更新已存在的，`POST`呢，是用来创建一个新的。

要创建一个新的数据，比如创建一个新的魔鬼👾，client需要向 `/monsters` 发射一个 `POST` request，但是呢，client并不知道这个新的魔鬼的 `id`， 要等到服务器创建好，然后发送回来它的`id`。因此，没有 *route parameter* 。

那怎么搞？

可以用 *query string*。

response的status：

- 成功： *201 Created*

## [13/15] 删除

response status code:

​	成功： *204 No Content*

## And that's all notes for this short course.



