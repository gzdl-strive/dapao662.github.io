---
title: 代码规范
date: 2022-03-22 11:01:24
toc: true
---

### eslint + prettier + stylelint
好的代码必然是规范出来的，所以代码格式化校验工具，在现代开发中，是很重要的

安装我们需要的包
```bash
npm install -D @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint eslint-config-prettier eslint-plugin-prettier eslint-plugin-vue prettier stylelint stylelint-config-standard-scss stylelint-scss
```

- 在根目录下新建.eslintignore
```bash
# 忽略文件和文件夹
*.sh
node_modules
*.md
*.woff
*.ttf
.vscode
.idea
dist
/public
/docs
.husky
.local
/bin
.json
Dockerfile
```

- 在根目录下新建.eslintrc.js
```js
module.exports = {
  root: true,
  env: {
    browser: true,
    node: true
  },
  parser: 'vue-eslint-parser',
  parserOptions: {
    parser: '@typescript-eslint/parser'
  },
  plugins: ['@typescript-eslint', 'prettier'],
  extends: [
    'plugin:@typescript-eslint/recommended',
    'plugin:vue/vue3-recommended',
    'prettier',
    'plugin:prettier/recommended'
  ],
  // 可以自定义一些校验规则
  rules: {},
  overrides: [
    {
      files: ['*.vue'],
      rules: {
        indent: 'off'
      }
    }
  ]
};
```

- 根目录下新建.prettierignore和.prettierrc.js
```bash
# 其他类型文件
*.js
*.ts
*.jpg
*.woff
# 测试和打包目录
/dist/
/node_modules/
```
```js
module.exports = {
  semi: true,
  eslintIntegration: true,
  singleQuote: true,
  endOfLine: 'crlf',
  tabWidth: 2,
  trailingComma: 'none',
  bracketSpacing: true,
  arrowParens: 'avoid'
}
```

- 根目录下新建.styleintignore和.stylelintrc.js
```bash
# 其他类型文件
*.js
*.ts
*.jpg
*.woff
# 测试和打包目录
/dist/
/node_modules/
```
```js
module.exports = {
  "customSyntax": "postcss-html",
  "extends": ["stylelint-config-standard-scss"],
  "plugins": ["stylelint-scss"],
  // 可以自定义一些校验规则
  "rules": {},
  "ignoreFiles": ['**/*.js', '**/*.jsx', '**/*.tsx', '**/*.ts']
}
```

### husky
主要作用就是关联git的钩子函数，在执行git hooks时进行自定义操作，比如提交前执行eslint校验

**install**
```js
// && 连接符在vscode中会报错，建议在windows的powershell执行
npx husky-init && npm install
```
执行完成后，项目根目录下会多处理.husky文件夹

内部的_文件夹我们在此无需关心，`pre-commit`文件便是在git提交前会执行的操作,我们可以在当前目录创建钩子文件来完成我们想要的操作
**我们想要在提交前进行eslint校验代码**
在package.json中添加脚本
```json
{
  "script": {
    "eslint": "eslint ./src --fix",
  }
}
```
修改pre-commit文件即可
```bash
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npm run eslint #++ 校验所有的.js .ts .vue文件，并修复可自动修复的问题
git add . #++ 用于将自动修复后改变的文件添加到暂存区
```
那么就会执行脚本的eslint命令，即eslint ./src --fix会自动进行校验并修复后提交到暂存区

### lint-staged
>作用：通过配置husky会在提交之前对代码进行检查，但是当我们项目代码越来越多，每次提交前校验所有代码显然是不显示的。**所以需要一个方法每次只检查新增或修改的文件。**

lint-staged的作用就是对暂存区的文件执行lint，可以让我们每次提交时只校验自己修改的文件
```bash
npm install lint-staged --save-dev
```
```json
// package.json
...
"scripts": {
    ...
    "lint-staged": "lint-staged"
},
"lint-staged": {
  "src/**/*.{ts,vue}": [
    "eslint --fix",
    "git add ."
  ],
  "*.{vue,scss}": [
    "stylelint --fix --custom-syntax postcss-html",
    "git add ."
  ]
}
```

**修改husky**
```sh
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

# eslint . --ext .js,.ts,.vue --fix
# git add .
# exit 1
npm run lint-staged
```

### 问题
#### 1、prettier和stylelint冲突问题
当我们配置了prettier与stylelint，template里面标签的行内样式末尾分号规则有冲突
具体如下：
![1](/assets/vueImg/elementplus2.png "stylelint规则")
查看stylelint官网的规则：希望我们在行内样式的末尾是一个分号
![1](/assets/vueImg/elementplus2-2.png "stylelint规则")

但是，当我们在末尾加上`;`prettier又提示我们：
想要我们删掉末尾的`;`
![1](/assets/vueImg/elementplus3.png "prettier规则")

冲突产生了，所以我们需要修改`.stylelintrc.js`
在rules中添加上规则
```js
module.exports = {
  //...
  "rules": {
    //...
    "declaration-block-trailing-semicolon": null,
  }
}
```
这样我们就能忽略stylelint对末尾分号的校验

### 2、:deep不能被stylelint失败的问题
![1](/assets/vueImg/elementplus4.png ":deep")

**同样的，在stylelintrc.js文件中**
```js
module.exports = {
  //...
  "rules": {
    'selector-pseudo-class-no-unknown': [
      true,
      {
        ignorePseudoClasses: ['deep']
      }
    ],
  }
}
```