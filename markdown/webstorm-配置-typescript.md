# WebStorm 配置 TypeScript

WebStorm 可以开发 TypeScript，同时支持自动编译为 JavaScript 文件，具体操作如下：

1. 前往 [Node.js](https://nodejs.org/zh-cn/) 官网下载安装 Node.js

2. 下载安装新版本的 [WebStorm](https://www.jetbrains.com/webstorm/)

3. 使用 node 的 npm 命令安装 TypeScript 编译器：`npm install typescript -g` 安装好，在命令行可以查看编译器的版本：`tsc --version`

4. 打开 WebStorm，新建一个空白项目

5. 在 WebStorm 中右击项目名，选择 `new -> tsconfig.json File`，创建 `tsconfig.json` 文件

6. 打开 WebStorm，为 TypeScript 文件更改编译设置，`File -> Settings -> File Watchers -> TypeScript`，这里我们需要选择 `TypeScript`，但是 `File Watchers` 下默认是不存在的。需要点击右侧“ + ”号，选择 `<custom>`。

7. 在弹出的 `New File Watcher` 中，填写 `Name`，在 `Wached Files` 中的 `File type` 选择 `TypeScript`，`Watcher Settings` 中填写如下内容：

    >**Program（程序）：**`tsc 的路径`  
    >**Arguments（参数）：**`--sourcemap --target "ES6"`  
    >**Output paths to refresh（要刷新的输出路径）：**`$FileNameWithoutExtension$.js:$FileNameWithoutExtension$.js.map`

    **Working directory（工作目录）：**`$FileDir$`

8. 同时，需要设置 TypeScript 自动编译，在 `Setting -> Language & Frameworks` 中的 `TypeScript` 勾选 `Compiler` 中的 `Recomplie on changes`。

9. 之后修改 TypeScript 文件时，WebStorm 就会自动编译
