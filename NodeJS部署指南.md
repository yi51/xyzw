# 使用Node.js启动静态文件服务器指南

当前系统尚未安装Node.js，但您可以按照以下步骤安装并使用Node.js来部署此项目。

## 一、安装Node.js

1. 访问Node.js官网：https://nodejs.org/zh-cn
2. 下载并安装**LTS版本**（长期支持版，更稳定）
3. 安装完成后，打开命令提示符，验证安装是否成功：
   ```
   node -v  # 查看Node.js版本
   npm -v   # 查看npm版本
   ```
   如果显示版本号，则表示安装成功。

## 二、使用Node.js启动静态文件服务器的方法

### 方法一：使用http-server（最简单的方法）

1. 全局安装http-server包：
   ```
   npm install -g http-server
   ```

2. 进入项目的dist目录：
   ```
   cd d:\New\yinyong\qq数据\2512151\FileRecv\dist
   ```

3. 启动服务器：
   ```
   http-server
   ```

4. 默认情况下，服务器会在8080端口启动，您可以通过浏览器访问：http://localhost:8080

### 方法二：使用Express框架

1. 在项目目录初始化package.json：
   ```
   cd d:\New\yinyong\qq数据\2512151\FileRecv\dist
   npm init -y
   ```

2. 安装Express：
   ```
   npm install express --save
   ```

3. 创建server.js文件，内容如下：
   ```javascript
   const express = require('express');
   const app = express();
   const port = 3000;
   
   // 设置静态文件目录
   app.use(express.static('.')); // 当前目录作为静态文件目录
   
   // 启动服务器
   app.listen(port, () => {
     console.log(`服务器运行在 http://localhost:${port}`);
   });
   ```

4. 运行服务器：
   ```
   node server.js
   ```

5. 通过浏览器访问：http://localhost:3000

### 方法三：使用Node.js内置http模块（不依赖第三方包）

1. 创建一个简单的server.js文件：
   ```javascript
   const http = require('http');
   const fs = require('fs');
   const path = require('path');
   const url = require('url');
   
   const port = 8000;
   const baseDir = '.'; // 当前目录
   
   const server = http.createServer((req, res) => {
     // 获取请求的文件路径
     let filePath = path.join(baseDir, url.parse(req.url).pathname);
     
     // 如果请求的是目录，默认加载index.html
     if (fs.existsSync(filePath) && fs.lstatSync(filePath).isDirectory()) {
       filePath = path.join(filePath, 'index.html');
     }
     
     // 读取并发送文件
     fs.readFile(filePath, (err, data) => {
       if (err) {
         res.writeHead(404, { 'Content-Type': 'text/plain' });
         res.end('404 Not Found');
         return;
       }
       
       // 设置Content-Type
       const ext = path.extname(filePath);
       let contentType = 'text/html';
       if (ext === '.js') contentType = 'application/javascript';
       if (ext === '.css') contentType = 'text/css';
       if (ext === '.png' || ext === '.jpg' || ext === '.gif') {
         contentType = `image/${ext.substring(1)}`;
       }
       
       res.writeHead(200, { 'Content-Type': contentType });
       res.end(data);
     });
   });
   
   server.listen(port, () => {
     console.log(`服务器运行在 http://localhost:${port}`);
   });
   ```

2. 运行服务器：
   ```
   node server.js
   ```

3. 通过浏览器访问：http://localhost:8000

## 三、关闭服务器

- 在命令提示符中按 `Ctrl + C` 可以停止服务器。

## 四、注意事项

1. 使用Node.js的优势是可以更好地处理路由、API和跨域问题。
2. 如果您只是需要简单地预览静态网站，之前使用的Python内置HTTP服务器也是一个不错的选择。
3. 实际部署到生产环境时，建议使用Nginx或Apache等专业的Web服务器。