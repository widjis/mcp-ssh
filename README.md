# SSH MCP Server

A Model Context Protocol (MCP) server that provides SSH functionality including file copying between servers, command execution, and file management.

## Features

- 🔐 **SSH Connection Management**: Connect to multiple SSH servers with connection pooling
- 📁 **File Operations**: Copy files between local and remote servers, or between remote servers
- 🖥️ **Command Execution**: Execute commands on remote servers
- 📋 **File Listing**: List files and directories on local or remote systems
- ℹ️ **File Information**: Get detailed file information (size, permissions, timestamps)
- 🔑 **Authentication**: Support for password and private key authentication

## Installation

```bash
npm install
npm run build
```

## Usage

### Claude Desktop Integration

To use this SSH MCP server with Claude Desktop, you need to configure it in your Claude Desktop settings.

#### 1. Build the Server
```bash
npm install
npm run build
```

#### 2. Configure Claude Desktop

Add the following configuration to your Claude Desktop MCP settings file:

**On macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
**On Windows:** `%APPDATA%/Claude/claude_desktop_config.json`

```json
{
  "mcpServers": {
    "ssh-server": {
      "command": "node",
      "args": ["/absolute/path/to/mcp-ssh/dist/index.js"],
      "env": {}
    }
  }
}
```

**Important:** Replace `/absolute/path/to/mcp-ssh/` with the actual absolute path to your mcp-ssh project directory.

#### 3. Restart Claude Desktop

After adding the configuration, restart Claude Desktop completely for the changes to take effect.

#### 4. Verify Installation

Once restarted, you should see the SSH tools available in Claude Desktop. You can test by asking Claude to:
- "Connect to an SSH server"
- "List available SSH tools"
- "Copy a file between servers"

### Development Mode
```bash
npm run dev
```

### Production
```bash
npm run build
npm start
```

## Quick Start

**New to SSH MCP Server?** Check out our [How to Use Authentication Guide](examples/how-to-use-authentication.md) for simple, step-by-step instructions on connecting to your servers using natural language with Claude Desktop.

## Available Tools

### 1. ssh_connect
Connect to an SSH server.

**Parameters:**
- `host` (string): SSH server hostname or IP address
- `port` (number, default: 22): SSH port number
- `username` (string): SSH username
- `password` (string, optional): SSH password
- `privateKeyPath` (string, optional): Path to private key file
- `passphrase` (string, optional): Passphrase for private key
- `connectionId` (string): Unique identifier for this connection

**Authentication Methods:**

1. **Password Authentication:**
```json
{
  "host": "192.168.1.100",
  "username": "your_username",
  "password": "your_password",
  "connectionId": "server1"
}
```

2. **SSH Key Authentication:**
```json
{
  "host": "192.168.1.100",
  "username": "your_username",
  "privateKeyPath": "/path/to/private/key",
  "passphrase": "optional_passphrase",
  "connectionId": "server1"
}
```

### 2. ssh_disconnect
Disconnect from an SSH server.

**Parameters:**
- `connectionId` (string): Connection ID to disconnect

### 3. ssh_execute
Execute a command on a remote SSH server.

**Parameters:**
- `connectionId` (string): SSH connection ID
- `command` (string): Command to execute
- `cwd` (string, optional): Working directory for command execution

**Example:**
```json
{
  "connectionId": "server1",
  "command": "ls -la /home/user",
  "cwd": "/home/user"
}
```

### 4. ssh_copy_file
Copy files between local and remote servers or between remote servers.

**Parameters:**
- `sourceConnectionId` (string): Source SSH connection ID (use "local" for local files)
- `sourcePath` (string): Source file path
- `targetConnectionId` (string): Target SSH connection ID (use "local" for local files)
- `targetPath` (string): Target file path
- `createDirectories` (boolean, default: true): Create target directories if they don't exist

**Examples:**

**Local to Remote:**
```json
{
  "sourceConnectionId": "local",
  "sourcePath": "/local/file.txt",
  "targetConnectionId": "server1",
  "targetPath": "/remote/file.txt"
}
```

**Remote to Local:**
```json
{
  "sourceConnectionId": "server1",
  "sourcePath": "/remote/file.txt",
  "targetConnectionId": "local",
  "targetPath": "/local/file.txt"
}
```

**Remote to Remote:**
```json
{
  "sourceConnectionId": "server1",
  "sourcePath": "/path/on/server1/file.txt",
  "targetConnectionId": "server2",
  "targetPath": "/path/on/server2/file.txt"
}
```

### 5. ssh_list_files
List files and directories on local or remote server.

**Parameters:**
- `connectionId` (string): SSH connection ID (use "local" for local files)
- `remotePath` (string): Directory path to list
- `showHidden` (boolean, default: false): Show hidden files

### 6. ssh_file_info
Get file information (size, permissions, etc.).

**Parameters:**
- `connectionId` (string): SSH connection ID (use "local" for local files)
- `filePath` (string): File path to get info for

## Troubleshooting

### Claude Desktop Integration Issues

**Server not appearing in Claude Desktop:**
1. Verify the absolute path in `claude_desktop_config.json` is correct
2. Ensure the server was built successfully (`npm run build`)
3. Check that `dist/index.js` exists in your project directory
4. Restart Claude Desktop completely (quit and reopen)
5. Check Claude Desktop logs for error messages

**Permission errors:**
1. Ensure Node.js has permission to read the project directory
2. On macOS, you may need to grant Claude Desktop full disk access in System Preferences > Security & Privacy

**SSH connection failures:**
1. Verify SSH server is accessible from your machine
2. Test SSH connection manually: `ssh username@hostname`
3. Check SSH key permissions (should be 600): `chmod 600 ~/.ssh/id_rsa`
4. Ensure SSH key is in the correct format (OpenSSH)

**File transfer issues:**
1. Check file paths are absolute and correct
2. Verify write permissions on target directories
3. Ensure sufficient disk space on target system

### Getting Help

If you encounter issues:
1. Check the Claude Desktop console/logs for error messages
2. Verify your configuration matches the examples exactly
3. Test SSH connections manually before using the MCP server

## Security Considerations

- Private keys should be stored securely with appropriate file permissions (600)
- Use SSH key authentication instead of passwords when possible
- Consider using SSH agent forwarding for additional security
- The server maintains connection pools - ensure proper cleanup on shutdown

## Error Handling

The server provides detailed error messages for:
- Connection failures
- Authentication errors
- File operation errors
- Invalid parameters
- Missing connections

## Connection Management

- Connections are pooled and reused for efficiency
- Each connection has a unique ID for reference
- Connections are automatically cleaned up on server shutdown
- Use `ssh_disconnect` to manually close connections

## Temporary Files

For remote-to-remote file transfers, temporary files are created in `/tmp/` and automatically cleaned up after transfer completion or on error.

## Dependencies

- `@modelcontextprotocol/sdk`: MCP SDK for server implementation
- `node-ssh`: SSH client for Node.js
- `ssh2`: Low-level SSH2 client
- `zod`: Runtime type validation

## License

MIT