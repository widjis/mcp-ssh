# SSH MCP Server Usage Examples

This document provides comprehensive examples of how to use the SSH MCP server for various tasks.

## Authentication Methods

The SSH MCP server supports two authentication methods:

### 1. Password Authentication

```json
{
  "tool": "ssh_connect",
  "arguments": {
    "host": "192.168.1.100",
    "port": 22,
    "username": "your_username",
    "password": "your_password",
    "connectionId": "server1"
  }
}
```

### 2. SSH Key Authentication

```json
{
  "tool": "ssh_connect",
  "arguments": {
    "host": "192.168.1.100",
    "port": 22,
    "username": "your_username",
    "privateKeyPath": "/path/to/your/private/key",
    "passphrase": "key_passphrase_if_needed",
    "connectionId": "server1"
  }
}
```

### 3. SSH Key Authentication (without passphrase)

```json
{
  "tool": "ssh_connect",
  "arguments": {
    "host": "192.168.1.100",
    "port": 22,
    "username": "your_username",
    "privateKeyPath": "/path/to/your/private/key",
    "connectionId": "server1"
  }
}
```

## Common Authentication Scenarios

### Connecting to AWS EC2 Instance

```json
{
  "tool": "ssh_connect",
  "arguments": {
    "host": "ec2-xx-xx-xx-xx.compute-1.amazonaws.com",
    "port": 22,
    "username": "ec2-user",
    "privateKeyPath": "/path/to/your-key.pem",
    "connectionId": "aws-server"
  }
}
```

### Connecting to Ubuntu Server

```json
{
  "tool": "ssh_connect",
  "arguments": {
    "host": "your-server.com",
    "port": 22,
    "username": "ubuntu",
    "password": "your_password",
    "connectionId": "ubuntu-server"
  }
}
```

### Connecting with Custom SSH Port

```json
{
  "tool": "ssh_connect",
  "arguments": {
    "host": "your-server.com",
    "port": 2222,
    "username": "admin",
    "privateKeyPath": "/home/user/.ssh/id_rsa",
    "connectionId": "custom-port-server"
  }
}
```

## Authentication Best Practices

### Security Recommendations

1. **Use SSH Keys Instead of Passwords**: SSH key authentication is more secure than password authentication
2. **Protect Private Keys**: Ensure your private key files have proper permissions (600)
3. **Use Passphrases**: Add passphrases to your SSH keys for additional security
4. **Unique Connection IDs**: Use descriptive and unique connection IDs to manage multiple connections

### Common SSH Key Locations

- **macOS/Linux**: `~/.ssh/id_rsa` (private key), `~/.ssh/id_rsa.pub` (public key)
- **Windows**: `C:\Users\YourUsername\.ssh\id_rsa`
- **AWS EC2**: Downloaded `.pem` file from AWS console

### Setting Correct Permissions (macOS/Linux)

```bash
# Set correct permissions for private key
chmod 600 ~/.ssh/id_rsa

# Set correct permissions for SSH directory
chmod 700 ~/.ssh
```

## Basic Connection and File Operations

### 1. Connect to SSH Server (Key Authentication Example)

```json
{
  "tool": "ssh_connect",
  "arguments": {
    "host": "192.168.1.100",
    "port": 22,
    "username": "admin",
    "privateKeyPath": "/Users/username/.ssh/id_rsa",
    "connectionId": "production-server"
  }
}
```

### 2. Execute Commands

```json
{
  "tool": "ssh_execute",
  "arguments": {
    "connectionId": "production-server",
    "command": "df -h",
    "cwd": "/home/admin"
  }
}
```

### 3. List Remote Files

```json
{
  "tool": "ssh_list_files",
  "arguments": {
    "connectionId": "production-server",
    "remotePath": "/var/log",
    "showHidden": false
  }
}
```

### 4. Copy File from Local to Remote

```json
{
  "tool": "ssh_copy_file",
  "arguments": {
    "sourceConnectionId": "local",
    "sourcePath": "/Users/username/config.json",
    "targetConnectionId": "production-server",
    "targetPath": "/etc/myapp/config.json",
    "createDirectories": true
  }
}
```

### 5. Copy File from Remote to Local

