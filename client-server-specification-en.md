# OpenTool Client-Server Communication Specification

Version 1.0.0

This document defines a communication specification based on HTTP and JSON-RPC 2.0 protocol, applicable to the interaction between Agents and the OpenTool system, including Client and Server sides.

---

## Table of Contents

* HTTP API

  * [1. Get Version](#1-get-version)
  * [2. Call Tool](#2-call-tool)
  * [3. Load OpenTool Description](#3-load-opentool-description)
* SDK Interfaces

  * [4. Interaction Between Agent and OpenToolClient](#4-interaction-between-agent-and-opentoolclient)
  * [5. Interaction Between OpenToolServer and Tool](#5-interaction-between-opentoolserver-and-tool)
  * [6. Client-Side Exception Handling](#6-client-side-exception-handling)

---

## HTTP API

### Base URL

```
https://<OPENTOOL_SERVER_HOST>:<PORT>/opentool
```

---

### 1. Get Version

* **Method**: `GET`
* **Path**: `/version`
* **Description**: Retrieve the current server version.

#### Request Parameters

None

#### Response

* **Status Code**: `200 OK`
* **Body**:

```json
{
  "version": "1.0.0"
}
```

| Field   | Type   | Description                    |
|---------|--------|--------------------------------|
| version | string | Server version in x.y.z format |

---

### 2. Call Tool

* **Method**: `POST`
* **Path**: `/call`
* **Description**: Invoke a specified tool method using the JSON-RPC 2.0 protocol.

#### Request Body

```json
{
  "jsonrpc": "2.0",
  "method": "<FUNCTION_NAME>",
  "params": {},
  "id": "<CALL_ID>"
}
```

| Field   | Type   | Description                        |
|---------|--------|------------------------------------|
| jsonrpc | string | Must be "2.0"                      |
| method  | string | Name of the function to call       |
| params  | object | Parameters object, optional        |
| id      | string | Unique call ID used to match reply |

#### Success Response

```json
{
  "jsonrpc": "2.0",
  "result": {},
  "error": null,
  "id": "<CALL_ID>"
}
```

#### Error Response

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

| Field   | Type        | Description               |
|---------|-------------|---------------------------|
| jsonrpc | string      | Always "2.0"              |
| result  | object      | Success result            |
| error   | object/null | Error information or null |
| id      | string      | Matching request ID       |

---

### 3. Load OpenTool Description

* **Method**: `GET`
* **Path**: `/load`
* **Description**: Return the currently loaded OpenTool description document from the server.

#### Request Parameters

None

#### Response

* **Status Code**: `200 OK`
* **Body**:

```json
{}
```

Returns a complete OpenTool JSON structure. Returns an empty object if no description is available.

See: [OpenTool Specification Document](opentool-specification-cn.md)

---

## SDK Interfaces

### 4. Interaction Between Agent and OpenToolClient

```
interface Client {
  Version version(); // Get server version
  ToolReturn call(FunctionCall functionCall); // Invoke tool call
  OpenTool? load(); // Load tool description, can return null
}
```

#### FunctionCall Structure

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

#### ToolReturn Structure

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

### 5. Interaction Between OpenToolServer and Tool

```
interface Tool {
  Map<String, Any> call(String name, Map<String, Any>? arguments); // Invoke specific method
  OpenTool? load(); // Load tool description, can return null
}
```

The developer must implement this interface to provide the actual logic for tool methods.

---

### 6. Client-Side Exception Handling

During interactions with the OpenTool Server, clients may encounter the following exceptions. It is recommended to implement a unified structure with JSON serialization support for debugging and external error reporting.

```
// Response is null
class ResponseNullException implements Exception {
  int code;
  String message = "Response is null";
}

// Error field is null
class ErrorNullException implements Exception {
  int code;
  String message = "Error is null";
}

// Unauthorized
class OpenToolServerUnauthorizedException implements Exception {
  int code = 401;
  String message = "Please check API Key is VALID or NOT";
}

// No access to server
class OpenToolServerNoAccessException implements Exception {
  int code = 404;
  String message = "Please check OpenTool Server is RUNNING or NOT";
}

// Tool call error
class OpenToolServerCallException implements Exception {
  String message;
  constructor(message: String)
}
```

These exception classes should implement a `toJson()` method to support structured logging and error output.
