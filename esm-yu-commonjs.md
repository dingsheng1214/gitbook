# ESM 与 CommonJS

## 使用方法比较

|    | Commonjs                                      | ESM                                                                                                                                                                                                                         |
| -- | --------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 导出 | <p>exports.fs = fs<br>module.exports = fs</p> | <p>1 export default fs<br>2 export function readFile<br>3 export {readFile, read}<br>4 export * from 'fs'</p>                                                                                                               |
| 导入 | const fs = require('fs')                      | <p>1 import fs from 'fs'<br>import {default as fs} from 'fs'<br>2 import {readFile} from 'fs'<br>3 import {readFile, read} from 'fs'<br>4 import * as fs from 'fs'<br>import fs, {readFile as readFileAlias } from 'fs'</p> |

## Commonjs 与 ESM 差异

* CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。
* CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。
* CommonJS 模块的`require()`是同步加载模块，ES6 模块的`import`命令是异步加载，有一个独立的模块依赖的解析阶段。

### 差异一

#### CommonJS 模块输出的是一个值的拷贝

![内存在中间，一个导出通用 JS 模块指向一个内存位置，然后将值复制到另一个，导入 JS 模块指向新位置](https://raw.githubusercontent.com/shengding1214/imageCloud/master/31\_cjs\_variable-500x113.png)

```js
// a.js
let counter = 1
exports.counter = 1
counter++
// b.js
const { counter } = require('./a.js')
console.log(counter) // 输出 1
```

#### ES6 模块输出的是值的引用

![与上图相同，但 main.js 的模块环境记录现在将其导入链接到其他两个模块的导出。](https://raw.githubusercontent.com/shengding1214/imageCloud/master/30\_live\_bindings\_02-500x206.png)

```js
// a.js
export let counter = 1
counter++
// b.js
import {counter} from './a.js'
console.log(counter) // 输出 2
```

### 差异二

CommonJs 加载的是一个对象(即`module.exports`属性)，该对象只有在脚本运行完才会生成。而ES6模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。

### 差异三

#### ESM 异步加载模块

1. 找到程序的入口文件
   1. 在html中`<script src='main.js' type='module'>`
   2. 在node中`package.json`中通过`"main": "./main.js"`
2.  根据入口文件`main.js`的导入语句分析入口文件的依赖模块

    ![img](https://raw.githubusercontent.com/shengding1214/imageCloud/master/09\_module\_specifier.png)
3. 然后再分析`counter.js`的依赖，就这样一层一层的遍历，找出依赖并加载

​ ![img](https://raw.githubusercontent.com/shengding1214/imageCloud/master/10\_construction.png)

1. 如果浏览器主线程等待这些文件一个个都下载完成，那许多其它任务将堆积在主线程队列中

​ ![image-20211109092052113](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211109092052113.png)

1.  像这样阻塞主线程会使使用模块的应用程序使用速度太慢。所以ESM规范将算法拆分成三个阶段

    ![img](https://raw.githubusercontent.com/shengding1214/imageCloud/master/07\_3\_phases-20211109094607777.png)

    `构建`--查找、下载所有文件并将其解析为`模块记录(Module Record)`并生成`模块映射(Module Map)`

    ![显示正在解析为模块记录的 main.js 文件的图表](https://raw.githubusercontent.com/shengding1214/imageCloud/master/25\_file\_to\_module\_record-500x199.png)![用模块记录填充的模块映射图中的“获取”占位符](https://2r4s9p1yi1fa2jd7j43zph8r-wpengine.netdna-ssl.com/files/2018/03/25\_module\_map-500x239.png)

    `实例化`--在内存中找到 `内存地址(Memory Location)`来存放所有导出的值，然后让导出和导入都指向该内存地址，这称为`链接`

    ​ ![](https://raw.githubusercontent.com/shengding1214/imageCloud/master/30\_live\_bindings\_02-500x206-20211109094741166-20211109094744182.png)

    `评估`--运行代码以使用变量的实际值填充

#### CommonJS 同步加载

因为CommonJS是从文件系统加载文件，这比通过Internet下载花费的时间少得多。这意味着Node可以在加载文件时阻塞主线程。同时也意味着在返回模块实例之前，你要遍历整个树，加载、实例化和评估任何依赖项。

![img](https://raw.githubusercontent.com/shengding1214/imageCloud/master/12\_cjs\_require.png)

## Node.js的模块加载方法

* 采用ESM的情况
  * `.mjs`后缀的js文件内可以使用`export`和`import`
  * `package.json`中指定`type`为`module`
* 采用CommonJS的情况
  * `.cjs`后缀的js文件内可以使用`exports`和`require`
  * `package.json`中指定`type`为`commonjs`

注意，ES6 模块与 CommonJS 模块尽量不要混用。`require`命令不能加载`.mjs`文件，会报错，只有`import`命令才可以加载`.mjs`文件。反过来，`.mjs`文件里面也不能使用`require`命令，必须使用`import`。

### package.json的main字段

```json
// ./node_modules/A/package.json
{
  "type": "module",
  "main": "./src/index.js"
}
```

上面代码指定项目的入口脚本为`./src/index.js`，它的格式为 ES6 模块。如果没有`type`字段，`index.js`就会被解释为 CommonJS 模块。

然后，其它项目就可以通过`import`命令就可以加载这个模块。

```js
import { something } from 'A'
```

上面代码中，运行该脚本以后，Node.js 就会到`./node_modules`目录下面，寻找`A`模块，然后根据该模块`package.json`的`main`字段去执行入口文件。

### package.json的exports字段

> main的别名

`exports`字段的别名如果是`.`, 就代表模块的主入口，优先级高于`main`字段，并且可以直接简写成`exports`字段

```json
{
  "exports": {
    ".": "./main.js"
  }
  // 等同于
  "exports": "./main.js"
}
```

> 子目录别名

`package.json`文件的`exports`字段可以指定脚本或子目录的别名

```json
// ./node_modules/es-module-package/package.json
{
  "exports": {
    "./submodule": "./src/submodule.js"
  }
}
```

上面的代码指定`src/submodule.js`别名为`submodule`，然后就可以从别名加载这个文件。

```json
import submodule from 'es-module-package/submodule';
// 加载 ./node_modules/es-module-package/src/submodule.js
```

## 参考资料

* [ES modules: A cartoon deep-dive](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)
* [ES Module的加载实现](https://wangdoc.com/es6/module-loader.html)
* [CommonJS规范](https://javascript.ruanyifeng.com/nodejs/module.html)
* [ES Module integration](https://www.youtube.com/watch?v=qR\_b5gajwug)
