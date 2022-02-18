# 前言
在多人合作开发的项目时，规范项目代码风格和`git`提交记录，有利于我们更容易的读懂他人写的代码，遇到问题时，清晰的`git`提交记录也方便我们快速的定位问题。
## Eslint+Prettier
`Eslint`可以通过静态分析`javascript`代码中的问题，并自动解决这些问题，`Prettier`则是主要用来格式化代码，让你的代码更加规范和美观。但是两者在配置上会有一些冲突，我们通过额外的扩展来解决这些冲突。

首先，我们来下载这些包`yarn add eslint prettier eslint-config-airbnb-base eslint-config-prettier eslint-plugin-prettier eslint-plugin-react --dev`, 然后在你的项目根目录新增`.eslintrc.json`配置文件，并加上以下配置：
```json
{
  "extends": [
    "airbnb-base",
    "plugin:react/recommended",
    "plugin:prettier/recommended"
  ],
  "rules": {
    "prettier/prettier": "error"
  }
}
```
- [eslint-config-airbnb-base](https://www.npmjs.com/package/eslint-config-airbnb-base): 目前最流行的`javascript`代码规范，引入它避免我们手动去配置每一条规则，类似的还有[standard](https://www.npmjs.com/package/eslint-config-standard)。
- [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier): 关闭`Eslint`不需要的或者可能与`Prettier`有冲突的规则。
- [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier): 将`Prettier`作为`Eslint`的规则去运行，配置`"prettier/prettier": "error"`，可以通过`Eslint`在你不符合`Prettier`的要求的代码下划红色横线。推荐阅读[Integrating with Linters](https://prettier.io/docs/en/integrating-with-linters.html)。
- [eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react): 可以使用`Eslint`检查项目中的`react`语法。事实上如果你使用的是[eslint-config-airbnb](https://www.npmjs.com/package/eslint-config-airbnb)，而不是`airbnb-base`，则不需要手动引入该插件，因为`airbnb`已经包含了这些。

同样的，在根目录新增`.eslintignore`和`.prettierignore`文件，可以忽略需要lint和格式化的文件，新增`.prettierrc.json`可以修改`Prettier`的一些默认规则，至于其中一些具体规则，或者怎样与编辑器集成，可以参考[Eslint](https://eslint.org/docs/user-guide/integrations)和[Prettier](https://prettier.io/docs/en/editors.html)官网来进行配置。
## husky+commitlint+lint-staged
这三个包主要是用来在客户端通过使用`git hooks`来规范提交的代码。
- [husky](https://xxtypicode.github.io/husky/#/?id=features): 是一个为`git`客户端增加 hook 的工具，比如`pre-commit`钩子就会在你执行`git commit`的触发。我们可以在`pre-commit`中实现一些比如 lint 检查、单元测试、代码美化等操作。
- [lint-staged](https://github.com/okonet/lint-staged): 过滤出`git`暂存区的文件，也就是执行`git add [file]`命令后的文件。这个可以让我们只针对当前提交的代码进行一些检查，单元测试等操作，避免改动整个项目，并且节约时间。
- [commitlint](https://github.com/conventional-changelog/commitlint): 规范化`commit message`，通过`commit-msg`钩子,来运行`yarn commitlint`命令检测提交的 message 。

实践：
1. 安装包`yarn add husky @commitlint/config-conventional @commitlint/cli lint-staged -D`。
2. 运行`yarn husky install`会在你的项目根目录创建`.husky`文件夹，用来存放`git hooks`。
3. 如果你使用的是`yarn`，并且版本是**v1**，则在`package.json`添加以下配置，这个命令将会在每次运行`yarn install`之后执行，保证每个新的客户端都会创建相应的`git hooks`。
   ```json
    {
        "scripts": {
            "prepare": "husky install"
        }
    }
   ```
4. 新增`pre-commit`钩子,钩子内执行`package.json`的脚本。`yarn husky add .husky/pre-commit "yarn run lint"`。
5. 修改`package.json`，这样就可以在执行`git commmit`时，对 git 暂存区的文件，执行 prettier 和 lint 操作。
   ```json
   {
        "scripts": {
            "prepare": "husky install",
            "lint": "lint-staged"
        },
        "lint-staged": {
            "src/**/*{.js,.jsx}": [
                "prettier --write",
                "eslint --fix"
            ]
        }
   }
   ```
6. 新增`commit-msg`钩子，对 git 提交的 message 进行检测,`yarn husky add .husky/commit-msg 'yarn commitlint --edit $1'`。并且新增`commitlint.config.js`配置文件，具体配置信息可以去 commitlint github主页查看。
   ```javascirpt
   module.exports = { extends: ["@commitlint/config-conventional"] };
   ```
