# OpenTool Client 与 Server 通信规范

版本 1.0.0

本文档定义了一套基于 HTTP 和 JSON-RPC 2.0 协议的通信规范，适用于 Agent 与 OpenTool 系统中 Client 与 Server 的交互场景。

---

## 目录

* HTTP API

  * [1. 获取版本](#1-获取版本)
  * [2. 工具调用](#2-工具调用)
  * [3. 读取 OpenTool 描述文档](#3-读取-opentool-描述文档)
* SDK 接口

  * [4. Agent 与 OpenToolClient 的交互方式](#4-agent-与-opentoolclient-的交互方式)
  * [5. OpenToolServer 与 Tool 的交互方式](#5-opentoolserver-与-tool-的交互方式)
  * [6. 客户端异常处理](#6-客户端异常处理)

---

## HTTP API

### Base URL

```
https://<OPENTOOL_SERVER_HOST>:<PORT>/opentool
```

---

### 1. 获取版本

* **请求方法**：`GET`
* **请求路径**：`/version`
* **功能说明**：获取服务器当前版本号。

#### 请求参数

无

#### 响应参数

* **状态码**：`200 OK`
* **响应体**：

```json
{
  "version": "1.0.0"
}
```

| 字段名     | 类型     | 说明                 |
|---------|--------|--------------------|
| version | string | 服务器版本号，格式为 "x.y.z" |

---

### 2. 工具调用

* **请求方法**：`POST`
* **请求路径**：`/call`
* **功能说明**：使用 JSON-RPC 2.0 协议调用指定工具方法。

#### 请求体结构

```json
{
  "jsonrpc": "2.0",
  "method": "<FUNCTION_NAME>",
  "params": {},
  "id": "<CALL_ID>"
}
```

| 字段名     | 类型     | 说明             |
|---------|--------|----------------|
| jsonrpc | string | 固定为 "2.0"      |
| method  | string | 待调用的函数名称       |
| params  | object | 参数对象，可选        |
| id      | string | 唯一调用 ID，用于匹配响应 |

#### 成功响应体

```json
{
  "jsonrpc": "2.0",
  "result": {},
  "error": null,
  "id": "<CALL_ID>"
}
```

#### 失败响应体

```json
{
  "jsonrpc": "2.0",
  "result": {},
  "error": {
    "code": 500,
    "message": "<ERROR_MESSAGE>"
  },
  "id": "<CALL_ID>"
}
```

| 字段名     | 类型          | 说明                |
|---------|-------------|-------------------|
| jsonrpc | string      | 固定为 "2.0"         |
| result  | object      | 成功结果              |
| error   | object/null | 失败错误信息，若成功则为 null |
| id      | string      | 请求对应的唯一 ID        |

---

### 3. 读取 OpenTool 描述文档

* **请求方法**：`GET`
* **请求路径**：`/load`
* **功能说明**：返回当前服务端加载的 OpenTool 描述文档。

#### 请求参数

无

#### 响应参数

* **状态码**：`200 OK`
* **响应体**：

```json
{}
```

返回完整的 OpenTool JSON 结构。若无描述信息，则返回空对象。

详见：《[OpenTool 规范文档](opentool-specification-cn.md)》。

---

## SDK 接口

### 4. Agent 与 OpenToolClient 的交互方式

```
interface Client {
  // 获取服务端版本信息
  Version version();

  // 发起工具调用，传入 FunctionCall，返回 ToolReturn
  ToolReturn call(FunctionCall functionCall);

  // 加载工具描述信息，可返回 null
  OpenTool? load();
}
```

#### FunctionCall（函数调用结构）

```
class FunctionCall {
  String id;
  String name;
  Map<String, Any> arguments;

  constructor(id: String, name: String, arguments: Map<String, Any>);

  static FunctionCall fromJson(Map<String, Any> json);

  Map<String, Any> toJson();
}
```

#### ToolReturn（工具返回结构）

```
class ToolReturn {
  String id;
  Map<String, Any> result;

  constructor(id: String, result: Map<String, Any>);

  static ToolReturn fromJson(Map<String, Any> json);

  Map<String, Any> toJson();
}
```

---

### 5. OpenToolServer 与 Tool 的交互方式

```
interface Tool {
  // 调用指定方法
  Map<String, Any> call(String name, Map<String, Any>? arguments);

  // 加载工具描述信息，可返回 null
  OpenTool? load();
}
```

开发者需实现该接口，实现工具方法的实际逻辑。

---

### 6. 客户端异常处理

客户端在调用 OpenTool Server 的过程中，可能会遇到以下几类异常，建议实现统一异常结构，支持序列化输出以便诊断。

```
// 响应结果为空
class ResponseNullException implements Exception {
  int code;
  String message = "Response is null";
}

// 错误字段为空
class ErrorNullException implements Exception {
  int code;
  String message = "Error is null";
}

// 鉴权失败
class OpenToolServerUnauthorizedException implements Exception {
  int code = 401;
  String message = "Please check API Key is VALID or NOT";
}

// 服务不可访问
class OpenToolServerNoAccessException implements Exception {
  int code = 404;
  String message = "Please check OpenTool Server is RUNNING or NOT";
}

// 工具调用错误
class OpenToolServerCallException implements Exception {
  String message;
  constructor(message: String)
}
```

这些异常类型应支持 JSON 序列化方法，如 `toJson()`，用于调试记录与对外抛出结构化错误。
