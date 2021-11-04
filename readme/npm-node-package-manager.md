# NPM: Node Package Manager

## 1.1 全局安装package

```
npm install 包名 --global (-g)
npm uninstall  --global
```

**全局安装包的目录**

*   Mac

    ```
    /Users/你的用户名/.nvm/versions/node/nvm各个版本/bin/
    ```
*   Windows

    ```
    C:\Users\你的用户名\AppData\Roaming\npm\node_modules
    ```

## 1.2 本地安装package

```
npm install 包名
```

## 1.3 package.json初始化

```
npm init -y
```

## 1.4 使用package.json

```
# 生产环境依赖
npm install underscore --save
# 开发环境依赖
npm install lodash --save-dev
# 安装全部依赖
npm install
# 卸载包
npm uninstall underscore --save
# 查看依赖树
npm list | grep underscore
```

## 1.5 安装指定版本的包

```
# 查看包信息
npm info 包名 
# 查看包的版本号
npm view 包名 versions
# 安装包的指定版本号
# 	major: 1 主版本号
# 	Minor: 8 次版本号
# 	Patch: 0 补丁号
npm install underscore@1.8.0
```

## 1.6 更新本地安装的包

```
# 查看包信息
npm info commander
```

![image-20211103161100089](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211103161100089.png)

```
# 查看包版本
npm view commander versions
```

![image-20211103161527430](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211103161527430.png)

```
# 查看过期的依赖包
npm outdated
```

* "commander": "`^`3.0.2" 表示锁定主版本号，其余版本号用最新的
  * ![image-20211103162013464](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211103162013464.png)
* "commander": "`~`6.2.0" 表示锁定次版本号，其余版本号用最新的
  * ![image-20211103162356052](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211103162356052.png)
* ​ "commander": "`*`" 表示使用最新的版本号
  * ![image-20211103162533980](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211103162533980.png)

```
# 更新包版本
npm update
```

## 1.7 清除缓存

```
npm cache clean --force
```

## 1.8 上传自己的包

#### 1.8.1 编写模块

保存为index.js

```js
exports.sayHello = function(){ 
  return 'Hello World'; 
}
```

#### 1.8.2 初始化包描述文件

```
npm init package.json
```

```json
{ 
  "name": "gp19-npm", 
  "version": "1.0.1", 
  "description": "gp19 self module", 
  "main": "index.js",
  "scripts": { 
    "test": "make test" 
  }, 
  "repository": { 
    "type": "Git", 
    "url": "git+https://github.com/lurongtao/gp19-npm.git" 
  }, 
  "keywords": [ 
    "demo" 
  ], 
  "author": "Felixlu", 
  "license": "ISC", 
  "bugs": { 
    "url": "https://github.com/lurongtao/gp19-npm/issues" 
  }, 
  "homepage": "https://github.com/lurongtao/gp19-npm#readme", 
}
```

#### 1.8.3 注册npm仓库账号

* https://www.npmjs.com 网站注册账号
* 命令行 `npm adduser`

#### 1.8.4 上传包

```
# 先登录
npm login
# 再上传
npm publish
```

坑：403 Forbidden

```
# 查看npm源：
npm config get registry
# 切换npm源方法一：
npm config set registry http://registry.npmjs.org
# 切换npm源方法二：
nrm use npm
```

#### 1.8.5 安装包

```
npm install gp19-npm
```

#### 1.8.6 卸载包

```
# 查看当前项目引用了哪些包 ：
npm ls
# 卸载包：
npm unpublish --force
```

#### 1.8.7 使用引入包

```js
var hello = require('gp19-npm')
hello.sayHello()
```

## 1.9 npm 脚本

Node 开发离不开 npm，而脚本功能是 npm 最强大、最常用的功能之一。

**1.9.1什么是 npm 脚本**

npm 允许在 package.json 文件里面，使用 scripts 字段定义脚本命令。

```json
{
  // ...
  "scripts": {
    "build": "node build.js"
  }
}
```

**1.9.2执行顺序**

如果 npm 脚本里面需要执行多个任务，那么需要明确它们的执行顺序。

