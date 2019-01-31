---
title: 使用Yeoman制作自己的脚手架
date: 2019-01-02 15:01:53
tags: ['Yeoman', '脚手架', '自己的脚手架']
categories: ['工具']
---

 基于[generator-vuecli-3-mobile](https://www.npmjs.com/package/generator-vuecli-3-mobile)为例
## 导读

面对复杂的 Web 应用的开发，良好的流程和工具支持是必不可少的，可以让日常的开发工作更加顺畅。Yeoman 作为一个流行的工具集，在整合了 Yo、Grunt 和 Bower 等工具的基础上，定义了一个更加完备和清晰的工作流程。通过把一些最佳实践引入到 Web 应用中，有助于创建高质量和可维护的应用。

<!-- more -->

## 环境

需要安装[Nodejs](https://nodejs.org/en/)

全局安装yo

```js
npm install -g yo
```

安装[generator-generator](https://github.com/yeoman/generator-generator),生成自己需要的generator的基础框架

```js
npm install -g generator-generator
```

## 运行命令开始生成基础代码

```js
yo generator
```

设置之后

![](/images/generator.png)

检查一下生成的目录结构

```pre
.
├── generators/
│   └── app/
│       ├── index.js // 设置项目模板文件
│       └── templates/ // 存放模板文件
│           └── dummyfile.txt
├── .editorconfig
├── .gitattributes
├── .gitignore
├── .eslintrc
├── .travis.yml
├── .yo-rc.json // 存储项目配置
├── package.json
├── gulpfile.js
├── README.md
├── LICENSE
```

## 将模板文件放入到 generators/app/templates文件夹后，我们开始编辑 index.js 文件

```js
'use strict';
const Generator = require('yeoman-generator');
const chalk = require('chalk');
const yosay = require('yosay');
const glob = require('globby');
const path = require('path');

module.exports = class extends Generator {
  prompting() {
    this.log(
      yosay(`Welcome to the phenomenal ${chalk.red('generator-test-generator')} generator!`)
    );

    const prompts = [
      {
        type: 'confirm',
        name: 'someAnswer',
        message: 'Would you like to enable this option?',
        default: true
      },
      {
        type: 'input',
        name: 'project-version',
        message: '项目版本号',
        default: '0.0.1'
      }
      ... // 可以根据自己的需要自行配置
    ];

    return this.prompt(prompts).then(props => {
      this.props = props;
    });
  }
  /**
   * 写入文件
   */
  writing() {
    this._writeRootFiles();
    this._writeFolders();
  }
  //  项目中的文件夹内容
  _writeFolders() {
    ['public', 'src'].forEach(floder => {
      this.fs.copy(
        this.templatePath(floder),
        this.destinationPath(floder)
      )
    });
  }
  // 根目录下的文件
  _writeRootFiles() {
    glob
      .sync([this.templatePath('*'), this.templatePath('package.json')])
      .forEach(file => {
        this.fs.copy(
          this.templatePath(file),
          this.destinationPath(path.basename(file).replace(/^_/, ''))
        )
      });
    this.fs.copyTpl(
      this.templatePath('package.json'),
      this.destinationPath('package.json'),
      this.props
    );
  }
  /**
   * 安装方法
   */
  install() {
    // 安装 package 安装.
    this.installDependencies({ bower: false });
  }
};

```

## 提交到git仓库

```js
git init // 初始化
git add . // 添加所以要提交的文件
git commit -m 'first commit' // 添加提交描述
git remote add origin https://github.com/BruceShang/generator-vuecli-3-mobile.git // 将文件添加到新仓库地址
git push -u origin master // 推送到master分支
```

## 发布到npm

> 登录or注册账号

```js
npm publish --access=public

// 发布成功后会返回版本号

+ generator-vuecli-3-mobile@0.0.4
```

## 如何使用

```js
yo vuecli-3-mobile
```

## 升级脚手架

实际开发中我们难免会多次修改项目，如何升级脚手架呢？

- 1.修改项目中package.json里的version版本号
- 2.重新发布
  ```js
    npm publish
  ```

- 3.重新安装全局脚手架
  ```js
  sudo npm i vuecli-3-mobile@1.0.1 -g
  // 或者 先卸载之前的安装，再重新安装
  sudo npm uninstall vuecli-3-mobile -g
  sudo npm install vuecli-3-mobile -g
  ```