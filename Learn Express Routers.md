# Learn Express: Routers

> 当我们写了很多的*routes*，文件会变得越来越长，这不太好。Express呢提供了一个功能叫*Routers*，解决了这个问题。
>
> Routers呢，是mini版本的Express，他们呢可以处理路由匹配，处理request，发送response，而且共用一个port。他们也使用`.get()`, `.put()`, `.post()`, `.delete()`这些routes。
>
> 那Routers是怎么让代码变得干净的嘞？

## [1] `Express.Router`

### a) 创建一个Router

我们在Express上调用`.Router()`方法，创建一个Router实例。

```js
const express = require('express');
const app = express();
 
const monsters = {
  '1': {
    name: 'godzilla',
    age: 250000000
  },
  '2': {
    Name: 'manticore',
    age: 21
  }
}
 
const monstersRouter = express.Router();
```

### b) 使用一个Router

我们使用`app.use()`来安装（mount）这个router。

```js
app.use('/monsters', monstersRouter);
 
monstersRouter.get('/:id', (req, res, next) => {
  const monster = monsters[req.params.id];
  If (monster) {
    res.send(monster);
  } else {
    res.status(404).send();
  }
});
```

 上面的例子，`monstersRouter`会匹配`/monsters/:id`。

> 当有一个request : ` GET /monsters/1 ` 来的时候，Express会在`app.use()`这里匹配到`/monsters`，然后Express的route匹配算法会从`monstersRouter`的routes里面找完整的匹配的path。

## [2] 使用多个Router Files

通常，会让每一个router在自己的文件里面，然后在main app里面`require`他们。

在monsters.js里面：

```js
// monsters.js
const express = require('express');
const monstersRouter = express.Router();
 
const monsters = {
  '1': {
    name: 'godzilla',
    age: 250000000
  },
  '2': {
    Name: 'manticore',
    age: 21
  }
}
 
monstersRouter.get('/:id', (req, res, next) => {
  const monster = monsters[req.params.id];
  if (monster) {
    res.send(monster);
  } else {
    res.status(404).send();
  }
});
 
module.exports = monstersRouter;
```

在main.js里面：

```js
// main.js
const express = require('express');
const app = express();
const monstersRouter = require('./monsters.js');
 
app.use('/monsters', monstersRouter);
```