```json
{
  "tool": "ssh_copy_file",
  "arguments": {
    "sourceConnectionId": "production-server",
    "sourcePath": "/var/log/application.log",
    "targetConnectionId": "local",
    "targetPath": "/Users/username/Downloads/application.log",
    "createDirectories": true
  }
}
```

## Advanced Scenarios

### Multi-Server File Transfer

```json
// Connect to first server
{
  "tool": "ssh_connect",
  "arguments": {
    "host": "server1.example.com",
    "username": "deploy",
    "privateKeyPath": "/Users/username/.ssh/deploy_key",
    "connectionId": "server1"
  }
}

// Connect to second server
{
  "tool": "ssh_connect",
  "arguments": {
    "host": "server2.example.com",
    "username": "deploy",
    "privateKeyPath": "/Users/username/.ssh/deploy_key",
    "connectionId": "server2"
  }
}

// Copy file between servers
{
  "tool": "ssh_copy_file",
  "arguments": {
    "sourceConnectionId": "server1",
    "sourcePath": "/app/data/backup.tar.gz",
    "targetConnectionId": "server2",
    "targetPath": "/backups/server1-backup.tar.gz",
    "createDirectories": true
  }
}
```

### Deployment Workflow

```json
// 1. Connect to deployment server
{
  "tool": "ssh_connect",
  "arguments": {
    "host": "deploy.example.com",
    "username": "deployer",
    "password": "secure_password",
    "connectionId": "deploy-server"
  }
}

// 2. Create deployment directory
{
  "tool": "ssh_execute",
  "arguments": {
    "connectionId": "deploy-server",
    "command": "mkdir -p /var/www/app/releases/$(date +%Y%m%d_%H%M%S)"
  }
}

// 3. Upload application files
{
  "tool": "ssh_copy_file",
  "arguments": {
    "sourceConnectionId": "local",
    "sourcePath": "/Users/developer/myapp/dist.tar.gz",
    "targetConnectionId": "deploy-server",
    "targetPath": "/var/www/app/releases/latest/app.tar.gz"
  }
}

// 4. Extract and setup
{
  "tool": "ssh_execute",
  "arguments": {
    "connectionId": "deploy-server",
    "command": "cd /var/www/app/releases/latest && tar -xzf app.tar.gz && rm app.tar.gz",
    "cwd": "/var/www/app/releases/latest"
  }
}

// 5. Restart application
{
  "tool": "ssh_execute",
  "arguments": {
    "connectionId": "deploy-server",
    "command": "systemctl restart myapp"
  }
}
```

### Log Analysis

```json
// Get file info
{
  "tool": "ssh_file_info",
  "arguments": {
    "connectionId": "production-server",
    "filePath": "/var/log/nginx/access.log"
  }
}

// Analyze recent logs
{
  "tool": "ssh_execute",
  "arguments": {
    "connectionId": "production-server",
    "command": "tail -n 100 /var/log/nginx/access.log | grep -E '(404|500)'"
  }
}

// Download log file for local analysis
{
  "tool": "ssh_copy_file",
  "arguments": {
    "sourceConnectionId": "production-server",
    "sourcePath": "/var/log/nginx/access.log",
    "targetConnectionId": "local",
    "targetPath": "/Users/analyst/logs/nginx-access-$(date +%Y%m%d).log"
  }
}
```

### Cleanup

```json
// Disconnect from servers
{
  "tool": "ssh_disconnect",
  "arguments": {
    "connectionId": "production-server"
  }
}

{
  "tool": "ssh_disconnect",
  "arguments": {
    "connectionId": "deploy-server"
  }
}
```

## Error Handling Examples

### Connection Errors
- Invalid host/port: Returns connection timeout error
- Authentication failure: Returns authentication error with details
- Network issues: Returns network connectivity error

### File Operation Errors
- Permission denied: Returns permission error with file path
- File not found: Returns file not found error
- Disk space issues: Returns disk space error

### Command Execution Errors
- Command not found: Returns command execution error with exit code
- Permission issues: Returns permission error
- Invalid working directory: Returns directory error

## Best Practices

1. **Use Connection IDs**: Always use descriptive connection IDs
2. **Key Authentication**: Prefer SSH keys over passwords
3. **Error Handling**: Always check for errors in responses
4. **Cleanup**: Disconnect when done to free resources
5. **Path Validation**: Validate file paths before operations
6. **Permissions**: Ensure proper file permissions on SSH keys (600)