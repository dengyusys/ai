# Node.js url模块详解

> 领域: technology
> 标签: nodejs, url, web, networking, core-modules
> 创建日期: 2026-02-12
> 关联文件: [http.md], [path.md]

## 概述
`url`模块是Node.js的核心模块之一，用于URL的解析和格式化。它提供了处理URL字符串的工具，将URL分解为各个组成部分，或者将各个组成部分组合成完整的URL。

## URL组成结构

一个完整的URL包含以下部分：
```
  https://user:pass@sub.example.com:8080/p/a/t/h?query=string#hash
  \___/   \______________________/\_________/ \_________/ \____/
    |               |                  |           |         |
  协议          认证信息               主机        路径      片段
```

## 核心API

### 1. `url.parse(urlString[, parseQueryString[, slashesDenoteHost]])`

```javascript
const url = require('url');

const urlString = 'https://user:pass@sub.example.com:8080/p/a/t/h?query=string#hash';
const parsed = url.parse(urlString, true);

console.log(parsed);
/*
{
  protocol: 'https:',
  slashes: true,
  auth: 'user:pass',
  host: 'sub.example.com:8080',
  port: '8080',
  hostname: 'sub.example.com',
  hash: '#hash',
  search: '?query=string',
  query: { query: 'string' }, // 因为第二个参数是true，所以解析为对象
  pathname: '/p/a/t/h',
  path: '/p/a/t/h?query=string',
  href: 'https://user:pass@sub.example.com:8080/p/a/t/h?query=string#hash'
}
*/
```

### 2. `url.format(urlObject)`

```javascript
const urlObj = {
  protocol: 'https:',
  hostname: 'example.com',
  pathname: '/path',
  query: { search: 'nodejs' }
};

const formatted = url.format(urlObj);
// 返回: 'https://example.com/path?search=nodejs'
```

### 3. `url.resolve(from, to)`

```javascript
const resolved = url.resolve('http://example.com/foo/bar', '../baz');
// 返回: 'http://example.com/baz'

const resolved2 = url.resolve('http://example.com/foo/bar', '/qux');
// 返回: 'http://example.com/qux'

const resolved3 = url.resolve('http://example.com/', '/images/logo.png');
// 返回: 'http://example.com/images/logo.png'
```

## 新版URL API (URL类)

Node.js也支持WHATWG URL标准，提供了更现代的API：

```javascript
const { URL, URLSearchParams } = require('url');

// 创建URL对象
const myURL = new URL('https://example.com/path?name=value#section');

// 访问各个部分
console.log(myURL.protocol);    // 'https:'
console.log(myURL.hostname);    // 'example.com'
console.log(myURL.pathname);    // '/path'
console.log(myURL.search);      // '?name=value'
console.log(myURL.hash);        // '#section'

// 修改URL
myURL.protocol = 'http:';
myURL.port = '8080';
myURL.searchParams.set('page', '2');

console.log(myURL.href); // 'http://example.com:8080/path?name=value&page=2#section'
```

## URLSearchParams

`URLSearchParams` 提供了操作查询字符串的便捷方法：

```javascript
const { URLSearchParams } = require('url');

// 从字符串创建
const params1 = new URLSearchParams('key1=value1&key2=value2');

// 从对象创建
const params2 = new URLSearchParams({ key1: 'value1', key2: 'value2' });

// 从数组创建
const params3 = new URLSearchParams([['key1', 'value1'], ['key2', 'value2']]);

// 常用方法
params.set('key3', 'value3');        // 添加/设置参数
params.append('key3', 'another');    // 添加重复参数
params.get('key1');                  // 'value1'
params.getAll('key3');               // ['value3', 'another']
params.has('key1');                  // true
params.delete('key2');               // 删除参数
params.toString();                   // 'key1=value1&key3=value3&key3=another'

// 遍历参数
for (const [key, value] of params) {
  console.log(`${key}: ${value}`);
}
```

## 实际应用场景

### 1. Web服务器中的URL解析

