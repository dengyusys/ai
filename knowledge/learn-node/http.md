# Node.js http/http2模块详解

> 领域: technology
> 标签: nodejs, http, http2, web-server, networking, core-modules
> 创建日期: 2026-02-12
> 关联文件: [url.md], [fs.md]

## 概述
`http`模块是Node.js的核心模块之一，用于创建HTTP服务器和客户端。`http2`模块提供了HTTP/2协议的支持，性能更好，功能更丰富。

## http模块核心功能

### 1. 创建HTTP服务器
```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  // 处理请求
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello, World!\n');
});

server.listen(3000, () => {
  console.log('服务器运行在 http://localhost:3000/');
});
```

### 2. HTTP客户端
```javascript
const http = require('http');

// 发送GET请求
const req = http.request('http://example.com', (res) => {
  console.log(`状态码: ${res.statusCode}`);
  console.log(`响应头: ${JSON.stringify(res.headers)}`);

  res.setEncoding('utf8');
  res.on('data', (chunk) => {
    console.log('响应主体:', chunk);
  });
});

req.on('error', (err) => {
  console.error('请求错误:', err);
});

req.end();
```

## 请求和响应对象

### 请求对象 (http.IncomingMessage)
```javascript
server.on('request', (req, res) => {
  console.log('请求方法:', req.method);        // GET, POST, etc.
  console.log('请求URL:', req.url);            // /path?query=value
  console.log('HTTP版本:', req.httpVersion);   // 1.1
  console.log('请求头:', req.headers);         // 头部信息对象

  // 获取查询参数
  const url = require('url');
  const parsedUrl = url.parse(req.url, true);
  console.log('查询参数:', parsedUrl.query);

  // 读取请求体（POST/PUT数据）
  let body = '';
  req.on('data', (chunk) => {
    body += chunk;
  });
  req.on('end', () => {
    console.log('请求体:', body);
    // 处理请求体数据
  });
});
```

### 响应对象 (http.ServerResponse)
```javascript
// 设置状态码和头部
res.statusCode = 201; // Created
res.setHeader('Content-Type', 'application/json');
res.setHeader('Cache-Control', 'no-cache');

// 或者一次性设置多个头部
res.writeHead(200, {
  'Content-Type': 'text/html',
  'X-Custom-Header': 'Custom Value'
});

// 发送响应数据
res.write('<h1>Hello</h1>');
res.end('<p>World</p>');

// 发送JSON响应
res.setHeader('Content-Type', 'application/json');
res.end(JSON.stringify({ success: true, data: 'Hello' }));

// 重定向
res.writeHead(302, {
  'Location': '/new-path'
});
res.end();
```

## 路由处理

### 基本路由
```javascript
const server = http.createServer((req, res) => {
  const { method, url } = req;

  if (method === 'GET' && url === '/') {
    res.writeHead(200, { 'Content-Type': 'text/html' });
    res.end('<h1>首页</h1>');
  } else if (method === 'GET' && url === '/api/users') {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify([{ id: 1, name: 'Alice' }]));
  } else if (method === 'POST' && url === '/api/users') {
    // 处理POST请求
    let body = '';
    req.on('data', chunk => body += chunk);
    req.on('end', () => {
      const user = JSON.parse(body);
      res.writeHead(201, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ id: Date.now(), ...user }));
    });
  } else {
    res.writeHead(404, { 'Content-Type': 'text/plain' });
    res.end('Not Found');
  }
});
```

## 中间件模式

### 简单的中间件实现
```javascript
class App {
  constructor() {
    this.middlewares = [];
  }

  use(middleware) {
    this.middlewares.push(middleware);
  }

  handleRequest(req, res) {
    const runMiddleware = (index) => {
      if (index >= this.middlewares.length) {
        // 最后一个中间件应该调用res.end()
        return;
      }

      const middleware = this.middlewares[index];
      middleware(req, res, () => runMiddleware(index + 1));
    };

    runMiddleware(0);
  }
}

// 使用示例
const app = new App();

app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next();
});

app.use((req, res, next) => {
  req.startTime = Date.now();
  next();
});

app.use((req, res) => {
  const duration = Date.now() - req.startTime;
  res.end(`请求处理时间: ${duration}ms`);
});

const server = http.createServer((req, res) => {
  app.handleRequest(req, res);
});
```

## 静态文件服务器

### 基础版本
```javascript
const fs = require('fs');
const path = require('path');

const server = http.createServer((req, res) => {
  // 防止路径遍历攻击
  const safePath = path.normalize(req.url).replace(/^(\.\.[\/\\])+/, '');
  const filePath = path.join(__dirname, 'public', safePath);

  fs.stat(filePath, (err, stats) => {
    if (err || !stats.isFile()) {
      res.writeHead(404);
      return res.end('File not found');
    }

    const ext = path.extname(filePath);
    const contentType = {
      '.html': 'text/html',
      '.css': 'text/css',
      '.js': 'application/javascript',
      '.json': 'application/json',
      '.png': 'image/png',
      '.jpg': 'image/jpeg'
    }[ext] || 'application/octet-stream';

    res.writeHead(200, { 'Content-Type': contentType });
    fs.createReadStream(filePath).pipe(res);
  });
});
```

## http2模块

