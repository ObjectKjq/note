npm和yarm的镜像源管理[npm、yarn的安装和设置淘宝镜像源_yarn设置淘宝镜像-CSDN博客](https://blog.csdn.net/cn_ljr/article/details/126319130)

cmd窗口

1. dir:列出当前目录的所有文件
2. cd 目录名 进入到指定的目录
3.   .   表示当前目录
4.   ..  表示上级目录
5. md 创建文件夹
6. rd    删除文件夹
7. 直接输入名字   打开文件
8. 当没有找到文件时，就会上环境变量中去找

cls清空终端内容，

​	node -v 查看node的版本

shift+右键，打开powershell

## Node.js基础功能

### Buffer

```js
let buf = Buffer.alloc(10)
//可能有旧的内存没有清除
let buf = Buffer.allocUnsafe(100)
//字符串转buffer
let buf = Buffer.from("hello")
//数组转buffer
let buf = Buffer.from([105,108,111,118,101,121,111,117])
```

```js
let buf = Buffer.from([105,108,111,118,101,121,111,117])
let buf_1 = buf.toString();//utf-8

let buf = Buffer.from("hello")
//获取字符串第一个字符的utf-8数字
console.log(buf[0].toString(2))//toString输出2进制
buf[0] = 95//修改

//溢出，最大数字255，大于8位的时候会舍弃后面的位置数
//中文每个字节站三个字符
```



### fs文件处理系统

fs模块是node.js官方提供的，用来操作文件模块，提供了方法和属性来满足用户的需求



#### 异步读写数据的方法

读内容

![](.\图片\Snipaste_2022-04-30_12-27-45.png)

```javascript
const fs = require('fs')//导入文件

fs.readFile('../texxt/text01.txt', 'utf8', function (err, dataStr) {
    //如果读取成功则err是null,dataStr是buffer数据
    //如果读取失败err是错误对象,dataStr是undefined
    
    //打印失败的结果
    console.log(err);
    console.log('=========');
    //打印成功的结果
    console.log(dataStr.toString());
});//调用方法读取文件
```

写入内容

![](.\图片\Snipaste_2022-04-30_12-43-20.png)

```javascript
const fs = require('fs')//导入文件

fs.writeFile("../texxt/text01.txt", "abcd", function (err) {
    console.log(err);//如果文件写入成功err的值为null，如果失败打印的是对象
});
```

#### 同步读写方法

```js
fs.writeFileSync()
//data也是buffer数据
let data = fs.readFileSync('./text.txt')
```

#### 追加

```js
appendFile/appendFileSync
```

#### 流式读写操作

```js
//打开一个通道，写入频繁
let ws = fs.createWriteStream('/text.txt');
ws.write('text1');
ws.write('text2');
ws.write('text3');
ws.write('text4');
ws.close();
//流式读取
const rs = fs.createReadStream('./text.txt')
//绑定事件,每当读取一块就会调用这个函数，每次读取64KB
rs.on('data',chunk => {
    //chunk是buffer
})
//end可选事件,读取完成后调用
re.on('end',()=>{
    
})
```

#### fs文件移动与重命名，删除

```js
//重命名
fs.rename('./text.txt', './论语.txt', err=>{
    //error为空操作成功
})
//移动
fs.rename('./data.txt', '../资料/data.txt', err=>{
    //error为空操作成功
})
//删除，同步方法unlinkSyc和rmSync
fs.unlink('./text.txt', err=>{
    
})
fs.rm(('./text.txt', err=>{
    
})
```

#### 文件夹的操作

```js
//创建文件夹
fs.mkdir('./html',err=>{
    
})
//多级目录
fs.mkdir('./a/b/c',{recursive:true},err=>{
    
})
//读取文件夹,资源的名称内容
fs.readdir('./资源',(err,data)=>{
    
})
//删除文件夹
fs.rmdir('./html', err=>{
    
})
//删除多级目录
fs.rmdir('./a', {recursive:true}, err=>{
    
})
fs.rm('./a', {recursive:true}, err=>{
    
})
```

#### 查看文件状态信息

```js
fs.stat('./text.txt', (err, data)=>{
    console.log(data)
    //判断资源是不是文件
    console.log(data.isFile())
    //判断资源是不是文件夹
    console.log(data.isDirectory())
})
```

#### 相对路径问题

```javascript
//js相对目录参照物：命令行的工作目录，随着命令行工作目录的变化而变化
__dirname	//全局变量，当前文件所在目录的绝对路径
```

### path路径模块

path模块是node官方提供的，用来处理路径的模块，它提供了一系列的方法和属性，用来满足用户的需求。path.join()方法，用来将多个路径片段拼接成一个完整的字符串

```js
const path = require('path'); 

//resolve拼接规范的绝对路径
console.log(path.resolve(__dirname, 'index.html'));
//sep分隔符,打印当前操作系统的分隔符
console.log(path.sep)
//parse获取路径的详细信息
let str = 'D:\\node\\path\\代码\\path.js';
console.log(path.parse(str))
//获取文件名
console.log(path.basename(str))
//获取文件夹路径
console.log(path.dirname(str))
//获取文件扩展名
console.log(path.extname(str))
```

![](图片\Snipaste_2022-04-30_14-57-52.png)

### http模块

他是node官方提供的，用来创建web服务器的模块，通过http模块提供的http.createService()方法，就能方便的把一台普通的电脑，编程一台web服务器

```javascript
//导入http模块
const http = require('http');
//创建web服务器实例
const server = http.createServer((req, res)=>{
    //获取请求信息
    // console.log(req.url);
    // console.log(req.method);
    // console.log(req.httpVersion);
    // console.log(req.headers);
    //接收请求体数据
    let body = '';
    //绑定数据接收事件
    req.on('data', chunk => {
        body += chunk.toString();
    });
    //绑定数据接收完毕事件
    req.on('end', () => {
        console.log(body);
        //响应
        res.end('Hello World\n');
    });
    
    //设置状态码
    res.statusCode = 200;
    //设置状态码描述
    res.statusMessage="iloveyou"
    //设置详情头
    res.setHeader('Content-Type', 'text/html; charset=utf-8');  
    //设置响应体
    res.write('love')
    res.end('Hello World\n');
});
//启动服务器
server.listen(8080, function () {
    //控制台打印
    console.log('server running at http://127.0.0.1:8080');
});
```

获取query参数

```js
const http = require('http');
const url = require('url');
const server = http.createServer((req, res) => {
    //获取url中query参数和获取路径参数
    const { query, pathname } = url.parse(req.url, true);
    console.log(query.name);
    console.log(pathname);
    
    //第二种获取查询字符串的参数和url
    let url = new URL(req.url, 'http://localhost:3000/');
    //获取query参数
    console.log(url.searchParams.get('name'));
    //获取路径
    console.log(url.pathname);
    res.end('Hello World\n');
});
server.listen(3000, () => {
  console.log('Server running at http://localhost:3000/');
});
```

#### 静态资源服务搭建

```js
const server = http.createServer((req, res) => {
    //获取请求url的路径
    let {pathname} = new URL(request.url, 'http://127.0.0.1');
    //拼接文件路径
    let filePath = __dirname+'/page'+pathname;
    fs.readFile(filePath,(err, data)=>{
        if(err){
            response.statusCode = 500;
            response.end("文件读取失败")
            return;
        }
        response.end(data);
    })
});
```



## Node.js模块化

```js
const tiemo = require('./me.js')
timeo();
```

```js
function tiemo(){
    console.log('贴膜..')
}
module.exports = tiemo;
```

### 暴露数据

可以暴露任何数据exports = module.exports = {}

```js
//暴露多个数据
module.exports = {
    tiemo:tiemo,
    niejiao:niejiao
}
//分别暴露
exports.niejiao = niejiao;
exports.tiemo = tiemo;
```

### 导入模块

```js
//相对路径不会受到文件的影响的和fs有点不同，js和json后缀可以省略
const tiemo = require('./me')

//导入文件夹，他回去判断package.json文件里面配置main对应的文件有没有，如果有导入
//如果package.json不存在或main配置不存在，他会尝试导入文件夹下index.js和index.json
const tiemo = require('./module')
```

### 模块化管理

***模块化的的分类***

内置模块	内置模块由node.js官方提供的，如fs，path，http

自定义模块	每个创建的js文件，都是自定义模块

第三方模块	并非官方提供的模块，也不是用户创建的自定义模块，使用时需要先下载



加载模块

1、使用require('fs');加载内置模块。2、加载自定义模块require('./custom.js')。3、加载第三方模块require('moment');。当加在模块中的代码时，会执行模块中的代码



模块作用域：默认在模块中定义的成员不能在模块外部访问

那么如何在其它模块共享数据那？module就表示当前这个模块，在自定义模块中，可以使用module.exports对象，将模块内的成员共享出去。**外界在使用require()导入自定义模块时，得到的就是module.exports所指向的对象**

```javascript
module.exports.username = 'zhangsan'//共享数据

const m = require('./model');
console.log(m);//m就是module.exports
```

![](.\图片\Snipaste_2022-04-30_17-55-22.png)

![](.\图片\Snipaste_2022-04-30_18-07-07.png)

![](.\图片\Snipaste_2022-04-30_18-09-01.png)

## npm

从哪里下载包那，可以从http://www.npmjs.com,中搜索。从http://registry.npmjs.org下载。在安装node时就自动下载了npm工具

![](.\图片\Snipaste_2022-04-30_18-21-26.png)

```javascript
//1，第一位数字，表示大版本，从底层重构。2、第二位数字，表示功能的更新。3、第三位数字，表示修复一些bug
npm install 包的名称@2.22.2//就能下载一个包，可以加@指定版本
```

![](.\图片\Snipaste_2022-04-30_18-34-12.png)

![](.\图片\Snipaste_2022-04-30_18-40-22.png)

### 包管理配置文件

共享git时只共享源代码，不共享包。创建package.json文件来记录我们的项目都是用到了那些包

快速创建包管理配置文件，npm中使用一个命令`npm init -y`。只能在英文目录下运行，当安装包时，就会自动的把包的信息放到package文件中



当我们进入空的项目目录是，先运行npm init -y，创建包管理工具，让后再安装包。当我们从github上拉取代码时，通过package.json批量安装项目所用到的包，运行npm install一次性安装所有的包



通过npm uninstall moment 命令来卸载包

![](.\图片\Snipaste_2022-04-30_19-01-31.png)

npm install 包的名称 -D表示只在开发中使用，部署到项目不用

--save-div 可以简写成 -D

**解决下包速度慢的问题**

使用淘宝npm镜像服务器 ，切换下包的服务器地址

1、查看当前下包的镜像地址

npm config get registry

2、设置淘宝镜像

npm config set registry=https://registry.npm.taobao.org/

![](.\图片\Snipaste_2022-04-30_19-17-47.png)

项目包和全局包

项目包：node_modules中都是项目包

​	开发依赖包：被记录到devDependencies中

​	核心依赖包：被记录dependencies

全局包：在执行npm install命令时，如果提供了 -g参数，则会把包安装到全局包。C:\Users\86175\AppData\Roaming\npm。卸载npm uninstall 包名 -g。只有工具才有安装到全局的必要

![](.\图片\Snipaste_2022-04-30_19-25-32.png)

### 发布自己的包

![](.\图片\Snipaste_2022-04-30_20-09-08.png)

![](.\图片\Snipaste_2022-04-30_20-15-35.png)

![](.\图片\Snipaste_2022-04-30_20-17-17.png)

![](.\图片\Snipaste_2022-04-30_20-27-48.png)

![](.\图片\Snipaste_2022-04-30_20-29-37.png)

````md
## 安装
```
npm install itheima-tools
```

## 导入
```js
const itheima = require("itheima-tools")
```

## 格式化时间
```js
//调用dateFormat对时间的格式化
const dtStr = itheima.dateFormat(new Date())
console.log(dtStr)
```

## 转译HTML中的特殊字符
```js
//带转换的HTML字符串
const htmlStr = '<h1 title='abc'></h1>'
const str = itheima.htmlEscape(htmlStr)
```
````

发布包

1、先注册npm账号。2、登陆账号。3、先切换到npm的官方服务器

npm login实现登录。先切换到包的根目录，使用npm publish命令，发布包。4、npm unpublish 包名 --force命令，即可从npm删除已发布的包

![](.\图片\Snipaste_2022-04-30_20-46-16.png)

node的加载机制

![](.\图片\Snipaste_2022-04-30_20-51-30.png)

![](.\图片\Snipaste_2022-04-30_20-56-13.png)

![](.\图片\Snipaste_2022-04-30_20-57-14.png)

## express讲解

Express是基于Node.js平台，快速，开放，筛选的web开发框架。是专门创建web服务器的，类似于node内置的http模块，express是根据http进一步封装

![](.\图片\Snipaste_2022-04-30_21-06-38.png)

```shell
npm i express@4.17.1#命令安装express
```

```javascript
//导入
const express = require('express');
//创建web服务
const app = express();
//调用app.listen(端口, 启动成功后的回调函数), 启动服务
app.listen(8080, function () {
    console.log("http://127.0.0.1:8080");
});
//处理get请求1、客户端请求的url地址2、请求对应的处理函数
app.get('/get', function(req,res){
   	res.send('<h1>get请求'+req.query.name+req.query.age+'</h1>');//输出参数的值
});
//处理post请求1、客户端请求的url地址2、请求对应的处理函数
app.post('/post', function(req,res){
    res.send('<h1>post请求</h1>');
});
```

### 创建静态资源的服务器，静态资源中间件

静态资源托管

![](图片\Snipaste_2022-04-30_21-46-44.png)

```javascript
app.use(express.static('./clock'))//把clock文件夹下的代码开放，此方法可以创建多个
```

![](图片\Snipaste_2022-04-30_21-53-42.png)

### nodemon

![](图片\Snipaste_2022-04-30_21-56-36.png)

```shell
#全局安装到这里C:\Users\86175\AppData\Roaming\npm\node_modules
npm install -g nodemon
```

nodemon的使用。

![](图片\Snipaste_2022-04-30_22-00-46.png)

使用nodemon会检查代码是否发生变化，如果变化就重新启动项目

### Express路由

路由表示映射关系，就比如一个按键表示一个服务一样

![](图片\Snipaste_2022-04-30_22-08-04.png)

![](图片\Snipaste_2022-04-30_22-08-46.png)

![](图片\Snipaste_2022-04-30_22-11-39.png)

### 获取请求参数

```js
app.get('/home/:id', (req, res) => {
    //获取路径
    console.log(req.path)
    //获取请求query参数
    console.log(req.query)
    //获取请求头
    console.log(req.get('User-Agent'))
    //获取路径中的参数parmas
    console.log(req.params.id)
  	res.send('Hello World!')
})
```

### 获取请求体中的参数

```sh
# 安装插件
npm i body-parser
```

```js
const express require('express')
const bodyParser require('body-parser')
const app = express();
//解析json格式请求体的中间件
const jsonParser = bodyParser.json();
//解析querystring格式的中间件?name="sdf"&age=12
const urlencodeParser = bodyParser.urlencoded({extended:false})
//获取请求体数据body
app.post('/login',urlencodeParser , (req, res) => {
    console.log(req.body)
    res.send('Hello World!')
})
```

### 给浏览器设置响应

```js
app.get('/index', (req, res) => {
    //设置状态码
    res.status(200)
    //设置头
    res.set('Content-Type', 'text/plain')
    //设置重定向
    res.redirect('/home/123')
    //下载响应,告诉客户端下载指定文件
    res.download(__dirname + '/package.json');
    //响应json数据
    res.json({name: 'zhangsan', age: 20})
    //响应html数据
    res.sendFile(__dirname + '/index.html')
    //设置体
    res.send('Hello World!')
})
```

### 模块化路由

![](图片\Snipaste_2022-04-30_22-16-16.png)

```javascript
//导入
const express = require('express');
//创建web服务
const router = express.Router();
//挂载路由
router.get('/name', function (req, res) {
    res.send('<h1>name</h1>');
});

//到出路由对象
module.exports = router;
```

```javascript
//导入
const express = require('express');
//导入路由
const exportsLuYou = require('./ExpressLuYou');
//创建web服务
const app = express();
//注册路由
app.use('/api', exportsLuYou);//也可以添加访问前缀
//调用app.listen(端口, 启动成功后的回调函数), 启动服务
app.listen(8080, function () {
    console.log("hello");
});
```

注意app.use()函数的作用就是来注册全局中间件

### Express中间件

类似于java的过滤器

中间件的概念

![](.\图片\Snipaste_2022-04-30_22-55-51.png)

Express中间件的格式

![](.\图片\Snipaste_2022-04-30_22-57-51.png)

next函数是实现多个中间件连续调用的关键，它表示把流转关系交给下一个中间件或路由



#### 定义全局中间件函数

```javascript
//定义一个全局中间件函数
const mw = function(req, res, next){
    console.log("日志");
    //把流转关系，转交给下一个中间件或路由
    next();
};
//全局中间件函数
app.use(mw);//当每次访问服务器时都会先经过这个函数的处理
```

**中间件的访问顺序，可以使用app.use()连续定义多个全集中间件，客户端到达服务后，会按照中间件定义的先后顺序依次进行调用**

#### 路由中间件

```javascript
//定义一个局部中间件函数
const mw1 = function(req, res, next){
    console.log("判断用户有没有登录");
    next();
}
//定义第二个中间件
const mw2 = function(req, res, next){
    console.log("判断有没有权限");
    next();
}
//只会在当前路由生效,调用顺序是从前向后的
app.get('/login', mw1, mw2, function(req, res){
    res.send('hello');
});
```

**中间件的注意事项**

![](.\图片\Snipaste_2022-05-01_10-01-30.png)

***Express五大中间件***

1. 应用级别的中间件

   只要是绑定到app上的实例都可以称为应用级别的中间键

2. 路由级别的中间件

   绑定到router上的实例称为路由级别的中间件

3. 错误级别的中间件

   错误级别的中间件的作用，捕获整个项目发生的异常错误，从而防止项目异常发生崩溃。

   function中必须有4个参数，分别是(err,req,res,next)

   错误中间件必须定义在所有路由之后

   ```javascript
   //定义第二个中间件
   const mw2 = function(req, res, next){
       console.log("hello2");
       next();
   }
   //只会在当前路由生效
   app.get('/login', mw1, mw2, function(req, res){
       throw new Error('发生错误');
       res.send('<div>hello</div>');
   });
   //定义一个全局的错误
   app.use(function(err, req, res, next){
       console.log(err.message);
       res.send("hello");
   });
   ```

4. Express内置的中间件

   ![](.\图片\Snipaste_2022-05-01_10-17-11.png)

   ```javascript
   //来解析json
   app.use(express.json());
   //来解析url-encoded数据
   app.use(express.urlencoded({ extended: false }))
   ```

5. 第三方的中间件

   ![](.\图片\Snipaste_2022-05-01_10-23-28.png)

   以上中间件，是解析表单中的数据

### 防盗链的实现

```js
//生命中间件
app.use((req, res, next)=>{
    //获取referer
    let referer = req.get('referer')
    if(referer){
        let url = new URL(referer);
        let hostname = url.hostname;
        if(hostname !== 'localhost'){
            //响应404
            return;
        }
    }
})
```

### 使用Express写接口

```javascript
const express = require('express');
const interface01 = require('./interfaceRouter');
const app = express();
app.use('/api', interface01);

app.listen(8080, function(){
    console.log('http://127.0.0.1:8080');
});
```



```javascript
const express = require('express');
const router = express.Router();

//编写get接口
router.get('/get', function(req, res){
    //获取响应数据
    const query = req.query;
    //响应数据
    res.send({
        status: 0,          //0表示成功
        msg: 'get请求成功',  //状态描述
        data: query         //数据
    });
});

//编写post接口
router.post('/post', function(req, res){
    //获取响应数据
    const query = req.body;
    //响应数据
    res.send({
        status: 0,          //0表示成功
        msg: 'get请求成功',  //状态描述
        data: query         //数据
    });
});

module.exports = router;
```

### 解决接口跨域问题

![](.\图片\Snipaste_2022-05-01_10-58-10.png)

![](.\图片\Snipaste_2022-05-01_10-59-10.png)

![](.\图片\Snipaste_2022-05-01_11-01-03.png)

![](.\图片\Snipaste_2022-05-01_11-02-38.png)



![](.\图片\Snipaste_2022-05-01_11-04-23.png)



![](.\图片\Snipaste_2022-05-01_11-06-35.png)



![](.\图片\Snipaste_2022-05-01_11-07-32.png)

**cors请求的分类**

![](.\图片\Snipaste_2022-05-01_11-08-41.png)

![](.\图片\Snipaste_2022-05-01_11-09-36.png)

![](.\图片\Snipaste_2022-05-01_11-12-04.png)

### 模板引擎EJS

```shell
#安装
npm i ejs
```

```js
const ejs = require('ejs');

let china = '中国';
let weather = "今天天气不错"

let str = fs.readFileSync('./tex.html');
let result = ejs.render(str, {china: china, weather:weather})
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h2>我爱你<%= china %></h2>
    <h2><%= weather %></h2>
</body>
</html>
```

#### 列表渲染

```js
const ejs = require('ejs');
const xiyou = ['a', 'b', 'c', 'd'];
let str = fs.readFileSync('./tex.html');
let result = ejs.render(str, {xiyou})
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <ul>
        <% xiyou.forEach(item => { %>
        <li><%= item %></li>
        <% }) %>
    </ul>
</body>
</html>
```

#### 条件渲染

```js
const ejs = require('ejs');
let isLogin = true;
let str = fs.readFileSync('./tex.html');
let result = ejs.render(str, {isLogin})
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <% if(isLogin){ %>
    <span>欢迎回来</span>
    <% }else{ %>
    <span>你好</span>
    <% } %>
</body>
</html>
```

#### express中使用模板引擎

```js
const express = require('express');
const path = require('path')

const app = express();

//设置模板引擎
app.set('view engine', 'ejs');
//设置模板文件夹存放位置,具有模板语法内容的文件
app.set('views', path.resolve(__dirname, './views'));

app.listen(8080, function () {
    console.log("http://127.0.0.1:8080");
});
app.get('/get', function(req,res){
   	let isDelete = true;
    res.render('home', {isDelete})
});
```

```html
<h2>
    <%= isDelete %>
</h2>
```

### 初始化项目

```sh
#全局安装
npm i -g express-generator
# 查看命令
express -v
# 创建express项目，映入-e表示添加ejs，放到project文件夹下
express -e project
# 安装依赖
npm i
# 启动
npm start
```

### 文件上传

```sh
# 文件上传包
npm i formidable
```

```js
const formidable = require('formidable');

//创建表单对象
const form = formidable({
    multiples:true,
    //是指上传文件目录
    uploadDir:__dirname+'/../public/images',
    //保持文件后缀
    keepExtensions:true
});
//解析请求报文，fields只存储基本字段，files存储上传的文件
form.parse(req, (err, fields, files)=>{
    if(err){
        next(err);
        return;
    }
    //获取文件路径
    let url = '/images/'+files.portrait.newFilename;
    //响应json数据
    res.json({fields,files});
})
```

## Mongodb数据库

- 数据库：每个数据库服务可以创建多个数据库
- 集合：类似于js中的list列表
- 文档：类似于js中的对象

```json
{//数据库
    "accounts":[//集合，存储同类型的文档
       {//文档
            id: 1,
            "title": "工资",
            "time": "2019-01-01",
            "type": "1",
            "account": "10000",
            "remarks": "工资收入"
        },
        { 
            id: 2,
            "title": "奖金",
            "time": "2019-01-01",
            "type": "2",
            "account": "5000",
            "remarks": "奖金收入"
        },
    ]
}
```

### 安装和使用

```shell
#启动mongodb
mongod --dbpath G:\mongodb7\mongodb-win32-x86_64-windows-7.0.7\data\db --logpath G:\mongodb7\mongodb-win32-x86_64-windows-7.0.7\log\mongodb.log
#shell连接数据库
mongosh
```

### 字段类型

- String-字符串
- Number-数字
- Boolean-布尔
- Array-数组
- Data-日期
- Buffer-Buffer对象
- Mixed-任意类型
- ObjectId-对象ID
- Decimal128-高精度数字

### 数据库操作

```shell
#选择和创建数据库的语法格式,
use articledb
#查看有权限查看的所有的数据库命令，我们创建的数据库看不到，如果想看到可以添加一些数据
show dbs
show databases
#查看当前正在使用的数据库命令
db
# MongoDB 删除数据库的语法格式如下：
db.dropDatabase()
```

### 集合的相关操作

```shell
# 在 articledb 数据库中创建 review 集合：
db.createCollection("review")
# 创建固定集合 mycol，整个集合空间大小 6142800 B, 文档最大个数为 10000 个。
db.createCollection("mycol", { capped: true, autoIndexId: true, size: 6142800, max: 10000 } )
# 如果要查看已有集合
show collections
show tables

#中使用 drop() 方法来删除名字为review的集合。
db.review.drop()
```

### 文档的操作

```shell
# MongoDB 使用insertOne、insertMany(插入多个)或bulkWrite，语法如下：项review添加数据
db.review.insertOne({
    title: 'MongoDB 教程', 
    description: 'MongoDB 是一个 Nosql 数据库',
    by: '菜鸟教程',
    url: 'http://www.runoob.com',
    tags: ['mongodb', 'database', 'NoSQL'],
    likes: 100
})

# MongoDB 使用updateOne、updateMany或bulkWrite。参数一查询条件，第二个参数是更新的数据，第三个参数是配置项
db.review.updateOne({'title':'MongoDB 教程'},{$set:{'title':'MongoDB'}})

# MongoDB remove() 函数是用来移除集合中的数据。删除review里的title=MongoDB第一次查询发现的，如果justOne: false删除所有匹配的记录
db.review.remove(
   {'title':'MongoDB'},
   {
     justOne: true
   }
)

# find() 方法以非结构化的方式来显示所有文档。pretty()格式化显示，可以写以逗号隔开and和or
# or形式
{
    $or: [
     	{key1: value1}, {key2:value2}
    ]
}
# 1和true相同，表示查出该字段
db.review.find({'title':'MongoDB'},{description:1}).pretty()

# MongoDB中条件操作符有：(>) 大于 - $gt、(<) 小于 - $lt、(>=) 大于等于 - $gte、(<= ) 小于等于 - $lte
#如果你想获取 "col" 集合中 "likes" 大于 100 的数据，你可以使用以下命令：
# 也可以根据类型查询数据
db.col.find({likes : {$gt : 100}})
```

```shell
# limit()方法指定读取记录的条数，以下是读取两条记录
db.col.find({},{"title":1,_id:0}).limit(2)
# skip()方法来跳过指定数量的数据，跳过第1条，查询1条记录
db.col.find({},{"title":1,_id:0}).limit(1).skip(1)
```

```shell
#sort() 方法对数据进行排序，1表示升序，-1表示降序。以下表示按照likes降序
db.col.find({},{"title":1,_id:0}).sort({"likes":-1})
```

### 索引和聚合

```shell
# 1 为指定按升序创建索引，如果你想按降序来创建索引指定为 -1
db.col.createIndex({"title":1})

# 计算每个作者所写的文章数，使用aggregate()计算结果如下：
db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : 1}}}])

```

### node中使用mongodb

```sh
# 安装连接包
npm i mongoose
```

```js
const mongoose = require('mongoose')
//连接mongodb服务
mongoose.connect('mongodb://localhost:27017/bilibili')
//设置连接成功回调
mongoose.connection.once('open',()=>{
    console.log("连接成功")
    //一般连接完成后启动http服务
    //app.listen(8080);
})
//设置连接失败回调
mongoose.connection.on('error',()=>{
    console.log("连接失败")
})
//设置连接关闭回调
mongoose.connection.on('close',()=>{
    console.log("关闭连接")
})
```

**插入文档**

```js
mongoose.connection.once('open',()=>{
    //设置集合中文档的属性以及属性值的类型
    let bookSchema = new mongoose.Schema({
        //字段校验
        name:{
        	type: String,
            //设置必填项
            required: true,
            //设置默认值
            default: '匿名',
            //设置的值必须是数组中的
            enum:['男','女'],
            //唯一值
            unique:true
        },
        author:String,
        price:Number
    });
    //创建模型对象，对文档操作的封装对象
    let BookModel = mongoose.model('books', bookSchema);
    //新增
    BookModel.create({
        name:"西游记",
        author:"吴承恩",
        price:18.2
    }).then((data)=>{
        console.log(data);
    })
    //关闭连接
    mongoose.disconnect();
})
```

**删除文档**

```js
//删除一条记录
mongoose.connection.once('open',()=>{
    let bookSchema = new mongoose.Schema({
        name:String,
        author:String,
        price:Number
    });
    let BookModel = mongoose.model('books', bookSchema);
    //删除单条
    BookModel.deleteOne({
        _id: '867dsf67d6f9d8sf7df6s89'
    }).then((data)=>{
        console.log(data);
    })
    //删除多条
     BookModel.deleteMany({
        is_host:false
    }).then((data)=>{
        console.log(data);
    })
    //关闭连接
    mongoose.disconnect();
})
```

**更新文档**

```js
//更新记录
mongoose.connection.once('open',()=>{
    let bookSchema = new mongoose.Schema({
        name:String,
        author:String,
        price:Number
    });
    let BookModel = mongoose.model('books', bookSchema);
    //更新单条
    BookModel.updateOne({
        name:'红楼梦'
    },{
        price:9.9
    }).then((data)=>{
        console.log(data);
    })
    //更新多条
     BookModel.updateMany({
        author:'玉华'
    },{
         is_host:true
     }).then((data)=>{
        console.log(data);
    })
    //关闭连接
    mongoose.disconnect();
})
```

**读取文档**

```js
//读取记录
mongoose.connection.once('open',()=>{
    let bookSchema = new mongoose.Schema({
        name:String,
        author:String,
        price:Number
    });
    let BookModel = mongoose.model('books', bookSchema);
    //读取单条
    BookModel.findOne({name:'狂飙'}).then((data)=>{
        console.log(data);
    })
    //根据id获取文档
    BookModel.findById('32423lk34jk3l24j3hg3').then((data)=>{
        console.log(data);
    })
    //查询多条
    BookModel.find({author:'余华'}).then((data)=>{
        console.log(data);
    })
    //读取所有
    BookModel.find()
    //关闭连接
    mongoose.disconnect();
})
```

**设置查询条件**

```js
//设置查询条件
mongoose.connection.once('open',()=>{
    let bookSchema = new mongoose.Schema({
        name:String,
        author:String,
        price:Number
    });
    let BookModel = mongoose.model('books', bookSchema);
    //价格小于20的，$lt,$gt,$lte,$gte,$ne
    BookModel.find({price:{$lt:20}}).then((data)=>{})
    //曹雪芹或余华的书
    BookModel.find($or: [{author:'曹雪芹'},{author:'余华'}]).then((data)=>{});
	//与
	BookModel.find($and: [{author:'曹雪芹'},{author:'余华'}]).then((data)=>{});
	//正则，搜索名称中带三的
	BookModel.find({name:/三/}).then((data)=>{})
    //字段筛选
    BookModel.find().select({_id:0,title:1}).then((data)=>{})
    //数据排序
    BookModel.find().sort({hot:1}).then((data)=>{})
    //数据截取
    BookModel.find().skip(10).limit(10).then((data)=>{})
    //关闭连接
    mongoose.disconnect();
})
```

**模块化**

```js
//这里包裹所有连接数据库的操作
module.exports = function(success, error){
    mongoose.connection.once('open',()=>{
		success();
    })
}
```

```js
const db = require('./db/db');
const BookModel = require('./models/BookModel')

db(()=>{
    //这里写数据库的操作
    console.log('成功');
},()=>{
    console.log('连接失败');
})
```

```js
//抽离模型(表)操作
const mongoose = require('mongoose')
let bookSchema = new mongoose.Schema({
        name:String,
        author:String,
        price:Number
});
let BookModel = mongoose.model('books', bookSchema);
module.exports = BookModel;
```



## Web开发模式

### 服务点渲染的web开发模式

![](.\图片\Snipaste_2022-05-01_11-15-20.png)

![](.\图片\Snipaste_2022-05-01_11-16-16.png)

### 前后端分离的web开发模式

依赖于ajax技术的广泛应用，简而言之，前后端分离的web开发模式就是后端只负责提供API接口，前端使用Ajax调用接口的开发模式。

![](.\图片\Snipaste_2022-05-01_11-21-47.png)

怎样选择开发模式

![](.\图片\Snipaste_2022-05-01_11-23-26.png)

### 前后端的身份认证

![](.\图片\Snipaste_2022-05-01_11-26-42.png)

![](.\图片\Snipaste_2022-05-01_11-27-59.png)

![](.\图片\Snipaste_2022-05-01_11-28-25.png)

### Session认证机制

服务端渲染使用

cookie：Cookie是存储在用户浏览器的不超过4kb的字符串。它由一个名称(name)、一个值(value)和其他几个用于控制Cookie有效期、安全性、适用范围的可选属性组成。

不同域名下的cookie各自独立，每当客户端发送请求，会自动把当前域名下所有未过期的coolie一同发送的服务器。

几大特点：1、自动发送。2、域名独立。3、过期时限。4、4kb的限制

![](.\图片\Snipaste_2022-05-01_11-41-21.png)

cookie不具有安全性

由于Cookie是存储在浏览器中的，而且浏览器也提供了读写cookie的API，因此Cookie很容易被伪造，不具有安全性，因此不建议把隐私数据，通过Cookie发送给浏览器



session是安全的

![](.\图片\Snipaste_2022-05-01_11-48-40.png)

**express使用session**

先安装express-session 		npm install express-session

```javascript
const express = require('express');
//导入session
const session = require('express-session');
const app = express();
//配置session中间件
app.use(session({
    secret: 'keyboard cat', //可以是任意字符
    resave: false,
    saveUninitialized: true
}));
app.post('/user', function(req, res){
    //判断用户登陆的信息是否正确
    if(req.body.username !== 'admin'){
        return res.send({status: 1, msg: '登陆失败'});
    }
    //将用户信息存储到session中
    req.session.username = req.body.username;
    //将用户登陆的状态存到session中
    req.session.islogin = true;

    res.send({status: 0, msg: '登陆成功'});
});
//从session中取数据
app.get('/outuser', function(req, res){
    //判断用户是否登录
    if(!req.session.islogin){
        return res.send({status: 1, msg: '没有登陆'});
    }
    res.send({status: 0, msg: '登陆了'});
});

app.listen(8080, function(){
    console.log('http://127.0.0.1:8080');
});
```

### JWT认证机制

推荐前后端分离使用

JWT的工作原理

![](.\图片\Snipaste_2022-05-01_12-14-41.png)

![](.\图片\Snipaste_2022-05-01_12-16-14.png)

![](.\图片\Snipaste_2022-05-01_12-19-06.png)

**在express使用JWT**

首先安装 npm install jsonwebtoken express-jwt

jsonwebtoken 用于生成jwt的字符串

express-jwt用于将jwt字符串解析还原成json对象

```javascript
const express = require('express');
const app = express();
//导包
const jwt = require('jsonwebtoken');
const expressJWT01 = require('express-jwt');
//定义secret密钥，来加密和解密用户的信息，本质就是一个字符串
const secretKey = 'klasjfzlkf/jkfjzlk/cvmzkljfda';

//加密
app.post('/user', function(req, res){
    //登录后，生成jwt字符串，通过token相应给客户端
    res.send({
        status: 0,
        message: '登陆成功',
        //最后是配置对象，里面定义了30秒过期
        token: jwt.sign({username: 'hello'}, secretKey, {expiresIn: '30s'})
    })
});

//还原
//注册中间件,以/api开头的路径都不需要访问权限
//注意：只要配置成功了express-jwt这个中间件，就可以把解析出类的用户信息，挂载到req.username属性上
app.use(expressJWT01({secret: secretKey}).unless({path: [/^\/api\//]}));

app.get('/admin', function (req, res){
    console.log(req.username);
});



app.listen(8080, function(){
    console.log('http://127.0.0.1:8080');
});
```

![](.\图片\Snipaste_2022-05-01_13-48-43.png)

## 工具

### 生成服务器

根据json文档快速搭建一个restful风格的接口服务

```sh
# 安装工具
npm i -g json-server
# 以json文件所在文件夹作为工作目录
json-server --watch db.json
```

```json
{
    "song":[
        {"id":1,"name":"kjq","singer":"五月天"},
        {"id":2,"name":"kjq","singer":"五月天"}
    ],
    "user":[]
}
```

### session操作

```shell
# 操作session，express-session将session信息存储到内存，connect-mongo存到mongodb里面
npm i express-session connect-mongo
```

### JWT

```sh
# 创建token，检验token
npm i jsonwebtoken
```



## webpack

![](.\图片\Snipaste_2022-05-08_11-27-00.png)

webpack五个核心的概念

![](.\图片\Snipaste_2022-05-08_11-29-39.png)

![](.\图片\Snipaste_2022-05-08_11-31-50.png)

