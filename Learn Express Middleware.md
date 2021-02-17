# Learn Express: Middleware

> 这篇笔记记录了一些有关middleware的概念，因为我也没有很系统的认识，所以以知识点的形式逐条记录。

1. 中间件是什么？

   是代码，在服务器接收request之后，发送response之前，执行。

   > 在Express中，middleware是一个函数，本质上来说，Express应用是一系列中间件函数的调用。
   >
   > *An Express application is essentially a series of middleware function calls.*

2. 当接收到一条request时，~~下面这个代码会在route匹配之前先执行：~~
   
   ```js
   app.use((req, res, next) => {
    console.log(`${req.method} Request Received`);
   });
   ```
   
   > `req.method`会返回request的类型：GET/PUT/POST/DELETE。
   
   上面这个代码因为没有*path*参数，所以会在作为route匹配时，被成功匹配，执行其中的代码。而执行顺序，是按照他们在文件里面出现的次序，从上到下执行的。
   
   其中，带有三个参数的函数：`(req, res, next)`就是Express的middleware，这三个参数会在不同的middleware之间依序传递，第三个参数`next`，应该显式地在middleware里面调用，从而把request和response传递给下一个middleware。
   
   > 所有的Express middleware函数都可以访问request、response和next middleware。Route也是一个middleware。
   
3. Express文档里面的`app.use()`：

    `app.use([path,] callback [, callback...])`

    > `[]`里面的参数是可选参数（optional）。

    当第一个可选参数*path*给定的时候，就是一个可以处理特定path的request的middleware。

    另外，这个*path*可以是数组，包含多个path。

4. 开源的middleware：

    - morgan：用来log，在发送完response之后，会自动log。
    - body-parser：用来解析request的body，会自动把解析好的body object加到req里面，即`req.body`。
    - errorhandler: 错误处理。
    
5. 在动态路由的时候，可能会多次使用`.param()`方法，这会造成代码重复。所以Express提供了一个解决办法：

    ```js
    app.param('spellId', (req, res, next, id) => {
      let spellId = Number(id);
        try {
          const found = SpellBook.find((spell) => {
            return spellId === spell.id;
          })
          if (found) {
            req.spell = found;
            next();
          } else {
            next(new Error('Your magic spell was not found in any of our tomes'));
          };
        } catch (err) {
          next(err)
        }
    });
    ```

    注意：`spellId`前面不用写`:`这个符号。实际值储存在回调函数的第四个参数。总的来说，就是先找到那个param值，作为一个property存到req里面，传递下去。

6. Nested Routers & Merge Parameters

    `aRouter.use('/:aId/bbb', bRouter)`

    这样就把bRouter呢nested到 aRouter下面了。

    `const bRouter = express.Router({ mergeParams: true})`

    这样就让bRouter也可以访问aRouter里面的params了。

