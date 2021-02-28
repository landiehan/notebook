# Test Driven Development (TDD)

> 这一部分的目标是学习TDD的基础，理解测试是怎么捕获和识别代码里的问题。

主要内容：

- 怎么写一个*自动测试套件 （automated test suite）*
- 描述一个测试套件在软件开发中是怎么使用的
- 解释自动测试的好处
- 使用Mocha和Chai写测试
- 使用TDD技术写程序

## 2级标题

### 手动测试

*软件测试*是评估软件完成度和质量的过程。通常呢是执行一下系统的一部分，比较一下实际的表现和预期的表现。

一种形式是*手动测试*。在web应用里面呢，可能是在页面里点击、拖拽或者输入之类的。

### 自动测试

另一种形式就是*自动测试*。相比于手动的，更快、更可靠、可维护。自动测试的流程是这样的：

1. 写代码和对应的测试
2. 在终端输入命令执行测试
3. 如果应用的表现符合预期，则测试通过，开发完成。
4. 如果应用的表现不符合预期，至少会有一个测试失败，根据提示修复代码，重复第2步。

### 测试套件

> 测试也是代码，可以把定义应用程序的代码称为*实现代码*，把定义测试的代码称为*测试代码*。

测试的集合称之为测试套件（test suite）。

### 作为文档的测试们

文档是与实现代码分开的任何内容，这些内容解释了应用是如何工作或如何使用的。

文档可以是很多形式，包括纯文本，图表......和测试！测试作为文档的话，还提供了可执行的代码，确保应用按照描述运行。

### regression

当添加新功能时，可能会出现错误，如果错误出现在以前开发的功能里面，称之为*regression*。具体到某一个函数，可以说那个函数*regressed*。

如果发生了regression，执行测试的输出会有提示。

### 总结

自动测试呢在编写的时候会消耗一些时间，但是这个花费对比于他的收益是很小的。自动测试有这些好处：

- 提高了你的自信（相比于手动测试，你可以保证你的产品按照预期运行）。
- 改进了文档。
- 减少了regression的可能性。

## 什么是TDD

![TDD-Cycle](C:\Users\Landi\Desktop\notebook\TDD-Cycle.png)

RED (test failed) => GREEN (test passes) => REFACTOR

## 使用Mocha写测试

### 介绍

Mocha是一个*测试框架（test framework）*。使用测试框架可以组织（organize）和自动化（automate）测试，而不用手动测试每一个函数。

这一小节学习内容：

- 学习写一个基础的Mocha测试套件
- 使用Node的`assert.ok`方法来验证代码的预期输出
- 理解、应用测试的四个阶段，来创建表达性的（expressive）测试套件
- 评估测试的质量

### 安装Mocha - 1

在写测试代码之前，需要使用Node.js和npm来创建一个JavaScript项目，然后安装Mocha。

- *Node*让我们可以在终端运行JavaScript。
- *npm*是一个Node工具，用来下载、安装和管理包。
- *Mocha*是用来测试JavaScript代码的包。

然后执行：

```bash
npm init
npm i mocha -D
```

### 安装Mocha - 2

要运行Mocha，需要现在**package.json**的`scripts`对象里面，把`"test"`的值设置成`mocha`。然后就可以通过：

```bash
npm test
```

### describe和it

通常类比实现代码的结构来组织测试代码的结构。用describe表示一组测试，it表示一个测试。这俩都接收两个参数，一个string，描述测试内容，一个是回调函数，在这里写测试代码。

### assert

测试是用来比较预期输出和实际输出的。

作为一个Node模块，可以require它。

```javascript
const assert = require('assert');
assert.ok();
```

如果传给`assert.ok`的是`false`，就会有`AssertionError`错误抛出。这个错误给Mocha汇报，说test失败了，然后Mocha会log错误信息到控制台。

### Setup, Exercise, Verify

好的测试代码清晰的划分这三个部分：

- Setup - 创建测试需要的对象、变量、设计条件（condition）
- Exercise - 执行你要测试的函数
- Verify - 检查预期和实际结果，可以用`assert`库。

### Teardown

有的测试需要第四个阶段叫做*teardown*。

- Teardown - 重置在这个测试之后所有被改变的情况。

用来在下一个测试执行前，重置环境，防止这个测试的结果影响别的测试，包括：

- 修改了文件和目录结构？
- 改变文件的读写权限？
- 修改数据库里的一条记录？

有些情况就不需要这个阶段，因为没有需要重置的情况。

### Hooks

>  上面👆呢，用teardown在`it`里面，让测试变得孤立，但是不可靠。万一代码还没执行到teardown，系统就出错了，那咋整？
>
> 这个阶段就不会执行了，比如：在某个测试中，你先打开了文件进行读写操作，然后在teardown阶段关闭文件，如果这个阶段没执行，文件保持打开，可能会在将来的测试中误报（false negatives）。
>
> Mocha提供了*hooks*来解决这个问题。

hook是在特定事件发生时执行的代码块。可以用来设置和重置条件，像Setup阶段和teardown阶段那样。

在Mocha中，钩子写在`describe`里面：

```javascript
describe('example', () => {
	afterEach(() => {
		// teardown goes here
	});
	
	it('.sample', () => {
		// test goes here
	})
})
```

👆这个例子里面，每一个`it`执行之后，在`afterEach`里面的函数会被调用执行。

Mocha库里面呢还有`before()`，`beforeEach()`，`after()`。

### 总结

现在呢，就可以用Mocha和Node的`assert.ok()`写测试啦。还学了这些：

- 使用npm安装Mocha
- 使用`describe()`和`it()`组织测试代码结构。
- 使用四个阶段确保测试的孤立（isolated）和具有表达性（expressive）。
- 使用钩子确保测试代码的可靠性。
- 使用`assert.ok()`写预期和实际的比较。

**好的测试6条标准**：fast, complete, reliable, isolated, maintainable, and expressive。