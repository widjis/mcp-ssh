# How to Use SSH Authentication in Claude Desktop

This guide explains how to actually use the SSH authentication examples you see in the documentation.

## What Those JSON Examples Mean

The JSON examples you see in `usage-examples.md` are **tool call formats** that Claude Desktop uses internally when you ask it to connect to SSH servers. You don't need to type these JSON blocks directly.

## How to Actually Use SSH Authentication

### Step 1: Set Up the MCP Server

First, make sure you've configured the SSH MCP server in Claude Desktop (see README.md for setup instructions).

### Step 2: Ask Claude to Connect Using Natural Language

Instead of typing JSON, you simply ask Claude in natural language. Here are examples:

#### Password Authentication

**You say:**
```
"Connect to my server at 192.168.1.100 using username 'admin' and password 'mypassword'. Call this connection 'webserver'."
```

**Claude will internally use:**
```json
{
  "tool": "ssh_connect",
  "arguments": {
    "host": "192.168.1.100",
    "username": "admin",
    "password": "mypassword",
    "connectionId": "webserver"
  }
}
```

#### SSH Key Authentication

**You say:**
```
"Connect to my AWS server ec2-xx-xx-xx-xx.compute-1.amazonaws.com using username 'ec2-user' and my private key at '/Users/myname/.ssh/my-key.pem'. Call this connection 'aws-server'."
```

**Claude will internally use:**
```json
{
  "tool": "ssh_connect",
  "arguments": {
    "host": "ec2-xx-xx-xx-xx.compute-1.amazonaws.com",
    "username": "ec2-user",
    "privateKeyPath": "/Users/myname/.ssh/my-key.pem",
    "connectionId": "aws-server"
  }
}
```

## Real-World Usage Examples

### Example 1: Connect and List Files

**You:** "Connect to my Ubuntu server at myserver.com with username 'ubuntu' and password 'secret123', then list the files in the home directory."

**Claude will:**
1. Connect using the ssh_connect tool
2. List files using the ssh_list_files tool
3. Show you the results

### Example 2: Copy Files Between Servers

**You:** "Connect to server1 at 192.168.1.10 (username: admin, password: pass1) and server2 at 192.168.1.20 (username: root, key: /path/to/key), then copy /home/data.txt from server1 to /backup/ on server2."

**Claude will:**
1. Connect to both servers
2. Copy the file between them
3. Confirm the operation

### Example 3: Execute Commands

**You:** "Connect to my development server using my SSH key and run 'git pull' in the /var/www/myapp directory."

**Claude will:**
1. Connect using your SSH key
2. Execute the git pull command
3. Show you the output

## Key Points

1. **You don't type JSON** - Just ask Claude in plain English
2. **Claude translates** your request into the appropriate tool calls
3. **Be specific** about:
   - Server address (IP or hostname)
   - Username
   - Authentication method (password or key file path)
   - What you want to do

## Common Authentication Scenarios

### Home Lab Server
```
"Connect to my home server at 192.168.1.100 using username 'pi' and password 'raspberry'"
```

### AWS EC2 Instance
```
"Connect to my AWS server using the key file at /Users/myname/Downloads/my-aws-key.pem and username 'ec2-user'"
```

### VPS with Custom Port
```
"Connect to myserver.com on port 2222 using username 'root' and my SSH key at ~/.ssh/id_rsa"
```

## Troubleshooting

If Claude says it can't connect:

1. **Check your credentials** - Make sure username/password or key path is correct
2. **Verify server address** - Can you ping the server?
3. **Test manually** - Try connecting with a regular SSH client first
4. **Check permissions** - SSH keys need proper permissions (chmod 600)

Remember: The JSON examples in the documentation are just showing you what happens "under the hood" - you interact with Claude using natural language!