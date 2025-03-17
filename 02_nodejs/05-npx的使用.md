# npx的使用

// npx 执行项目中的可执行文件

`npx` 是 `npm` 从 `v5.2.0` 开始新增了 `npx` 命令，`>=` 该版本会自动安装 `npx`，如果不能使用就手动安装一下：

```powershell
$ npm install -g npx
```

`npm` 只能管理包的依赖，`npx` 则可以快捷的运用包中的命令行工具和其他可执行文件，让项目内部安装的模块用起来更方便。

> 当执行 `$ npx <command>` 相关命令的时候，`npx` 会先本地找（可以是项目中的也可以是本机的）寻找这个 `command`。
>
> - 找到了：就用本地的版本
> - 没找到：直接下载最新版本（这里是在缓存里），完成命令要求
> - 使用完之后就会完全清除，不会在本机或项目留下任何东西
> - 这样就不会污染本机、永远使用最新版本的 `dependency

**使用场景**

`npm` 是安装第三方包的，但是安装之后，如何快捷的使用这些包？比如，项目内部安装了测试工具 `webpack`

```powershell
# 非全局安装
$ npm i webpack -D
```

如果要执行 `webpack` 的命令，要么直接运行下面的命令，要么只能在项目脚本的 `package.json` 中的 `scripts` 字段里面按下面这样配置一个新的命令：

```powershell
$ ./node_modules/.bin/webpack -v
```

类似这样的用法就很不方便有时候一些一次性调试，这个使用可以使用 `npx` 来做这件事，结果是一样的：

```powershell
$ npx webpack -v
```

详细作用：https://www.ruanyifeng.com/blog/2019/02/npx.html