```js
// script1.js
var x = 0
console.log(x)
```

```js
// script2.js
var y = 0
console.log(y)
"scripts": {
  "script1": "node script1.js",
  "script2": "node script2.js"
}
```

如果是`并行`执行（即同时的平行执行），可以使用 `&` 符号。

```
npm run script1 & npm run script2
```

如果是`串行`执行（即只有前一个任务成功，才执行下一个任务），可以使用 `&&` 符号。

```
npm run script1 && npm run script2
```

**1.9.3简写形式**

常用的 npm 脚本简写形式。

```
npm start 
# 等同于
npm run start
```

**1.9.4变量**

npm 脚本有一个非常强大的功能，就是可以使用 npm 的内部变量。

首先，通过 `npm_package_` 前缀，npm 脚本可以拿到 package.json 里面的字段。比如，下面是一个 package.json。

> 注意：一定要在 npm 脚本中运行（如：npm run view）才可以，直接在命令行中运行JS（如：node view.js）是拿不到值的

```json
{
  "name": "foo", 
  "version": "1.2.5",
  "scripts": {
    "view": "node view.js"
  }
}
```

那么，变量 npm\_package\_name 返回 foo，变量 npm\_package\_version 返回 1.2.5。

```js
// view.js
console.log(process.env.npm_package_name); // foo
console.log(process.env.npm_package_version); // 1.2.5
```

上面代码中，我们通过环境变量 `process.env` 对象，拿到 package.json 的字段值。如果是 Bash 脚本，可以用$npm\_package\_name 和 $npm\_package\_version 取到这两个值。

npm_package_前缀也支持嵌套的package.json字段。

```json
"repository": {
  "type": "git",
  "url": "xxx"
},
scripts: {
  "view": "echo $npm_package_repository_type"
}
```

上面代码中，repository 字段的 type 属性，可以通过 npm\_package\_repository\_type 取到。

下面是另外一个例子。

```json
"scripts": {
  "install": "foo.js"
}
```

上面代码中，npm\_package\_scripts\_install 变量的值等于 foo.js。

然后，npm 脚本还可以通过 npm_config_ 前缀，拿到 npm 的配置变量，即 npm config get xxx 命令返回的值。比如，当前模块的发行标签，可以通过 npm\_config\_tag 取到。

```json
"view": "echo $npm_config_tag",
```

注意，package.json 里面的 config 对象，可以被环境变量覆盖。

```json
{ 
  "name" : "foo",
  "config" : { "port" : "8080" },
  "scripts" : { "start" : "node server.js" }
}
```

上面代码中，npm\_package\_config\_port 变量返回的是 8080。这个值可以用下面的方法覆盖。

```
npm config set foo:port 80
```

最后，env命令可以列出所有环境变量。

"env": "env"

## 1.10 npm 安装 git 上发布的包

```
# 这样适合安装公司内部的git服务器上的项目
npm install git+https://git@github.com:lurongtao/gp-project.git

# 或者以ssh的方式
npm install git+ssh://git@github.com:lurongtao/gp-project.git
```

## 1.11 cross-env 使用

### 1.11.1 cross-env是什么

运行跨平台设置和使用环境变量的脚本

### 1.11.2 出现原因

当您使用 `NODE_ENV=production` 来设置环境变量时，大多数 Windows 命令提示将会阻塞(报错)。（异常是Windows上的Bash，它使用本机Bash。）换言之，Windows 不支持 NODE\_ENV=production 的设置方式。

### 1.11.3 解决

cross-env 使得您可以使用单个命令，而不必担心为平台正确设置或使用环境变量。这个迷你的包(cross-env)能够提供一个设置环境变量的 scripts，让你能够以 Unix 方式设置环境变量，然后在 Windows 上也能兼容运行。

### 1.11.4 安装

```
npm install --save-dev cross-env
```

### 1.11.5 使用

```json
{
  "scripts": {
    "build": "cross-env NODE_ENV=production webpack --config build/webpack.config.js"
  }
}
```

NODE\_ENV环境变量将由 cross-env 设置 打印 process.env.NODE\_ENV === 'production'
