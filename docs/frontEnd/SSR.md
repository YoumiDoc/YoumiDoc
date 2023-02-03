---
title: SSR
order: 30
group:
  title: 后端篇
---

## 简介

Server Side Render，服务端渲染，即渲染的过程在服务端进行，而不是浏览器，利于性能优化和 SEO.

这并不是一种很新的技术，但是却在近年又得到了广泛的应用.

本文不会深入介绍 SSR 相关框架，仅阐述基本原理及使用方式.

那么执行下述命令，让我们开始吧：

```bash
npm init -y
npm i react react-dom
```

### React 与 SSR

```javascript
const React = require('react');
const ReactDOMServer = require('react-dom/server');
const http = require('http');

function App(props) {
  return React.createElement('div', {}, props?.children || 'Hello');
}

const server = http.createServer((req, res) => {
  res.end(`
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Document</title>
    </head>
    <body>
        <div>
            <h1 style="color: red">Youmi</h1>
            ${ReactDOMServer.renderToString(
              React.createElement(App, {}, 'Hello World!'),
            )}
        </div>
    </body>
    </html>
    `);
});

server.listen(3680, () => {
  console.log('server is running on the port: 3680');
});
```

## 继续学习

[NextJS 官网](https://nextjs.org/)

[NuxtJS 官网](https://nuxtjs.org/)
