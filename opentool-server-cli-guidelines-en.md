# OpenTool Server Command-Line Interface Guidelines

## OpenTool Server CLI

If your OpenTool Server has reached the release stage, it is recommended to package it as a CLI for the following reasons:

1. Simplifies usage
2. Exposes configuration options to users
3. Convenient for operations (especially on Linux servers)
4. Enables management by the OpenTool Daemon

## Tool Naming Conventions

* Tool display name: use `UPPER_SNAKE_CASE`, e.g., `MOCK_UTIL_TOOL`
* Executable command name: use `snake_case`, e.g., `mock_util_tool`

## Recommended CLI Options

### 1. Version

* (Optional) Used by OpenTool Client to query the version; recommended to be hardcoded in the implementation

```
--version -v : OpenTool Server Version. Default: {YOUR_VERSION}
```

### 2. Tool Port

* (Optional) Used to specify the HTTP port of the OpenTool Server; recommended to be preset

```
--toolPort -t : OpenTool Server Port. Default: {YOUR_PORT}
```

### 3. API Key Authentication

* (Optional) Restricts OpenTool Client requests to those that include an allowed API key

```
--apiKeys -k : OpenTool Server APIKEY, allow array, as: --apiKeys KEY_A --apiKeys KEY_B
```

### 4. Reserved Short Options

To avoid conflicts, do not reuse the short forms `-v`, `-t`, and `-k`. Here are some suggestions for other arguments:

* `--host -h`
* `--port -p`
* `--ssl -s`
* `--id -i`
* `--access -a`
* `--refresh -r`

## Command-Line Help Format

### 1. Command Execution

Assuming the tool is named `mock_util_tool`, execute:

```
$ ./mock_util_tool -h
```

```
$ ./mock_util_tool --help
```

The recommended output format:

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

### 2. Behavior When No Parameters Are Provided

* If there are **no required** parameters, the tool should run using default values
* If there **are required** parameters, the tool should print the help message (same as `--help` or `-h`)
