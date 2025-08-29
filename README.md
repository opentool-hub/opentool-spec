# OpenTool

Universal Protocol for Tool Invocation by AI Agents

## Features

1. The OpenTool protocol is inspired by OpenAPI, OpenRPC, and OpenAI's `function calling` examples.
2. Focused on enabling tool invocation capabilities for Agents and large language models.
3. Uses `JSON-RPC over HTTP` as the transport protocol, consisting of a Client on the Agent side and a Server on the Tool side:

   ```
   Agent
     ↓
   Client
     ⇘ HTTP (JSON-RPC)
   Server
     ↓
   Tool
   ```

## Note

* Differences from MCP:

  * MCP aims to cover a broader range of use cases, with Tools being only one part of it. OpenTool focuses specifically on tool invocation between Agents and large models.
  * MCP's `STDIO` mode requires the Client to launch the Server, which can result in unnecessary process spawns. In contrast, OpenTool allows manually starting the Server, which can then handle requests from multiple Clients.
  * MCP combines description and code in one place. OpenTool separates invocation and documentation: the Client calls a Server that invokes the Tool, while the documentation is a JSON file adhering to the OpenTool specification. As long as interfaces remain unchanged, the documentation does not need updates even if code changes.

* Therefore, OpenTool is better suited for tool invocation scenarios compared to MCP.

## Docs

* [OpenTool Schema Specification](opentool-specification-en.md)
* [OpenTool Client-Server Interaction](client-server-specification-en.md)

## Planning

* Support for more programming languages:

  * [x] [Dart SDK](https://github.com/opentool-hub/opentool-dart)
  * [ ] TypeScript
  * [ ] Java
  * [x] [C# .NET Framework](https://github.com/opentool-hub/opentool-csharp)
  * [ ] Python
  * [x] [LabVIEW 2017](https://github.com/opentool-hub/opentool-labview)
