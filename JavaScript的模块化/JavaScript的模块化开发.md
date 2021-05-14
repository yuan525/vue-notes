# 什么是JavaScript的模块

  将各个js文件的功能、逻辑分块，各自分装，相互独立，每一个模块各自决定对外暴露什么、引入什么。那么有什么办法可以将当前文件的一些方法、变量等暴露给其它文件，或者说怎么在当前文件使用其它文件暴露出的方法、变量等内容？

# JavaScript模块的几种实现

## 1. CommonJS

> CommonJS规范概述了同步声明依赖的模块定义。这个规范主要用于在服务器端实现模块化代码组织，但也可用于定义在浏览器中使用的模块依赖。CommonJS模块语法不能在浏览器中直接运行。

- 导出：module.exports 或 exports
> 注意：exports和module.exports的区别是：exports只是对module.exports的一个引用，相当于node为每个模块提供了一个exports变量指向module.exports。这等同于在每个模块顶部有一行
``` let exports = module.exports ```这样的命令

  例：在moduleB.js中导出一个值为“Hello world”的变量a:
  ``` 
  module.exports.a='Hello world' 
  ```

- 导入：require('模块标识符')

  例：在moduleA.js中导入moduleB.js所暴露出的变量a,并将其打印出来
  ``` 
  const moduleA=require('./moduleB.js')
  console.log(moduleA.a) 
  ```
  
## 2. AMD

> AMD（Asynchronous Module Definition）异步模块定义

- 与CommonJS的比较
  - CommonJS以服务端为目标环境；而AMD以浏览器为目标环境
  - CommonJS同步加载，一次性把所有模块全部加载到内存；而AMD为异步加载，让模块声明自己的依赖，而运行在浏览器中的模块系统会按需获取依赖，并在依赖加载完成后立即执行依赖它们的模块

- 导出：define(function (){return '值');
  
  例：在moduleB.js中导出一个值为“Hello world”的变量a:
  ```
  define(function(){
  	return {
  		a:"Hello World"
  	}
  })
  ```
  
- 导入：require(['模块标识符'], function ('模块变量引用'){// 代码});
  
  例：在moduleA.js中导入moduleB.js所暴露出的变量a,并将其打印出来
  ```
  require(["./moduleB.js"],function(mdA){
  	console.log(mdA.a)
  })
  ```
  
## 3. UMD

> 为了统一CommonJS和AMD生态系统，通用模块定义（UMD, Universal ModuleDefinition）规范应运而生。UMD可用于创建这两个系统都可以使用的模块代码,下面是只包含一个依赖的UMD模块定义的示例（来源为GitHub上的UMD仓库）：
```
  (function (root, factory) {
   if (typeof define === 'function' && define.amd) {
   //AMD。注册为匿名模块
   define(['moduleB'], factory);
   } else if (typeof module === 'object' && module.exports) {
   //Node。不支持严格CommonJs
   //但可以在node这样支持module.exports的类CommonJS环境下使用
   module.exports = factory(require('moduleB'));
   } else {
   //浏览器全局上下文(root 即 window)
   root.returnExports = factory(root.moduleB);
   }
  }(this, function (moduleB) {
   //以某种方式使用moduleB
   //将返回值作为模板的导出
   //这个离职返回了一个对象
   //但是模板也可以返回函数作为导出值
   return {};
  }));
```

## 4. ES6

> 从各个方面来说，ES6模块系统是集AMD和CommonJS之大成者

- 导出：export
  - 命名导出
  
     1. 声明一个变量并导出：
     ```
	 export const a='aaa';
	 ```
	 2. 变量声明和导出可以不在同一行
     ```
	 const a = 'aaa';
	 export {a};
	 ```
	 3. 导出时可以起别名
     ```
     const a = 'aaa';
     export {a as A};
     ```
	 4. 可以在一个模块中声明多个命名导出
     ```
     export const a='aaa';
	 export const b='bbb';
	 export const c='ccc';
     ```
	 5. 多个导出也可以先声明后导出
     ```
     const a='aaa';
	 const b='bbb';
	 const c='ccc';
	 export {a as A,b,c};
     ```
  - 默认导出default:
    > 每个模块只能有一个默认导出。因为命名导出和默认导出不会冲突，所以ES6支持在一个模块中同时定义这两种导出：
  
     例：下面的例子定义了一个默认导出，外部模块可以导入这个模块，而这个模块本身就是a的值  
	 ```
	 const a='aaa';
	 export default a;
	 ```
	 
- 导入：import
> 导入的模块标识符可以是相对路径，也可以是绝对路径，但必须是纯字符串，不能是动态计算的结果，例如:不能是拼接的字符串

1. 普通导入
  ```
  import {a} from './moduleB.js'
  ```
2. 也可以起别名
```
import {a as A} from './moduleB.js'
```
3. 可以用*批量获取导入的值
```
//moduleB.js
const a='aaa';
const b='bbb';
const c='ccc';
export {a as A,b,c};
//-------------------------------------------
//moduleA.js
import {* as item} from './moduleB.js'
console.log(item.a);//'aaa'
console.log(item.b);//'bbb'
console.log(item.c);//'ccc'
```
4. 获取默认导出的值
```
//moduleB.js
const a='aaa';
export default a;
//-------------------------------------------
//moduleA.js
//以下两种导入方式效果一样
import {default as item} from './moduleB.js'
import item from './moduleB.js'
console.log(item) //'aaa'
```