```javascript
const http = require('http');
const url = require('url');

const server = http.createServer((req, res) => {
  const parsedUrl = url.parse(req.url, true);

  console.log('路径:', parsedUrl.pathname);
  console.log('查询参数:', parsedUrl.query);
  console.log('哈希:', parsedUrl.hash);

  // 根据路径路由
  switch (parsedUrl.pathname) {
    case '/':
      res.end('首页');
      break;
    case '/api/data':
      const page = parseInt(parsedUrl.query.page) || 1;
      const limit = parseInt(parsedUrl.query.limit) || 10;
      // 返回分页数据
      res.end(JSON.stringify({ page, limit, data: [] }));
      break;
    default:
      res.writeHead(404);
      res.end('Not Found');
  }
});
```

### 2. 构建API请求URL

```javascript
function buildApiUrl(baseUrl, endpoint, params = {}) {
  const urlObj = url.parse(baseUrl);
  urlObj.pathname = endpoint;

  // 合并查询参数
  const searchParams = new URLSearchParams(urlObj.search?.substring(1) || '');
  Object.entries(params).forEach(([key, value]) => {
    searchParams.set(key, value);
  });

  urlObj.search = searchParams.toString();
  return url.format(urlObj);
}

// 使用示例
const apiUrl = buildApiUrl('https://api.example.com', '/users', {
  page: 1,
  limit: 20,
  sort: 'name'
});
// 返回: 'https://api.example.com/users?page=1&limit=20&sort=name'
```

### 3. URL验证和规范化

```javascript
function isValidUrl(urlString) {
  try {
    const urlObj = new URL(urlString);
    return ['http:', 'https:'].includes(urlObj.protocol);
  } catch {
    return false;
  }
}

function normalizeUrl(urlString, baseUrl) {
  try {
    const urlObj = new URL(urlString, baseUrl);

    // 移除默认端口
    if ((urlObj.protocol === 'http:' && urlObj.port === '80') ||
        (urlObj.protocol === 'https:' && urlObj.port === '443')) {
      urlObj.port = '';
    }

    // 规范化路径
    urlObj.pathname = urlObj.pathname.replace(/\/+/g, '/');

    // 排序查询参数
    const params = new URLSearchParams(urlObj.searchParams);
    params.sort();
    urlObj.search = params.toString();

    return urlObj.href;
  } catch (error) {
    console.error('URL规范化失败:', error);
    return null;
  }
}

// 使用示例
console.log(isValidUrl('https://example.com')); // true
console.log(isValidUrl('javascript:alert(1)')); // false

const normalized = normalizeUrl('/api/data?b=2&a=1', 'https://example.com');
// 返回: 'https://example.com/api/data?a=1&b=2'
```

### 4. 处理相对路径

```javascript
function resolveRelativeUrl(baseUrl, relativePath) {
  const base = new URL(baseUrl);
  const resolved = new URL(relativePath, base);
  return resolved.href;
}

// 使用示例
const base = 'https://example.com/blog/post.html';
const relative = '../images/logo.png';
const absolute = resolveRelativeUrl(base, relative);
// 返回: 'https://example.com/images/logo.png'
```

### 5. 构建分页链接

```javascript
function buildPaginationLinks(currentUrl, currentPage, totalPages) {
  const urlObj = new URL(currentUrl);
  const links = {};

  if (currentPage > 1) {
    urlObj.searchParams.set('page', currentPage - 1);
    links.prev = urlObj.href;
  }

  if (currentPage < totalPages) {
    urlObj.searchParams.set('page', currentPage + 1);
    links.next = urlObj.href;
  }

  // 生成所有页码链接
  links.pages = [];
  for (let i = 1; i <= totalPages; i++) {
    urlObj.searchParams.set('page', i);
    links.pages.push({
      number: i,
      url: urlObj.href,
      active: i === currentPage
    });
  }

  return links;
}
```

## 安全考虑

### 1. 防止开放重定向攻击

```javascript
function isSafeRedirect(urlString, allowedDomains) {
  try {
    const urlObj = new URL(urlString);

    // 检查协议
    if (!['http:', 'https:'].includes(urlObj.protocol)) {
      return false;
    }

    // 检查域名
    const domain = urlObj.hostname;
    const isAllowed = allowedDomains.some(allowed =>
      domain === allowed || domain.endsWith('.' + allowed)
    );

    return isAllowed;
  } catch {
    return false;
  }
}

// 使用示例
const allowed = ['example.com', 'trusted-site.org'];
console.log(isSafeRedirect('https://example.com/login', allowed)); // true
console.log(isSafeRedirect('https://evil.com/steal', allowed));    // false
console.log(isSafeRedirect('javascript:alert(1)', allowed));       // false
```

