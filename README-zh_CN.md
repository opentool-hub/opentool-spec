# OpenTool

AI Agent工具调用通用协议

## Features

1. OpenTool协议定义来源于OpenAPI、OpenRPC和OpenAI的 `function calling`示例。
2. 专注于Agent与大模型的工具调用能力
3. 使用`JSON-RPC over HTTP`作为传输协议，包含Agent端的Client、工具端的Server
  ```
  Agent
    ↓
  Client
    ⇓ HTTP (JSON-RPC)
  Server
    ↓
  Tool
  ```

## Note
- 与MCP的差异
    - MCP试图覆盖更多场景，Tools只是其中一个部分；OpenTool专注于Agent与大模型的工具调用能力。
    - MCP的`STDIO`模式需要Client拉起Server，导致无必要的多次进程被拉起；而OpenTool的Server允许手工拉起，多个Client请求
    - MCP的描述与代码合在一起；OpenTool倾向于调用与文档相分离，调用Server来具体调用Tool，文档为OpenTool规范的JSON文件，代码实现变更后，只要接口不变，文档不变。
- 也即，OpenTool设计出来相比MCP更适合用于工具调用的场景

## 文档

- [OpenTool格式规范](opentool-specification-cn.md)
- [OpenTool Client-Server交互文档](client-server-specification-cn.md)

## 计划

* 支持更多的语言
    * [x] [Dart SDK](https://github.com/opentool-hub/opentool-dart)
    * [ ] TypeScript
    * [ ] Java
    * [x] [C# .NET Framework](https://github.com/opentool-hub/opentool-csharp)
    * [ ] Python
    * [x] [LabVIEW 2017](https://github.com/opentool-hub/opentool-labview)