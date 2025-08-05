# OpenTool Server 命令行建议做法

## OpenTool Server CLI

如果你的OpenTool Server已经到达Release状态，那么建议封装为CLI形式发布，原因：

1. 足够简单
2. 一些参数可以开放给用户
3. 方便（特别是Linux Server）运维人员后台运行
4. 提供给OpenTool Daemon常驻程序管理

## 工具命名

- 工具名称文案，采用`大写蛇形命名法`，例如：`UPPER_SNAKE_CASE`
- 工具执行的命令行，采用`小写蛇形命名法`，例如：`snake_case`

## 建议入参

### 1. 版本号

- （可选）用于OpenTool Client获取版本号，建议在代码中预先指定

```
-- version -v : OpenTool Server Version. Default: {YOUR_VERSION}
```

### 2. 工具端口

- （可选）用于指定OpenTool Server的HTTP端口号，建议在代码中预先指定

```
-- toolPort -t : OpenTool Server Port. Default: {YOUR_PORT}
```

### 3. 鉴权访问

- （可选）用于限定携带有ApiKey的OpenTool Client请求

```
-- apiKeys -k : OpenTool Server APIKEY, allow array, as: --apiKeys KEY_A --apiKeys KEY_B
```

### 4. 其他

1. 需要避免上述缩写的单字母（-v -t -k）
2. 一些建议的参数命名：
  - `-- host -h`
  - `-- port -p`
  - `-- ssl -s`
  - `-- id -i`
  - `-- access -a`
  - `-- refresh -r`

## 命令行帮助文案

### 1. 命令执行

假设：执行的工具名称为 `mock_util_tool`，那么执行

```
$./mock_util_tool -h
```
```
$./mock_util_tool --help
```

打印出来的效果建议为：

```
MOCK_UTIL_TOOL OpenTool Server CLI (1.0.0) - OpenTool Server implement by MOCK_UTIL_TOOL.

Usage: mock_util_tool <command> [options]

Available commands:

Command: start
  -v, --version     Version, Default: 1.0.0
  -t, --toolPort    OpenTool Server Port, Default: 9639
  -k, --apiKeys     API Keys, allow array, as: --apiKeys KEY_A --apiKeys KEY_B

Global options:

-h, --help    Show help information
```

### 2. 无参数执行情况

- 如果命令行中，**没有**必填项，建议按照默认参数直接执行
- 如果命令行中，**具有**必填项，建议打印帮助文档，与`--help`和`-h`效果一致