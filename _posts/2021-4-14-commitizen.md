---
layout: post
comments: true
title: 使用commitizen规范Git 提交说明
excerpt: 使用commitizen规范Git 提交说明
categories: work
date: 2021-04-14 12:00:00
---

## Commit Message 的标准格式

**Commit Message 标准格式包括三个部分： Header， Body， Footer**

```
<type>(<scope>): <subject>
// 空一行
<body>
// 空一行
<footer>
```

其中，**Header** 是必须的， **Body**和**Footer** 可以省略

### Header

**Header** 部分只有一行，包括三个字段：**type**（必需）、**scope**（可选）、**subject**（必需）

#### 1、type

用于说明类型。可分以下几种类型

|    Type     |                       Description                        |
| :---------: | :------------------------------------------------------: |
|    feat     |                          新功能                          |
|     fix     |                         修复bug                          |
| improvement |                     对当前功能的改进                     |
|    docs     |                     仅包含文档的修改                     |
|    style    | 格式化变动，不影响代码逻辑。比如清除多余空白，删除分号等 |
|  refactor   |      重构，既不是新增功能，也不是修改bug的代码变动       |
|    perf     |                      提高性能的修改                      |
|    test     |                    添加或修改测试代码                    |
|    build    |    构建工具或外部依赖包的修改，比如更新依赖包的版本等    |
|     ci      |              持续集成的配置文件或脚本的修改              |
|    chore    |          杂项。其他不修改源代码与测试代码的修改          |
|   revert    |                       撤销某次提交                       |

#### 2、scope

用于说明影响的范围，比如数据层、控制层、视图层等等。

#### 3. subject

主题，简短描述。一行

### Body

对 subject 的补充。可以多行。

### Footer

主要是一些关联 issue 的操作。

## Commitizen

Commitizen 是一个撰写符合上面 Commit Message 标准的一款工具。

### 安装

#### 全局安装（推荐）

1、下载

```
npm install -g commitizen cz-conventional-changelog
```

2、创建~/.czrc 文件，写入如下内容

```
{ "path": "cz-conventional-changelog" }
```

3、这时就可以全局使用 git cz 命令来代替 git commit 命令了

#### 项目局部使用

1、下载 commitizen

```
npm install --save-dev commitizen
```

2、配置，打开项目的 package.json 文件，配置如下：

```
 {
  "scripts": {
    "commit": "git-cz",
  },
  "config": {
    "commitizen": {
      "path": "node_modules/cz-conventional-changelog"
    }
  }
}
```

3、这时就可以使用 npm run commit 脚本了

### 使用

全局安装使用 git cz 来代替 git commit

局部安装使用 npm run commit 脚本来代替 git commit