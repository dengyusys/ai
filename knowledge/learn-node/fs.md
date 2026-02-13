# Node.js fs模块详解

> 领域: technology
> 标签: nodejs, fs, file-system, core-modules, async
> 创建日期: 2026-02-12
> 关联文件: [path.md], [http.md]

## 概述
`fs`（文件系统）模块是Node.js的核心模块之一，提供文件系统操作功能。它支持同步和异步两种操作方式，以及基于Promise的API（fs.promises）。

## 核心功能

### 1. 文件读写
```javascript
const fs = require('fs');

// 同步读取
const dataSync = fs.readFileSync('file.txt', 'utf8');

// 异步读取（回调方式）
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) throw err;
  console.log(data);
});

// Promise方式（推荐）
const fsPromises = require('fs').promises;
const data = await fsPromises.readFile('file.txt', 'utf8');
```

### 2. 目录操作
```javascript
// 创建目录
fs.mkdirSync('new-directory');

// 读取目录内容
const files = fs.readdirSync('.');
```

### 3. 文件信息
```javascript
const stats = fs.statSync('file.txt');
console.log(`文件大小: ${stats.size} bytes`);
console.log(`创建时间: ${stats.birthtime}`);
console.log(`是否目录: ${stats.isDirectory()}`);
```

## API分类

### 异步回调API
- `fs.readFile(path[, options], callback)`
- `fs.writeFile(file, data[, options], callback)`
- `fs.appendFile(path, data[, options], callback)`
- `fs.unlink(path, callback)` - 删除文件
- `fs.rename(oldPath, newPath, callback)` - 重命名

### 同步API（添加Sync后缀）
- `fs.readFileSync()`
- `fs.writeFileSync()`
- `fs.appendFileSync()`
- `fs.existsSync()` - 检查文件是否存在（异步版本已废弃）

### Promise API（fs.promises）
```javascript
const fsp = require('fs').promises;

async function processFile() {
  try {
    const data = await fsp.readFile('input.txt', 'utf8');
    const processed = data.toUpperCase();
    await fsp.writeFile('output.txt', processed);
  } catch (err) {
    console.error('文件处理错误:', err);
  }
}
```

### 流式操作
```javascript
const readStream = fs.createReadStream('large-file.txt', 'utf8');
const writeStream = fs.createWriteStream('copy.txt');

readStream.pipe(writeStream);

// 或手动处理数据
readStream.on('data', (chunk) => {
  console.log('收到数据块:', chunk.length);
});

readStream.on('end', () => {
  console.log('文件读取完成');
});
```

## 重要概念

### 1. 文件描述符
```javascript
// 打开文件获取文件描述符
const fd = fs.openSync('file.txt', 'r');
// 使用文件描述符读取
const buffer = Buffer.alloc(1024);
fs.readSync(fd, buffer, 0, buffer.length, 0);
fs.closeSync(fd);
```

### 2. 文件权限
```javascript
// 修改文件权限（八进制表示）
fs.chmodSync('script.sh', 0o755); // rwxr-xr-x

// 常用权限：
// 0o644 - 所有者读写，其他人只读
// 0o755 - 所有者读写执行，其他人读执行
// 0o600 - 仅所有者读写
```

### 3. 符号链接
```javascript
// 创建符号链接
fs.symlinkSync('target.txt', 'link.txt');

// 读取符号链接指向的实际路径
const actualPath = fs.readlinkSync('link.txt');
```

## 实际应用场景

### 1. 配置文件管理
```javascript
const configPath = './config.json';

// 读取配置
const config = JSON.parse(fs.readFileSync(configPath, 'utf8'));

// 保存配置
config.lastUpdate = new Date().toISOString();
fs.writeFileSync(configPath, JSON.stringify(config, null, 2));
```

### 2. 日志系统
```javascript
class Logger {
  constructor(logFile) {
    this.logFile = logFile;
  }

  log(message) {
    const timestamp = new Date().toISOString();
    const logEntry = `[${timestamp}] ${message}\n`;

    fs.appendFile(this.logFile, logEntry, (err) => {
      if (err) console.error('日志写入失败:', err);
    });
  }
}
```

### 3. 文件上传处理
```javascript
// 结合http模块处理文件上传
http.createServer((req, res) => {
  if (req.url === '/upload' && req.method === 'POST') {
    const writeStream = fs.createWriteStream('uploaded-file.bin');
    req.pipe(writeStream);

    writeStream.on('finish', () => {
      res.writeHead(200, { 'Content-Type': 'text/plain' });
      res.end('文件上传成功');
    });
  }
});
```

### 4. 目录遍历
```javascript
// 递归遍历目录
function walkDir(dir) {
  const items = fs.readdirSync(dir);

  for (const item of items) {
    const fullPath = path.join(dir, item);
    const stat = fs.statSync(fullPath);

    if (stat.isDirectory()) {
      console.log(`目录: ${fullPath}`);
      walkDir(fullPath);
    } else {
      console.log(`文件: ${fullPath} (${stat.size} bytes)`);
    }
  }
}
```

## 最佳实践

### 1. 错误处理
```javascript
// 异步回调的错误处理
fs.readFile('nonexistent.txt', 'utf8', (err, data) => {
  if (err) {
    if (err.code === 'ENOENT') {
      console.log('文件不存在');
    } else {
      console.error('读取文件错误:', err);
    }
    return;
  }
  console.log(data);
});
```

### 2. 使用Promise API
- 避免"回调地狱"
- 更好地使用async/await
- 统一的错误处理机制

### 3. 大文件使用流
- 内存效率高
- 支持实时处理
- 避免阻塞事件循环

### 4. 路径安全
```javascript
// 不安全：可能造成路径遍历攻击
const userFile = path.join('public', req.query.filename);

// 安全：验证路径在允许范围内
const safePath = path.normalize(userFile);
if (!safePath.startsWith(path.join(process.cwd(), 'public'))) {
  throw new Error('非法路径访问');
}
```

## 常见错误码
- `ENOENT`：文件或目录不存在
- `EACCES`：权限不足
- `EEXIST`：文件已存在
- `EISDIR`：期望是文件但实际是目录
- `ENOTDIR`：期望是目录但实际是文件

## 练习题目
1. 实现一个简单的文件复制工具，支持进度显示
2. 创建一个目录清理工具，删除指定天数前的文件
3. 实现一个文件搜索工具，支持按名称和内容搜索
4. 构建一个简单的静态文件服务器

## 参考资源
- [Node.js官方文档 - fs模块](https://nodejs.org/api/fs.html)
- 《Node.js设计模式》中的文件操作章节
- 相关开源项目：Express静态文件中间件、webpack-dev-server等

---
*本文件将随着学习深入不断更新*