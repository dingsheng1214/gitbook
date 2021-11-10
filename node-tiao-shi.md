# Node 调试

## 待调试代码

```js
// index.js
import log from '../log.js'
import http from 'http';

const server = http.createServer((req, res) => {
    log.debug(req)
})

server.listen(8080, () => {
    console.log('localhost listening on port: 8080')
})
```

## Chrome 浏览器中调试

### 命令行启动

```js
node --inspect=9229 index.js
```

![image-20211110113443159](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211110113443159.png)

`ws://127.0.0.1:9229/2e1e7514-407f-4b6d-b97c-009b6fb013f4`这个不是提供给我们在Chrome浏览器中访问的地址，而是Node.js与外部(Chrome、VSCode、WebStorm等)进行`websocket`通信的地址，从而将调试信息展示在外部。

浏览器访问`http://localhost:8080/` 可以在控制台上看到一个绿色的 Node 图标

![image-20211110114643922](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211110114643922.png)

点击弹出调试框

![image-20211110114748741](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211110114748741.png)

后面就和前端调试没啥区别了

你也可以通过访问`chrome://inspect/#devices`查看Chrome监听的所有`inspect`，然后点击进入调试

![image-20211110115013493](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211110115013493.png)

## WebStorm 中调试

在webstorm中有2种方式开启调试

* 在命令行中启动

![image-20211110115518001](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211110115518001.png)

*   页面启动

    ![image-20211110115701534](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211110115701534.png)

    ![image-20211110115749904](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211110115749904.png)

    ![image-20211110120126657](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211110120126657.png)

​

无论以上哪种方式，点击`websocket`通信地址后，都会进入webstorm的debug模式，

![image-20211110120453960](https://raw.githubusercontent.com/shengding1214/imageCloud/master/image-20211110120453960.png)
