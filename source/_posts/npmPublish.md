---
title: npm快速发布自己的模块
date: 2017-01-09 14:15:20
tags: [npm发布]
categories: node.js
---

### 创建
>注意创建模块前，先去 [npm 官网](https://www.npmjs.com/)确认模块名是否未被占用

```bash
npm init
```

### 添加账号

```bash
$ npm adduser   
Username: your name
Password: your password
Email: yourmail@gmail.com
```
### 发布
```bash
npm publish
```

### 编辑 .npmignore

>如果文件夹中存在 .gitignore， 则 .npmignore 与之相同；
>如果想忽略一些 .gitignore 中没有包括的东西，那么创建一个空的 .npmignore 可覆盖之.
npm 社区版本号规则采用的是 [semver](http://semver.org/) (语义化版本)

<!-- more -->

### 报错
发布时报错：no_perms Private mode enable, only admin can publish this module

因为 npm 镜像源会有延时，替换为官方源即可。

```bash
npm config set registry http://registry.npmjs.org
```
然后重新 adduser

### 镜像源
>[临时] 通过 config 配置指向国内镜像源
```bash
# 配置指向源npm info express
npm config set registry http://registry.cnpmjs.org
```
>[临时] 通过 npm 命令指定下载源

```bash
npm --registry http://registry.cnpmjs.org info express
```
>在配置文件 ~/.npmrc 文件写入源地址

```bash
//打开配置文件
vim ~/.npmrc
//写入配置文件
registry =https://registry.npm.taobao.org
```

### 淘宝 npm 镜像
```js
//cnpm
npm install -g cnpm --registry=https://registry.npm.taobao.org 

npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
```