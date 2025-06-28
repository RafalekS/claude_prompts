# Project Manager Desktop Extension - Complete Recreation Prompt

## CRITICAL INSTRUCTIONS - READ FIRST

**DO NOT ASSUME ANYTHING. ASK QUESTIONS IF UNCLEAR.**

**DEPENDENCY RULE:** NEVER use `@modelcontextprotocol/sdk` or ANY external dependencies. Always create standalone MCP server implementations that handle the protocol directly, like the Windows system info examples you've seen.

**FILE ACCESS:** I have direct filesystem access to `$HOME\Projects\DXT\project_mgmt` - use this to create/modify files directly instead of providing code in artifacts.

**NO SCRIPTS:** Don't create PowerShell build scripts unless specifically requested. Provide manual commands instead.

## PROJECT REQUIREMENTS

### Extension Overview
- **Name:** `project_manager`
- **Purpose:** Intelligent coding project manager that detects natural language triggers and manages development workflows
- **Architecture:** Standalone DXT extension with no external dependencies

### Natural Language Triggers
The extension must automatically detect these phrases and trigger appropriate tools:

1. **"start new coding project"** → Triggers project creation workflow
2. **"continue working on project"** OR **"let's continue working on a project"** → Shows project selection list  
3. **"coding project {name} finished"** → Marks project as completed

### Project Creation Workflow
When triggered, ask these 5 questions IN ORDER:

1. **Project name:** (Used for folder name and identification)
2. **Programming Language:** (Choose from supported list - see below)
3. **Application/Script/Program Name:** (Main file name like 'app.py', 'server.js', 'tool.ps1')
4. **Project folder:** (Path where project gets created)
5. **Initialize git repository?** (y/n)

### Supported Languages with Templates
Create appropriate starter files and folder structures for:

- **PowerShell** → .ps1 script with headers, Modules/, Tests/ folders
- **C#** → src/ folder, Program.cs, .csproj file
- **C++** → src/, include/ folders, main.cpp, CMakeLists.txt
- **Node.js** → package.json, main JS file, src/ folder
- **Python** → main .py file, requirements.txt, setup.py, src/ with __init__.py
- **Rust** → Cargo.toml, src/main.rs with tests
- **Dockerfile** → Dockerfile, docker-compose.yml, .dockerignore
- **VBA** → .vba file with proper headers, Modules/ folder
- **DXT** → manifest.json, package.json, server/ folder with STANDALONE MCP server (no SDK)
- **Generic** → Basic template with README

### User Configuration Settings
Default values that can be changed in Claude Desktop settings:

```json
{
  "default_language": "python",
  "auto_git_init": false, 
  "default_project_path": "X:\\{Project Name}",
  "author_name": "???",
  "default_license": "MIT"
}
```

### Project State Management
- **File:** `.projects.json` in extension root
- **Structure:** Track open_projects[], finished_projects[], current_active
- **Persistence:** Maintain state across Claude sessions
- **Operations:** Create, list, select, finish projects

### Required Tools
1. `start_new_project` - Show the 5 questions
2. `continue_project` - List existing projects for selection
3. `finish_project` - Move project from open to finished
4. `list_projects` - Display all projects with status
5. `get_current_project` - Show active project details
6. `create_project` - Actually create the project (called after questions answered)
7. `select_project` - Set project as active (called after selection)

## TECHNICAL IMPLEMENTATION

### MCP Server Implementation
**CRITICAL:** Use standalone implementation without ANY external dependencies:

```javascript
class StandaloneProjectManagerServer {
  constructor() {
    this.capabilities = { tools: {} };
    this.tools = [...]; // Tool definitions
  }
  
  sendResponse(id, result) { /* JSON-RPC response */ }
  sendError(id, code, message) { /* JSON-RPC error */ }
  async handleRequest(request) { /* Protocol handling */ }
  
  run() {
    // stdio transport implementation
    process.stdin.setEncoding('utf8');
    // ... handle line-by-line JSON-RPC
  }
}
```

### File Structure
```
project_mgmt/
├── manifest.json          # DXT 0.1 compliant
├── package.json           # No dependencies!
├── server/
│   └── index.js           # Standalone MCP server
├── README.md              # Documentation
└── dist/
    └── project_manager.dxt # Built extension
```

### Language Template Details
Each language template must create:
- Appropriate main file with proper headers (author, license)
- Language-specific folder structure
- Configuration files (package.json, .csproj, Cargo.toml, etc.)
- README.md with usage instructions
- Optional: .gitignore with language-specific patterns

## CRITICAL ISSUES TO AVOID

### Dependency Error (MOST IMPORTANT)
**NEVER use `@modelcontextprotocol/sdk`** - this causes `Cannot find package` errors because dependencies aren't bundled in DXT files. Always implement the MCP protocol directly.

### Implementation Approach
1. Use direct filesystem access to `Project folder`
2. Create all files directly using filesystem tools
3. Build using manual PowerShell command: `Compress-Archive -Path manifest.json,package.json,server,README.md -DestinationPath dist\project_manager.dxt -Force`

### Ask Before Acting
- Confirm requirements before implementing
- Don't rush to provide solutions
- Ask about unclear specifications

## BUILD PROCESS

1. **Create file structure** in `Project folder`
2. **Write all files** using filesystem access (not artifacts)
3. **Build extension** using PowerShell command
4. **Test** that .dxt file is created properly

## MANIFEST.JSON REQUIREMENTS

```json
{
  "dxt_version": "0.1",
  "name": "project_manager", 
  "version": "1.0.0",
  "display_name": "Project Manager",
  "description": "Intelligent coding project manager...",
  "author": { "name": "???" },
  "license": "MIT",
  "server": {
    "type": "node",
    "entry_point": "server/index.js",
    "mcp_config": {
      "command": "node",
      "args": ["${__dirname}/server/index.js"],
      "env": {
        "DEFAULT_LANGUAGE": "${user_config.default_language}",
        "AUTO_GIT_INIT": "${user_config.auto_git_init}",
        "DEFAULT_PROJECT_PATH": "${user_config.default_project_path}",
        "AUTHOR_NAME": "${user_config.author_name}",
        "DEFAULT_LICENSE": "${user_config.default_license}"
      }
    }
  },
  "tools": [/* All 7 tools */],
  "user_config": {/* All 5 config options */},
  "compatibility": {
    "platforms": ["win32"],
    "runtimes": { "node": ">=18.0.0" }
  }
}
```

## SUCCESS CRITERIA

✅ Extension detects natural language triggers automatically  
✅ Creates appropriate templates for all 10 languages  
✅ Manages project state across sessions  
✅ No external dependency errors  
✅ Git integration works when enabled  
✅ All user configuration options functional  
✅ Can create, continue, and finish projects  
✅ Files created directly in specified directory  
✅ .dxt file builds successfully  

## FINAL NOTE

This extension should work exactly like described - detecting when I say "start new coding project" and guiding me through the entire workflow without any dependency or implementation issues. Focus on the standalone implementation and direct file system access approach that worked in our final solution.