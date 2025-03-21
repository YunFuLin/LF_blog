---
title: scp2自动化部署到静态文件服务器 Nginx
date: 2021-11-20 23:55:34
layout: 'archives'
top: true
urlname: notes
keywords: 'scp2自动化部署到静态文件服务器 Nginx'
tags: 
- 前端自动化部署
categories: 
- 笔记
---

>平时部署项目：先部署到测试环境，再发布到生产环境上，部署到测试环境用 `xshell` 连上服务器，然后用 `xftp` 连接服务器，将本地 `build` 好的 `dist` 文件通过 `xftp` 上传到服务器上，整个流程感觉稍有繁琐，重复。

>解决方法：利用`scp2`插件完成自动化部署
## 一、安装 `scp2` 插件
```javascript
npm install scp2 --save-dev
```
## 二、配置 测试 / 生产环境
1. 在项目根目录下, 创建 `.env.test` 文件 (测试环境变量)、`.env.prod` 文件 (生产环境变量)
```javascript
// .env.test文件中
VUE_APP_SERVER_ID=0


// .env.prod文件中
VUE_APP_SERVER_ID=1
```
## 三、创建 `scp2` 自动化部署脚本
### 1、 在项目根目录下, 创建 `deploy/products.js` 文件
```javascript

// 读取env环境变量
const fs = require('fs');
const path = require('path');
// env 文件 判断打包环境指定对应的服务器id
const envfile = process.env.NODE_ENV === 'prod' ? '../.env.prod' : '../.env.test';

// env环境变量的路径
const envPath = path.resolve(__dirname, envfile);
// env对象
const envObj = parse(fs.readFileSync(envPath, 'utf8'));
const SERVER_ID = parseInt(envObj.VUE_APP_SERVER_ID);

console.log('环境变量、路径、ID',process.env.NODE_ENV, envfile, SERVER_ID)

function parse(src) {
  // 解析KEY=VAL的文件
  const res = {};
  src.split('\n').forEach((line) => {
    // matching "KEY' and 'VAL' in 'KEY=VAL'
    // eslint-disable-next-line no-useless-escape
    const keyValueArr = line.match(/^\s*([\w\.\-]+)\s*=\s*(.*)?\s*$/);
    // matched?
    if (keyValueArr != null) {
      const key = keyValueArr[1];
      let value = keyValueArr[2] || '';

      // expand newlines in quoted values
      const len = value ? value.length : 0;
      if (len > 0 && value.charAt(0) === '"' && value.charAt(len - 1) === '"') {
        value = value.replace(/\\n/gm, '\n');
      }

      // remove any surrounding quotes and extra spaces
      value = value.replace(/(^['"]|['"]$)/g, '').trim();

      res[key] = value;
    }
  });
  return res;
}

/*
 *定义多个服务器账号 及 根据 SERVER_ID 导出当前环境服务器账号
 */
const privateKeyPath = '../deploy/id_rsa'; // 把秘钥保存到项目中
const privateKeyFile = path.resolve(__dirname, privateKeyPath);
let privateKey = fs.readFileSync(privateKeyFile).toString();

const SERVER_LIST = [
  {
    id: 0,
    name: '测试环境',
    domain: 'www.dev.com', // 域名
    host: 'xx.xx.xx.xx', // ip
    port: 22, // 端口
    username: 'admin', // 登录服务器的账号
    //password: '123456', // 密码
    privateKey: privateKey, // 私钥匙 账号密码暴露出来不安心，用秘钥（其实也不安全哈）
    path: '/data/program/dist', // 发布至静态服务器的项目路径
    relativePath: './dist',
  },
  {
    id: 1,
    name: '生产环境',
    domain: 'www.prod.com', // 域名
    host: 'xx.xx.xx.xx', // ip
    port: 22, // 端口
    username: 'admin', // 登录服务器的账号
    //password: '123456', // 密码
    privateKey: privateKey, // 私钥匙 账号密码暴露出来不安心，用秘钥（其实也不安全哈）
    path: '/data/program/dist', // 发布至静态服务器的项目路径
    relativePath: './dist',
  }
];

module.exports = SERVER_LIST[SERVER_ID];
```
### 2、在项目根目录下, 创建 `deploy/index.js` 文件
```javascript
const scpClient = require('scp2');
const chalk = require('chalk');
const server = require('./products');

scpClient.scp(
  server.relativePath,
  {
    host: server.host,
    port: server.port,
    username: server.username,
    //password: server.password,
    privateKey: server.privateKey,
    path: server.path,
  },
  (err) => {
    if (err) {
      console.log(chalk.red('发布失败.\n'));
      throw err;
    } else {
      console.log(chalk.green('Success! 成功发布到' + (process.env.NODE_ENV === 'prod' ? '生产' : '测试') + '服务器! \n'));
    }
  },
);
```
## 四、在 `package.json` 里添加执行命令
>安装 `cross-env`：支持跨平台地设置及使用环境变量，这里用来设置 `生产环境`还是`测试环境`
```javascript
npm i cross-env --save-dev
```
```javascript
"scripts": {
    "dev": "webpack-dev-server --config ./scripts/webpack.dev.js",
    "build": "webpack --config ./scripts/webpack.prod.config.js",
    "deploy:test": "npm run build && cross-env NODE_ENV=test node ./deploy",
  	"deploy:prod": "npm run build && cross-env NODE_ENV=prod node ./deploy",
  },
```