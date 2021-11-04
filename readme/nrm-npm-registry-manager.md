# NRM: npm registry manager

## 1.1 手工切换源

### 1.1.1 查看当前源

```
npm config get registry
```

### 1.1.2 切换淘宝源

```
npm config set registry https://registry.npm.taobao.org
```

## 1.2 NRM 管理源

NRM (npm registry manager)是npm的镜像源管理工具，有时候国外资源太慢，使用这个就可以快速地在 npm 源间切换。

### 1.2.1 安装 nrm

在命令行执行命令，`npm install -g nrm`，全局安装nrm。

### 1.2.2 使用 nrm

执行命令 `nrm ls` 查看可选的源。 其中，带\*的是当前使用的源，上面的输出表明当前源是官方源。

### 1.2.3 切换 nrm

如果要切换到taobao源，执行命令`nrm use taobao`。

### 1.2.4 测试速度

你还可以通过 nrm test 测试相应源的响应时间。

```
nrm test
```