### 2. URL编码和解码

```javascript
// 正确的URL编码
const original = 'hello world & special chars = ?';
const encoded = encodeURIComponent(original);
// 'hello%20world%20%26%20special%20chars%20%3D%20%3F'

const decoded = decodeURIComponent(encoded);
// 'hello world & special chars = ?'

// 注意：encodeURI和encodeURIComponent的区别
const fullUrl = 'https://example.com/path?name=value&param=hello world';
const encodedUrl = encodeURI(fullUrl);
// 'https://example.com/path?name=value&param=hello%20world'

const encodedParam = encodeURIComponent('hello world');
// 'hello%20world'
```

## 与path模块的对比

| 模块 | 用途 | 示例 |
|------|------|------|
| `path` | 处理文件系统路径 | `path.join('/foo', 'bar')` → `/foo/bar` |
| `url` | 处理网络URL | `url.resolve('http://a.com/b', 'c')` → `http://a.com/c` |

```javascript
// 区分使用场景
const path = require('path');
const url = require('url');

// 文件系统路径操作
const filePath = path.join(__dirname, 'public', 'index.html');

// URL操作
const webUrl = url.resolve('https://example.com/api/', '../v2/users');
// 'https://example.com/v2/users'

// 不要混淆使用
// ❌ 错误：用path处理URL
const badUrl = path.join('https://example.com', 'api/users');
// 返回: 'https:\\example.com\api\users' (Windows) 或 'https:/example.com/api/users' (POSIX)

// ❌ 错误：用url处理文件路径
const badPath = url.resolve('C:/Users', 'Documents/file.txt');
// 可能产生意想不到的结果
```

## 练习题目

1. **URL解析器**：实现一个函数，接收URL字符串，返回包含所有解析信息的对象
2. **查询字符串构建器**：创建能够处理嵌套对象和数组的查询字符串构建工具
3. **URL路由器**：实现支持动态参数的路由匹配系统（如 `/users/:id`）
4. **爬虫URL管理器**：构建一个URL队列管理器，避免重复爬取，处理相对路径
5. **API客户端**：创建一个灵活的API客户端，能够自动构建带查询参数的请求URL

## 常见问题

### Q: `url.parse()` 和 `new URL()` 有什么区别？
A:
- `url.parse()` 是旧版API，提供了更多选项（如`parseQueryString`）
- `new URL()` 是WHATWG标准，更符合现代Web标准
- 推荐使用 `new URL()`，但需要处理旧代码时可能需要 `url.parse()`

### Q: 什么时候使用 `encodeURI()` 和 `encodeURIComponent()`？
A:
- 对整个URL使用 `encodeURI()`，它不会编码 `:/?#[]@!$&'()*+,;=` 等URL保留字符
- 对URL中的查询参数值使用 `encodeURIComponent()`，它会编码所有非字母数字字符
- 简单记法：`encodeURIComponent()` 编码更彻底，适合参数值

### Q: 如何处理包含中文字符的URL？
A:
```javascript
// 中文URL需要正确编码
const chineseUrl = 'https://example.com/search?q=中文';
const encoded = encodeURI(chineseUrl);
// 'https://example.com/search?q=%E4%B8%AD%E6%96%87'

// 或者使用URL对象自动处理
const urlObj = new URL('https://example.com/search');
urlObj.searchParams.set('q', '中文');
console.log(urlObj.href); // 自动编码
```

## 参考资源
- [Node.js官方文档 - url模块](https://nodejs.org/api/url.html)
- [WHATWG URL标准](https://url.spec.whatwg.org/)
- [MDN URL API文档](https://developer.mozilla.org/en-US/docs/Web/API/URL)
- 相关开源项目：Express路由系统、request库的URL处理等

---
*本文件将随着学习深入不断更新*