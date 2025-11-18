# Claude Desktop Troubleshooting Guide

## Test Results ✅

The `autotask-mcp` package (v1.0.2) has been verified working correctly:

- ✅ Package published to npm successfully
- ✅ STDOUT contains only clean JSON-RPC messages
- ✅ STDERR contains all logs (properly redirected via wrapper.js)
- ✅ MCP protocol initialization works correctly
- ✅ Tool calls execute successfully
- ✅ Autotask API connectivity confirmed with provided credentials

## Configuration File

Your Claude Desktop config should be at:
- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`
- **Linux**: `~/.config/Claude/claude_desktop_config.json`

Your configuration:
```json
{
  "mcpServers": {
    "autotask": {
      "command": "npx",
      "args": ["autotask-mcp"],
      "env": {
        "AUTOTASK_USERNAME": "fnmb6rs4atps5gv@phoneware.us",
        "AUTOTASK_SECRET": "H#t80jR*$4yKM@6e3Sp~w2$W5",
        "AUTOTASK_INTEGRATION_CODE": "FCYZIAVIWAQCGKDVPCZXY33EZMC"
      }
    }
  }
}
```

## Troubleshooting Steps

### 1. Clear npx Cache
Claude Desktop might be using a cached old version:

```bash
# Clear npx cache for autotask-mcp
npx clear-npx-cache
# OR manually remove cache
rm -rf ~/.npm/_npx/*/node_modules/autotask-mcp
```

### 2. Force Latest Version
Update your config to explicitly use latest:

```json
{
  "mcpServers": {
    "autotask": {
      "command": "npx",
      "args": ["autotask-mcp@latest"],  // Add @latest
      "env": {
        "AUTOTASK_USERNAME": "fnmb6rs4atps5gv@phoneware.us",
        "AUTOTASK_SECRET": "H#t80jR*$4yKM@6e3Sp~w2$W5",
        "AUTOTASK_INTEGRATION_CODE": "FCYZIAVIWAQCGKDVPCZXY33EZMC"
      }
    }
  }
}
```

### 3. Restart Claude Desktop
Completely quit and restart Claude Desktop:
- **macOS**: Cmd+Q to quit, then relaunch
- **Windows**: Exit from system tray, then relaunch

### 4. Check Claude Desktop Logs
View Claude Desktop logs to see MCP server errors:

**macOS**:
```bash
tail -f ~/Library/Logs/Claude/mcp*.log
```

**Windows**:
```powershell
Get-Content "$env:APPDATA\Claude\logs\mcp*.log" -Tail 50 -Wait
```

### 5. Test Outside Claude Desktop
Verify the package works independently:

```bash
export AUTOTASK_USERNAME='fnmb6rs4atps5gv@phoneware.us'
export AUTOTASK_SECRET='H#t80jR*$4yKM@6e3Sp~w2$W5'
export AUTOTASK_INTEGRATION_CODE='FCYZIAVIWAQCGKDVPCZXY33EZMC'

# Test the server
npx autotask-mcp@latest

# Then in another terminal, send an initialize request:
echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"1.0.0"}}}' | npx autotask-mcp@latest
```

### 6. Check for Old Versions
Make sure no old versions are installed globally:

```bash
# Check global installs
npm list -g autotask-mcp

# If found, uninstall
npm uninstall -g autotask-mcp
```

## Common Issues

### Issue: "Failed to connect to MCP server"
**Solution**: Check that Node.js is installed and accessible:
```bash
node --version  # Should show v18 or higher
npx --version
```

### Issue: "Invalid credentials" or "401 Unauthorized"
**Solution**: Verify credentials are exactly as shown in the config, with no extra spaces or quotes.

### Issue: "Zod validation errors" or malformed JSON
**Solution**: This was fixed in v1.0.2. Make sure you're using the latest version:
```bash
npx autotask-mcp@latest --version
```

## Version Information

- **Current Published Version**: 1.0.2
- **Minimum Node.js**: 18.0.0
- **Package**: https://www.npmjs.com/package/autotask-mcp
- **GitHub**: https://github.com/jwaldrip/autotask-mcp

## Still Having Issues?

If the above steps don't resolve your issue:

1. Capture the error from Claude Desktop logs
2. Test the server independently using step 5 above
3. Report the issue at: https://github.com/jwaldrip/autotask-mcp/issues

Include:
- Claude Desktop version
- Node.js version (`node --version`)
- npm version (`npm --version`)
- Operating system
- Error logs from Claude Desktop
- Output from independent test