### HTTP/2服务器
```javascript
const http2 = require('http2');
const fs = require('fs');

// 需要SSL证书（HTTP/2要求HTTPS）
const server = http2.createSecureServer({
  key: fs.readFileSync('server.key'),
  cert: fs.readFileSync('server.crt')
});

server.on('stream', (stream, headers) => {
  // HTTP/2使用stream而不是req/res对象
  stream.respond({
    'content-type': 'text/html',
    ':status': 200
  });

  stream.end('<h1>Hello HTTP/2</h1>');
});

server.listen(3000);
```

### HTTP/2特性
1. **多路复用**：单一连接上并行处理多个请求
2. **头部压缩**：使用HPACK算法压缩头部
3. **服务器推送**：服务器可以主动推送资源
4. **流优先级**：可以设置请求的优先级

```javascript
// 服务器推送示例
stream.pushStream({ ':path': '/style.css' }, (err, pushStream) => {
  if (err) throw err;
  pushStream.respond({ ':status': 200 });
  pushStream.end('body { color: red; }');
});
```

## 性能优化

### 1. 连接池
```javascript
const agent = new http.Agent({
  keepAlive: true,
  maxSockets: 10,
  maxFreeSockets: 5,
  timeout: 60000
});

const options = {
  hostname: 'example.com',
  port: 80,
  path: '/',
  method: 'GET',
  agent: agent // 使用连接池
};
```

### 2. 请求超时
```javascript
const req = http.request(options, (res) => {
  // 处理响应
});

req.setTimeout(5000, () => {
  console.log('请求超时');
  req.abort();
});

req.on('error', (err) => {
  if (err.code === 'ECONNRESET') {
    console.log('连接被重置');
  }
});

req.end();
```

### 3. 大文件传输
```javascript
// 使用流式传输大文件
app.get('/download', (req, res) => {
  const filePath = '/path/to/large/file.zip';
  const stat = fs.statSync(filePath);

  res.writeHead(200, {
    'Content-Type': 'application/octet-stream',
    'Content-Length': stat.size,
    'Content-Disposition': `attachment; filename="file.zip"`
  });

  const readStream = fs.createReadStream(filePath);
  readStream.pipe(res);
});
```

## 安全考虑

### 1. 防止DDoS攻击
```javascript
// 限制请求频率
const requestCounts = new Map();

setInterval(() => {
  requestCounts.clear();
}, 60000); // 每分钟清空计数器

server.on('request', (req, res) => {
  const clientIP = req.socket.remoteAddress;
  const count = requestCounts.get(clientIP) || 0;

  if (count > 100) { // 每分钟超过100次请求
    res.writeHead(429);
    res.end('请求过于频繁');
    return;
  }

  requestCounts.set(clientIP, count + 1);
  // 正常处理请求
});
```

### 2. 输入验证
```javascript
// 验证请求体大小
const MAX_BODY_SIZE = 1024 * 1024; // 1MB

server.on('request', (req, res) => {
  let bodyLength = 0;
  let body = '';

  req.on('data', (chunk) => {
    bodyLength += chunk.length;
    if (bodyLength > MAX_BODY_SIZE) {
      req.destroy(); // 中断连接
      res.writeHead(413);
      res.end('请求体过大');
      return;
    }
    body += chunk;
  });

  req.on('end', () => {
    // 安全处理请求体
  });
});
```

## 实际应用场景

### 1. RESTful API服务器
```javascript
class ApiServer {
  constructor() {
    this.routes = new Map();
  }

  addRoute(method, path, handler) {
    const key = `${method}:${path}`;
    this.routes.set(key, handler);
  }

  handleRequest(req, res) {
    const { method, url } = req;
    const parsedUrl = require('url').parse(url, true);
    const pathname = parsedUrl.pathname;

    const handler = this.routes.get(`${method}:${pathname}`);

    if (handler) {
      handler(req, res, parsedUrl.query);
    } else {
      res.writeHead(404);
      res.end('Not Found');
    }
  }
}
```

### 2. WebSocket网关
```javascript
const WebSocket = require('ws');

const server = http.createServer();
const wss = new WebSocket.Server({ server });

wss.on('connection', (ws) => {
  console.log('客户端连接');

  ws.on('message', (message) => {
    console.log('收到消息:', message);
    ws.send(`回声: ${message}`);
  });

  ws.on('close', () => {
    console.log('客户端断开连接');
  });
});

server.listen(3000);
```

### 3. 反向代理
```javascript
const proxyServer = http.createServer((clientReq, clientRes) => {
  const options = {
    hostname: 'backend-server.com',
    port: 80,
    path: clientReq.url,
    method: clientReq.method,
    headers: clientReq.headers
  };

  const proxyReq = http.request(options, (proxyRes) => {
    clientRes.writeHead(proxyRes.statusCode, proxyRes.headers);
    proxyRes.pipe(clientRes);
  });

  clientReq.pipe(proxyReq);
});
```

## 练习题目
1. 实现一个简单的博客系统API（CRUD操作）
2. 创建一个支持文件上传的HTTP服务器
3. 实现一个基本的负载均衡器（轮询算法）
4. 构建一个支持服务器推送的HTTP/2应用
5. 创建一个带有中间件的Web框架原型

## 参考资源
- [Node.js官方文档 - http模块](https://nodejs.org/api/http.html)
- [Node.js官方文档 - http2模块](https://nodejs.org/api/http2.html)
- 《深入浅出Node.js》网络编程章节
- 相关开源项目：Express、Koa、Fastify等框架源码

---
*本文件将随着学习深入不断更新*