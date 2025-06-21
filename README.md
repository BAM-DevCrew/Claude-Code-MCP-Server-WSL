# Working Solution: Claude Code MCP Server + WSL + VS Code IDEs

> **Cross-platform configuration enabling Claude Code MCP server integration across Windows-based VS Code IDEs through WSL**

## The Problem

Despite Anthropic's claim that "Claude Code MCP server works with any MCP client," there's no documentation showing how to actually configure this on Windows systems using WSL. The BAM development crew discovered this gap while building our complex multi-component TypeScript project requiring advanced AI development tools.

**Tested and working with:**
Cursor, Augment Code, Windsurf, CodeGPT, Cline, Roo Code, Refact.ai, Continue, VS Code

## The Solution

After extensive research and testing, our crew (in collaboration with Claude Sonnet 4) developed a working cross-platform bridge configuration.

### Prerequisites

1. **WSL (version 1 or 2) with Ubuntu** installed on Windows
2. **Claude Code installed in WSL** (`npm install -g @anthropic-ai/claude-code`)
3. **VS Code-based IDE** with MCP support
4. **Claude Code working directory** accessible via WSL

### Universal JSON Configuration

Add this to your IDE's MCP configuration file (replace `YOUR_DRIVE` and `YOUR_PROJECT_PATH` with your actual values):

```json
{
  "mcpServers": {
    "claude-code": {
      "command": "wsl",
      "args": [
        "-d",
        "Ubuntu",
        "bash",
        "-c",
        "cd /mnt/YOUR_DRIVE/YOUR_PROJECT_PATH; /usr/bin/claude mcp serve"
      ]
    }
  }
}
```

**Example for C: drive:**
```json
"cd /mnt/c/Users/YourName/Projects/MyProject; /usr/bin/claude mcp serve"
```

**Example for D: drive:**
```json
"cd /mnt/d/Development/MyProject; /usr/bin/claude mcp serve"
```

### Key Elements

#### 1. **WSL Command Bridge**
```json
"command": "wsl"
```
Enables Windows IDEs to execute commands inside WSL environment.

#### 2. **Proper Command Chaining**
```bash
"cd /mnt/f/YOUR_PROJECT_PATH; /usr/bin/claude mcp serve"
```
**Critical:** Use semicolon (`;`) instead of (`&&`) for reliable command execution.

#### 3. **WSL Path Format**
- ❌ Windows: `C:\Users\Name\Project`
- ✅ WSL: `/mnt/c/Users/Name/Project`

#### 4. **Full Claude Path**
```bash
/usr/bin/claude
```
Use absolute path to avoid PATH resolution issues.

## IDE-Specific Setup

### Cursor
Create/edit `.cursor/mcp.json`:
```json
{
  "mcpServers": {
    "claude-code": {
      "command": "wsl",
      "args": ["-d", "Ubuntu", "bash", "-c", "cd /mnt/c/YOUR_PATH; /usr/bin/claude mcp serve"]
    }
  }
}
```

### Augment Code
Use Settings Panel or `settings.json`:
```json
{
  "augment.advanced": {
    "mcpServers": [{
      "name": "claude-code",
      "command": "wsl",
      "args": ["-d", "Ubuntu", "bash", "-c", "cd /mnt/c/YOUR_PATH; /usr/bin/claude mcp serve"]
    }]
  }
}
```

### VS Code
Create `.vscode/mcp.json`:
```json
{
  "servers": {
    "claude-code": {
      "command": "wsl",
      "args": ["-d", "Ubuntu", "bash", "-c", "cd /mnt/c/YOUR_PATH; /usr/bin/claude mcp serve"]
    }
  }
}
```

## Available Claude Code Tools

Once configured, you'll have access to:

**File Operations**: `Read`, `Write`, `Edit`, `MultiEdit`  
**Discovery**: `LS`, `Glob`, `Grep`  
**Execution**: `Bash`  
**Jupyter**: `NotebookRead`, `NotebookEdit`  
**Task Management**: `TodoRead`, `TodoWrite`  
**Web**: `WebFetch`, `WebSearch`

## Testing Your Setup

1. Restart your IDE after saving MCP configuration
2. Test with prompt: `"Use claude-code to read /mnt/c/YOUR_PATH/README.md"` (replace with actual filename)
3. Verify tools available: Check IDE's MCP panel/tools list

## Troubleshooting

**"Claude not recognized"**  
Verify Claude Code installed: `wsl which claude`  
Use full path: `/usr/bin/claude` or `/home/user/.nvm/versions/node/vXX.X.X/bin/claude`

**"Path not found"**  
Check WSL mount: `wsl ls /mnt/c/`  
Verify project path exists in WSL  
Use absolute WSL paths (`/mnt/c/...`)

**"Connection closed"**  
Ensure semicolon (`;`) separates commands  
Check WSL distribution name: `wsl -l -v`  
Try without directory change first: `"/usr/bin/claude mcp serve"`

## About This Solution

This configuration was developed by the BAM development crew in collaboration with Claude Sonnet 4 while building advanced TypeScript-based development tools. We identified this documentation gap and solved the Windows→WSL→MCP bridge problem.

## Search Keywords

Claude Code MCP server, VS Code MCP configuration, WSL MCP setup, Windows Subsystem Linux MCP, Anthropic Claude Code, MCP JSON configuration, cross-platform AI development, Claude Code Windows setup, VS Code AI tools, MCP client configuration

---

Star this repo if this solution helped you. Share with other developers struggling with MCP + WSL setup. Open issues for other IDE configurations or improvements.
