# Claude Desktop Configuration Examples

Here are platform-specific configuration examples for integrating the SSH MCP server with Claude Desktop.

## Configuration File Locations

- **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows:** `%APPDATA%/Claude/claude_desktop_config.json`
- **Linux:** `~/.config/Claude/claude_desktop_config.json`

## Platform-Specific Examples

### macOS Configuration

```json
{
  "mcpServers": {
    "ssh-server": {
      "command": "node",
      "args": ["/Users/username/Documents/mcp-ssh/dist/index.js"],
      "env": {}
    }
  }
}
```

### Windows Configuration

```json
{
  "mcpServers": {
    "ssh-server": {
      "command": "node",
      "args": ["C:\\Users\\username\\Documents\\mcp-ssh\\dist\\index.js"],
      "env": {}
    }
  }
}
```

### Linux Configuration

```json
{
  "mcpServers": {
    "ssh-server": {
      "command": "node",
      "args": ["/home/username/mcp-ssh/dist/index.js"],
      "env": {}
    }
  }
}
```

## Important Notes

1. **Use Absolute Paths**: Always use the complete absolute path to the `dist/index.js` file
2. **Escape Backslashes**: On Windows, use double backslashes (`\\`) in JSON strings
3. **Replace Username**: Replace `username` with your actual username
4. **Project Location**: Adjust the path to match where you cloned/created the mcp-ssh project

## Finding Your Project Path

### macOS/Linux
```bash
cd /path/to/your/mcp-ssh
pwd
```

### Windows (Command Prompt)
```cmd
cd C:\path\to\your\mcp-ssh
echo %cd%
```

### Windows (PowerShell)
```powershell
cd C:\path\to\your\mcp-ssh
Get-Location
```

## Verification

After adding the configuration:

1. **Restart Claude Desktop** completely (quit and reopen)
2. **Test the integration** by asking Claude: "What SSH tools are available?"
3. **Check for errors** in Claude Desktop's console/logs if the server doesn't appear

## Multiple MCP Servers

If you have other MCP servers, add the SSH server to your existing configuration:

```json
{
  "mcpServers": {
    "existing-server": {
      "command": "existing-command",
      "args": ["existing-args"]
    },
    "ssh-server": {
      "command": "node",
      "args": ["/absolute/path/to/mcp-ssh/dist/index.js"],
      "env": {}
    }
  }
}
```