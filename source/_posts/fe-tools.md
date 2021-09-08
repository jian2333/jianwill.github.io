---
title: 自用的前端小工具
date: 2021-09-08 10:54:26
top:
categories:
- 学习笔记
tags:
- 前端
- cli
- nodejs
---

最近要在项目里引入 `git commit` 前执行 `eslint` 校验，通过才允许提交的功能。

考虑到可能会在多个项目中使用，以后也可能会增加其他功能。

然后吧，决定写个小工具，集合了**一键安装**相关依赖的功能，后续只要不断维护小工具就能扩展功能了。

目前的成果是，写了个包发布在 `npm` 上，通过命令 `npx zwt-fe` 即可使用。

效果如下：

![zwt-fe](/images/ft-1.gif)

<!--more-->

### 想法

**一开始**

考虑到用户体验，最开始是打算使用类似 [一键安装v2ray脚本](https://jian2333.github.io/2020/01/05/v2ray/) 那种方式的，用户只需在 `终端` 输入命令，即可直接使用最新版的小工具，类似这样：

```bash
bash <(curl -L -s https://raw.githubusercontent.com/wulabing/V2Ray_ws-tls_bash_onekey/master/install.sh) | tee v2ray_ins.log
```

**后来**

但是考虑到开发成本，**一键安装脚本** 要使用 `shell` 开发的，有一定的学习成本，而且有些功能(动效、替换内容等)实现比较麻烦，最后决定用 `node` 写了，发包到 `npm` 上，直接使用 `npx xx` 即可。

### eslint + husky

- 提交代码 `git commit` 时自动调用 `eslint` 校验代码，通过才提交代码。
- 输入 `npm run lint:fix` 调用 `esilnt` 自动修复相关错误。

**实现过程**

`husky` 可以捕获 `git` 的钩子，在 `git commit` 前的钩子 `pre-commit` 上添加 `npm run lint` 即可以实现。

**eslint配置**

- 安装依赖：`npm install -D eslint@7.32.0 eslint-plugin-vue@7.16.0 eslint-loader@4.0.2 babel-eslint@10.1.0` 。
- 创建 `.eslintrc.js` 并写入：`less xxx > .eslintrc.js` 。
- 创建 `.eslintignore` 并写入：`less xx > .eslintignore` 。
- `package.json` 的 `script` 中写入：
  - `npm run lint`：`npm set-script lint "./node_modules/.bin/eslint --ext .js,.vue ./"` 。
  - `npm run lint:fix`：`npm set-script lint "./node_modules/.bin/eslint --fix --ext .js,.vue ./"` 。
  - `npm set-script` 需要 `npm7 | node15` 以上，版本不符合需自己写方法替换 。

**husky配置**

- 官方文档：https://typicode.github.io/husky/#/?id=features 。
- 安装 `husky7`：`npx husky-init && npm install `。
  - 【可选】新增其他钩子：`npx husky add .husky/pre-commit "npm test" `。
- 修改命令 `echo "npm run lint" >> ./husky/pre-commit` 。
- 【可选】添加 `path环境变量`(`sourceTree`用)：`echo "export PATH=\"$(dirname $(which node)):\$PATH\"" > ~/.huskyrc` 。

**其他: node路径**

- `__dirname`：`node` 命令**所在文件**的路径 。
- `./xx`：**执行** `node` 命令时的路径 。
- **特例**：在 `require('./xx')`时，和 `__dirname` 一样，是所在文件的路径 。

### commitlint + git cz + changelog

- 提交代码 `git commit` 时自动校验 `commit message` 的格式，符合[Angular规范](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit#heading=h.greljkmo14y0)的才提交成功 。
- 使用 `git cz` 或 `git-cz` 代替 `git commit`，用**工作流**的方式添加符合规范的 `commit message ` 。
- 根据 `git log` 自动生成 `CHANGELOG.md`，需要注意：
  - `npm run changelog`:  在 `CHANGELOG.md` 内生成自上次 `npm run changelog` 后至 `package.json.version` 更新之间的 `git` 提交记录。只添加在最后，不覆盖文件。
    - PS：所以，使用 `npm run changelog` 更新记录前，需手动修改 `package.json` 中的 `version`，或使用 `npm version major|minor|patch` 自动更新 `package.json` 中的 `version`。
  - `npm run changelog:all`：在 `CHANGELOG.md` 内生成从项目创建开始到现在的 `git` 提交记录，会覆盖文件。

**实现过程**

**commitlint配置**

- 安装依赖：`npm install -D @commitlint/cli@13.1.0 @commitlint/config-conventional@13.1.0` 。
- 创建 `commitlint.config.js` 并写入：`less xxx > commitlint.config.js `。
  - 【可选】安装 `husky`：已安装可忽略 `npx husky-init && npm install `。
- 添加钩子：`npx husky add .husky/commit-msg 'npx --no-install commitlint --edit $1' `。
  - 【可选】测试效果：`echo "foo" | ./node_modules/.bin/commitlint` 。

**git-cz配置**

- 官方文档：https://github.com/streamich/git-cz 。
- 官方文档：https://github.com/commitizen/cz-cli/blob/master/README.md 。
- `commitizen` 默认使用 `cz-conventional-changelog`，只能显示英文，如要显示中文需使用 `git-cz` 并修改配置文件 `changelog.config.js` 。
- 安装 `commitizen` 和 `git-cz`：`npm install -g commitizen git-cz `。
- 初始化 `git-cz`：`commitizen init git-cz -D -E --force `。
- 创建 `changlog.config.js` 并写入：`less xxx > changelog.config.js `。

**changelog配置**

- 安装依赖：`npm install -D conventional-changelog-cli` 。
- 写入脚本到 `package.json`：
  - `changelog`：`npm set-script changelog "conventional-changelog -p angular -i CHANGELOG.md -s" `。
  - `changelog:all`：`npm set-script changelog:all "conventional-changelog -p angular -i CHANGELOG.md -s -r 0" `。