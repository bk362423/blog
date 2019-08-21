# 基础

::: warning ⚠️注意事项
所有 Cloudbase CLI 命令均在 `.tcbrc.json` 文件所在目录执行。
:::

云函数是一段运行在云端的代码，无需管理服务器，在开发工具内编写、一键上传部署即可运行后端代码。云开发中的云函数可让用户将自身的业务逻辑代码上传，并通过云开发的调用触发函数，从而实现后端的业务运作。

## 查看函数

您可以使用下面的命令列出所有云函数，查看函数的基本信息：

```sh
tcb functions:list
```

您会得到类似下面的输出：

```sh
┌─────────┬───────────┬───────────┬─────────────────────┬─────────────────────┬──────────┐
│    Id   │   Name    │  Runtime  │       AddTime       │       ModTime       │  Status  │
├─────────┼───────────┼───────────┼─────────────────────┼─────────────────────┼──────────┤
│ lam-xxx │   hello   │ Nodejs8.9 │ 2019-08-20 10:27:23 │ 2019-08-20 10:27:23 │ 部署完成  │
├─────────┼───────────┼───────────┼─────────────────────┼─────────────────────┼──────────┤
│ lam-xxx │   test    │   Java8   │ 2019-08-19 20:31:52 │ 2019-08-19 21:15:39 │ 部署完成   │
├─────────┼───────────┼───────────┼─────────────────────┼─────────────────────┼──────────┤
│ lam-xxx │    db     │    Php7   │ 2019-08-09 15:09:09 │ 2019-08-09 15:41:41 │ 部署完成   │
└─────────┴───────────┴───────────┴─────────────────────┴─────────────────────┴──────────┘
```

### 指定返回条数和便宜量

默认情况下，`functions:list` 命令只会列出前 20 个函数，如果你的函数较多，需要列出其他的函数，你可以通过下面的选项指定命令返回的数据长度以及数据的偏移量：

```sh
-l, --limit <limit>    返回数据长度，默认值为 20
-o, --offset <offset>  数据偏移量，默认值为 0
```

如：

```sh
# 返回前 10 个函数的信息
tcb functions:list -l 10
# 返回第 3 - 22 个函数的信息（包含 3 和 22）
tcb functions:list -l 20 -o 2
```

## 部署函数

> **注意** `functions:deploy` 命令必须在包含 `tcbrc.json` 配置文件的项目根目录下执行。

您可以通过下面的命令部署函数：

```sh
functions:deploy <functionName>
```

`functions:deploy` 会读取 `tcbrc.json` 文件中指定函数的配置，`functions:deploy` 命令会完成以下几项工作：

1. 将函数打包成压缩文件，并上传函数代码。
2. 部署函数配置，包括超时时间、网络配置等。
3. 部署函数触发器。

举个例子 🌰：

```sh
# 部署 app 函数
tcb functions:deploy app
```

使用 `functions:deploy` 时，`functionName` 选项是可以省略的，当 `functionName` 省略时，Cloudbase CLI 会部署配置文件中的全部函数：

```sh
# 部署配置文件中的全部函数
tcb functions:deploy
```

### 覆盖同名函数

部署函数时，很可能会遇到已经存在同步函数的情况，此时 Cloudbase CLI 会终止部署，询问是否覆盖同名函数，您可以选择覆盖或者终止部署。

如果您确定要覆盖可能存在的同名函数，您可以在命令后附加 `--force` 选项指定 Cloudbase CLI 覆盖已存在的函数。

```sh
tcb functions:deploy dev --force
```

**注意**：覆盖函数时，也会覆盖函数的配置和触发器。

## 触发函数

您可以在本地通过 Cloudbase CLI 直接触发您的云函数：

```sh
# 触发 app 函数
tcb functions:invoke app

# 触发配置文件中的全部函数
tcb functions:invoke
```

## 查看函数详情

前面提到的 `functions:list` 命令只能查看函数的简单信息，如果您想查看函数的详细信息，您可以使用下面的命令：

```sh
# 查看 app 函数的详情
tcb functions:detail app

# 查看配置文件中的所有函数的详情
tcb functions:detail
```

输出

```sh

状态：部署完成
代码大小（B）：1695
环境变量(key=value)：key=value
函数名称：test
执行方法：index.main
内存配置(MB)：256
修改时间：2019-08-19 21:15:39
环境 Id：dev-xxx
运行环境：Nodejs8.9
超时时间(S)：20
网络配置：无
触发器：
myTrigger：{"cron": "0 0 2 1 * * *"}
函数代码（Java 函数以及入口大于 1 M 的函数不会显示）：
'use strict';
exports.main = async (event, context, callback) => {
    console.log(event);
    return 'hello world'
};

```

## 删除函数

您可以通过下面的命令删除函数

```sh
# 删除 app 函数
tcb functions:delete app

# 删除配置文件中的所有的函数
tcb functions:delete
```

## 复制函数

您可以通过下面的命令快速复制一个已经存在的函数：

```sh
# 复制 app 函数为 app2 函数
functions:copy app app2
```

使用 `functions:copy` 命令时需要指定原函数名称以及复制后新的函数名称。当前环境 Id 和目标环境环境 Id 是可选的，如果没有指定目标环境 Id，函数会被复制到当前环境中。

如果新函数的名称已经存在，则复制操作会被终止。如果你想要覆盖已经存在的函数，你可以使用下面的命令

### 覆盖同名函数

```sh
# 复制 app 函数为 app2 函数，如果 app2 函数存在，覆盖已经存在的 app2 函数
functions:copy app app2 --force
```

**注意**：复制函数操作不会复制函数的函数的触发器。