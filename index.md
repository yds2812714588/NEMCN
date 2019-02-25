---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---
## 概要
* 这是Nem2-cli的Usercase，我们将教你如何使用nem2-cli构建一个Nem2的Demo Dapp.
使用Nem2-cli可以很方便的使用命令的方式快速搭建一个Nem2 Dapp的框架，然后你可以在里面写业务，快速实现一个Nem2 Dapp的开发。我们会先将Nem2-cli所有的命令跟您介绍一遍，然后你就知道这些命令有什么作用，如何使用，通过Usercase的方式，你会很轻松了解到如何调用这些命令以及深入代码学习到它内部实现的原理。

### 设置开发环境

* 创建一个package.json文件。 Node.js所需的最低版本是8.9.X.
$> npm init
* 安装nem2-sdk和rxjs库。
$> npm install nem2-sdk rxjs
* nem2-sdk是使用TypeScript语言构建的。 在为NEM区块链构建应用程序时，建议使用TypeScript而不是JavaScript。
确保至少安装了2.5.X版。

```
$> sudo npm install --global typescript
$> typescript --version
$> sudo npm install --global ts-node
```
<br/><br/>
 

## Abstract

* This is the Usercase of Nem2-cli, we will teach you how to build a Nem2 Demo Dapp using nem2-cli.
Using Nem2-cli, you can quickly build a Nem2 Dapp framework using commands, and then you can write business in it and quickly implement a Nem2 Dapp development.
We will first introduce all the commands of Nem2-cli to you, then you will know what these commands do, how to use them, through Usercase, you will easily understand how to call these commands and in-depth code to learn inside it. The principle of implementation.


## Requirements

Before starting the workshop, install the following packages:

* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Docker](https://docs.docker.com/install/)
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Node](https://nodejs.org/en/download/) >= 8.9.X.

## Setting up the development environment

* Create a folder for your new project and run the instructions for the selected language.
* Create a package.json file. The minimum required Node.js version is 8.9.X.
$> npm init
* Install nem2-sdk and rxjs library.
$> npm install nem2-sdk rxjs
* nem2-sdk is build with TypeScript language. It is recommended to use TypeScript instead of JavaScript when building applications for NEM blockchain.
Make sure you have at least version 2.5.X installed.

```
$> sudo npm install --global typescript
$> typescript --version
$> sudo npm install --global ts-node
```




