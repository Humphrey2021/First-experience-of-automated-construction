# 自动化构建初体验

> 通过一个简单的`sass`案例，来体验一下自动化构建的便捷之处

## 初始化一个项目
```shell
mkdir automation
cd automation
# 初始化 package.json 文件
yarn init --yes
# 安装 sass 模块（安装成开发依赖）
yarn add sass --dev
```
我们可以先创建一个`scss`文件，编写对应的`scss`代码
```shell
# 运行 sass 命令
./node_modules/.bin/sass scss/main.scss css/style.css
```
为了项目的更好维护，我们可以用到`npm scripts`，来定义一些与项目相关的脚本命令

- 打开`package.json`文件，添加`"scripts"`
```json
{
  "scripts": {
    "build": "sass scss/main.scss css/style.css"
  },
}
```
可以新增一个命令，这里可以直接写`sass`，而不是路径，因为会自动去找`node_modules/.bin`里的命令
这样我们就只需要运行`yarn build`就可以转换`scss`文件

然后我们可以再安装一个`browser-sync`模块，用来为项目启动一个测试服务器。
```shell
# 同样将其安装为开发依赖
yarn add browser-sync --dev
```
继续编写`package.json`文件,添加`serve`命令
在启动服务之前，我们需要先进行构建命令，所以需要添加一个钩子方法，`preserve`,用来在`serve`之前先执行某些命令
```json
{
  "scripts": {
    "build": "sass scss/main.scss css/style.css",
    "preserve": "yarn build",
    "serve": "browser-sync ."
  },
}
```
考虑到每次编写`scss`文件，都需要重新构建一下，那么不妨我们来监听`scss`的变化,添加`--watch`

然后借助于`npm-run-all`模块来实现同时执行多个命令
```shell
yarn add npm-run-all --dev
```
继续编写`package.json`文件
```json
{
  "scripts": {
    "build": "sass scss/main.scss css/style.css --watch",
    "serve": "browser-sync .",
    "start": "run-p build serve"
  },
}
```
虽然我们监听了`scss`文件，但是更改之后，`borwser-sync`并不会监听到这些文件的变化，也就无法让浏览器自动刷新，我们可以为`browser-sync`添加`--files`参数
```json
{
  "scripts": {
    "build": "sass scss/main.scss css/style.css --watch",
    "serve": "browser-sync . --files \"css/*.css\"",
    "start": "run-p build serve"
  },
}
```
到这，我们就可以随意修改`sass`文件，保存后，浏览器会自动更新样式。
