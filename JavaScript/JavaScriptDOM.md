# 珠玑

函数的结构化程序设计原则：一个函数应该只有一个入口和一个出口，如果有多个出口应尽量放在函数开头。比如将预防性措施`if(!document.getElementById("imagegalley")) return false;` 放在开头。

为以后的扩展做准备工作总不是坏事。

注释：如果在编写某些代码时就觉得它们不容易理解，等日后再去阅读它们的时候就会更加困难。

请确保先包含 addLoadEvent.js ，因为 displayAbbreviation.js 依赖于它。在真实项目中，你通常还需要压缩脚本，并把它们合并成一个文件。

你已经看到，用 DOM 设置样式是多么容易，但你能做什么事并不意味着你应该做什么事。在绝大多数场合，还是应该使用 CSS 去声明样式。就像你不应该利用 DOM 去创建重要的内容那样。

如果你手里只有榔头，那么你看到的任何东西都像钉子。

#DOM

节点类型：

1. 元素节点，getElementById, getElementByTagName, getElementByClassName
2. 属性节点，getAttribute, setAttribute
3. 文本节点，当前元素节点.firstChild.nodeValue 可以get, set值

nodeType属性

nodeValue属性

## 选择器

选择器是jQuery的核心。一个选择器写出来类似`$('#dom-id')`。

返回的对象是jQuery对象。什么是jQuery对象？jQuery对象类似数组，它的每个元素都是一个引用了DOM节点的对象。

jQuery对象和DOM对象之间可以互相转化：

```javascript
var div = $('#abc'); // jQuery对象
var divDom = div.get(0); // 假设存在div，获取第1个DOM元素
var another = $(divDom); // 重新把DOM包装为jQuery对象
```



# JS

充斥着回调函数

## Promise

Promise “承诺将来会执行”的对象在JavaScript中称为Promise对象。

```javascript
// promise对象都要有resolve，reject参数，事实上它俩应该是函数，resolve放行，reject截止，还可以有参数，分别对应之后的回调函数：.then和.catch
new Promise(function (resolve, reject) {
  log('start new Promise...');
  var timeOut = Math.random() * 2;
  log('set timeout to: ' + timeOut + ' seconds.');
  setTimeout(function () {
    if (timeOut < 1) {
      log('call resolve()...');
      resolve('200 OK');
    }
    else {
      log('call reject()...');
      reject('timeout in ' + timeOut + ' seconds.');
    }
  }, timeOut * 1000);
}).then(function (r) {
  log('Done: ' + r);
}).catch(function (reason) {
  log('Failed: ' + reason);
});
```

Promise还可以做更多的事情，比如，有若干个异步任务，需要先做任务1，如果成功后再做任务2，任何任务失败则不再继续并执行错误处理函数。

要串行执行这样的异步任务，不用Promise需要写一层一层的嵌套代码。有了Promise，我们只需要简单地写：

```javascript
job1.then(job2).then(job3).catch(handleError);
```

其中，`job1`、`job2`和`job3`都是Promise对象。