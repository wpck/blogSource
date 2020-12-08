---
title: javaScript中的异步操作
date: 2020-11-30 20:16:03
tags: javaScript
cover: cover.png
---
连续的执行叫做同步,异步是指一个任务分两段,先执行第一段,等到第一段响应之后,接着执行第二段; js 语言的执行环境是单线程的,如果都是同步的话,页面会卡顿体验极差;   
js异步的实现方式是把异步操作放到事件循环队列里,等主线程执行完毕再执行事件队列里的事件;
### 异步的实现方式
+ 回调函数   
把任务的第二段单独写在一个函数里面,第一段执行完毕之后,直接执行该函数;问题在于多个回调函数的嵌套,导致"回调地狱";   
+ Promise 函数
Promise 函数使用 then 方法对回调函数进行了改进,多个then代码冗杂,语义不清晰;
+ generator 函数   
generator函数式协程在js的实现,特点是可以交出函数的执行权(即暂停执行);该函数是一个封装的异步任务,返回一个内部指针(遍历器),用 yeild 注明需要暂停的地方,调用指针的 next() 方法,移动内部指针; next 返回包含当前执行阶段信息的一个对象({value: '', done: true/false})
```
function* gen(i) {
    let j = yield i + 1;
    return j;
}
let g = gen(1);
g.next();    //{value: 2, done: false}
g.next();    //{value: undefined, done: true}
```
generator函数将异步操作处理的很简洁,但流程管理却不方便,即何时执行执行第一阶段,何时执行第二阶段;
+ async 函数   
ES7 提供了async函数,是 generator 函数的语法糖; 将* 换成 async, 将 yield 换成 await, async 函数的实现就是将generator函数和自动执行器包在一个函数里; async的改进体现在下面4点:
>1. 内置执行器: generator 函数的执行依靠执行器,async自带执行器,不用试用 next() 调用执行;
>2. 更好的语义化: async/await 比 */yield 语义更明确, async表示函数里有异步操作, await表示后面的表达式,需要等待结果;
>3. 更广的适用性: yield 后面只能是promise对象或thunk函数, await后面可以是promise对象和原始类型的值(此时,是同步操作);
>4. 返回值是promise: 可以使用then方法指定下一步操作, generator函数的返回的指针对象遍历器,使用next遍历操作复杂;
async函数规则比较简单,难的是错误处理机制:
>+ async函数return语句的返回值是then方法的参数,函数内部抛出错误,会被catch函数捕获; 
>+ 只有当async函数内部所有的await异步操作执行完毕,才会执行then方法指定的函数,只要一个await后面的promise变成reject状态,整个async就会被中断执行,故可以把所有await命令放入 try{} catch{} 表达式中;
使用中的要点:
>+ await命令后的Promise可能返回reject状态,把await写在 try/catch 表达式里;
>+ 多个await如果不存在继发关系,可以让它们同时触发;
```
let [a, b] = await Promise.all([getA(), getB()]);     //写法一
let aPromise = getA(), bPromise = getB();   //写法二
let a = await aPromise, b= await bPromise; 
```
>+ await 命令只能在 async 函数中使用,否则会报错;

以上文档参考[W3CSchool异步教程](https://www.w3cschool.cn/ecmascript/vis51q5w.html)

----------
js Event Loop事件循环机制:   
所有的任务分为同步任务和异步任务两种,同步任务会直接进入主线程并执行,异步任务遵循任务队列(Event Queue)机制;主线程的任务执行完毕,将读取Event Queue内的任务推入主线程继续执行,上述过程的不断重复就是Event Loop
>js 的任务分为两大类: 宏任务(macro-task) 和 微任务(micro-task)
>+ 宏任务: 包括整体script代码, setTimeout, setInterval
>+ 微任务: Promise.then(), process.nextTick(nodejs环境)
>+ Event Queue 分为微任务Quene和宏任务Queue,执行顺序为: 微任务Queue > 宏任务Queue


