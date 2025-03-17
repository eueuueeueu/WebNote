# npm和pnpm

## npm

**npm：官方的nodejs的包管理工具(Node Package Manager)**

### 常用命令：

快速初始化一个package.json：

```js
// npm init -y 快速初始化一个package.json
```

依赖：开发依赖(devDependencies) 和 生产依赖(dependencies)

环境：开发环境 和 生产环境

安装依赖：

```js
// npm install <package-name> -S 记录在package.json的dependencies中       【生产依赖】
// npm install <package-name> -D 记录在package.json的devDependencies中    【开发依赖】
```

从package.json中移除包：

```js
// npm uninstall <package-name> 从package.json中移除包
```

## pnpm

**高性能的nodeja的包管理工具**

### 选择原因：

> 使用 npm 时，依赖每次被不同的项目使用，都会重复安装一次。  而在使用 pnpm 时，依赖会被存储在内容可寻址的存储中，所以：
>
> 1. 如果你用到了某依赖项的不同版本，只会将不同版本间有差异的文件添加到仓库。 例如，如果某个包有 100 个文件，而它的新版本只改变了其中 1 个文件。那么 `pnpm update` 时只会向存储中额外添加 1 个新文件，而不会因为单个改变克隆整个依赖。
> 2. 所有文件都会存储在硬盘上的某一位置。 当软件包被被安装时，包里的文件会硬链接到这一位置，而不会占用额外的磁盘空间。 这允许你跨项目地共享同一版本的依赖。
>
> 因此，你在磁盘上节省了大量空间，这与项目和依赖项的数量成正比，并且安装速度要快得多！

### 常用命令：

| npm 命令                        | pnpm 等效                                    |
| :------------------------------ | :------------------------------------------- |
| `npm init -y`                   | `pnpm init`                                  |
| `npm install <package-name> -S` | `pnpm add <package-name>`                    |
| `npm install <package-name> -D` | `pnpm add <package-name> -D`                 |
| `npm run <cmd>`                 | `pnpm <cmd>`                                 |
| `npm uninstall <package-name>`  | `pnpm uninstall/rm/remove/un <package-name>` |

```js
// node.js => node npm npx corepack(低版本不存在)
// 使用node运行js => node /path/to/xxx.js
// 为什么在系统任意位置可以使用node、npm... 因为环境变量PATH
// npm命令 https://npmjs.com
// 安装第三方的包到本地的node_modules目录：npm install <package-name>
// 在js中我们可以直接通过名字导入node_modules的包 那是因为自有查找策略
// 依赖：开发依赖(devDependencies) 和 生产依赖(dependencies)
// 环境：开发环境 和 生产环境
// npm init -y 快速初始化一个package.json
// npm install <package-name> -S 记录在package.json的dependencies中       【生产依赖】
// npm install <package-name> -D 记录在package.json的devDependencies中    【开发依赖】
// npm uninstall <package-name> 从package.json中移除包
// npm install 作用:根据package.json中记录的包和对应的版本进行安装
// npm：官方的nodejs的包管理工具(Node Package Manager)

// pnpm 高性能的nodeja的包管理工具
// npm init -y  ==>  pnpm init
// npm install <package-name> -S  ==>  pnpm add <package-name>
// npm install <package-name> -D  ==>  pnpm add <package-name> -D
// npm install  ==>  pnpm install
// npm uninstall <package-name>  ==>  pnpm remove/rm/uninstall <package-name>
```











