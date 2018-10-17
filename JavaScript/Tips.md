1. [JavaScript 中的相等性判断](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Equality_comparisons_and_sameness)
   1. JavaScript提供三种不同的值比较操作：
      - 严格相等 ("triple equals" 或 "identity")，使用 [===](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Identity) ,
      - 宽松相等 ("double equals") ，使用 [==](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Equality)
      - 以及 [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) （ECMAScript 2015/ ES6 新特性）
   2. 选择使用哪个操作取决于你需要什么样的比较。
   3. 简而言之，在比较两件事情时，双等号将执行类型转换; 三等号将进行相同的比较，而不进行类型转换 (如果类型不同, 只是总会返回 false );  而Object.is的行为方式与三等号相同，但是对于NaN和-0和+0进行特殊处理，所以最后两个不相同，而Object.is（NaN，NaN）将为 `true`。(通常使用双等号或三等号将NaN与NaN进行比较，结果为false，因为IEEE 754如是说.)

