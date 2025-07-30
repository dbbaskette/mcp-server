# Claude Desktop Configuration for MCP Server

This guide explains how to configure Claude Desktop to use your custom MCP Server.

## Prerequisites

1. **Build the Server**: Make sure you have built the JAR file
   ```bash
   mvn clean install
   ```

2. **Verify JAR Location**: Ensure the JAR exists at:
   ```
   /Users/dbbaskette/Projects/mcp-server/target/mcp-server-0.0.1-SNAPSHOT.jar
   ```

3. **Java Installation**: Verify Java is available in your PATH
   ```bash
   java -version
   ```

## Configuration Setup

### Option 1: Using the Provided Config File

1. Copy the contents of `claude-desktop-config.json` from this directory
2. Open Claude Desktop settings and navigate to the MCP configuration
3. Add the server configuration

### Option 2: Manual Configuration

Add this configuration to your Claude Desktop MCP servers settings:

```json
{
  "mcpServers": {
    "mcp-server": {
      "command": "java",
      "args": [
        "-Dspring.profiles.active=stdio",
        "-jar",
        "/Users/dbbaskette/Projects/mcp-server/target/mcp-server-0.0.1-SNAPSHOT.jar"
      ]
    }
  }
}
```

### Configuration Explanation

- **Server Name**: `mcp-server` - This is how the server will appear in Claude Desktop
- **Command**: `java` - Runs the Java executable
- **Profile**: `stdio` - Configures the server for STDIO transport (required for Claude Desktop)
- **Logging**: Automatically configured in the `stdio` profile (banner disabled, console logging off)
- **JAR Path**: Absolute path to your built JAR file

## Available Tools

Once configured, Claude Desktop will have access to these tools:

### 1. **capitalizeText**
- **Description**: Capitalize the first letter of each word in input text
- **Usage**: Ask Claude to capitalize text like "hello world" → "Hello World"

### 2. **calculate** 
- **Description**: Perform basic mathematical operations
- **Supported Operators**: `+`, `-`, `*`, `/`, `%`, `^` (power)
- **Usage**: Ask Claude to calculate "2 + 3 * 4" or "2 raised to the power of 8"

### 3. **toUpperCase**
- **Description**: Convert text to uppercase
- **Usage**: Ask Claude to convert text to uppercase

## Testing the Configuration

1. **Restart Claude Desktop** after adding the configuration
2. **Check Server Status**: Look for the "mcp-server" in the connected servers list
3. **Test Tools**: Try asking Claude to:
   - "Capitalize this text: hello world from mcp"
   - "Calculate 15 divided by 3"
   - "Convert 'hello world' to uppercase"

## Testing the Server

Use the unified test script to verify everything works:

```bash
# Test STDIO mode (Claude Desktop compatible)
./test-mcp.sh --stdio --test-tools

# Test SSE mode  
./test-mcp.sh --sse --test-tools

# Show all options
./test-mcp.sh --help
```

## Troubleshooting

### Server Not Starting
- **Check JAR Path**: Ensure the absolute path is correct
- **Verify Java**: Run `java -version` in terminal
- **Check Logs**: Look at `./target/mcp-server-stdio.log` for errors

### Tools Not Available
- **Restart Claude Desktop**: Configuration changes require restart
- **Check Connection**: Verify server appears in MCP servers list
- **Build Issues**: Ensure `mvn clean install` completed successfully

### Permission Issues
- **JAR Permissions**: Ensure the JAR file is readable
- **Directory Access**: Verify Claude Desktop can access the project directory

## Development Workflow

When making changes to the MCP server:

1. **Rebuild**: `mvn clean install`
2. **Restart Claude Desktop**: Stop and start the application
3. **Test**: Verify tools work as expected

## Alternative Configuration (Relative Path)

If you prefer a relative path configuration, you can use:

```json
{
  "mcpServers": {
    "mcp-server": {
      "command": "java",
      "args": [
        "-Dspring.profiles.active=stdio",
        "-Dspring.main.banner-mode=off", 
        "-Dlogging.pattern.console=",
        "-jar",
        "target/mcp-server-0.0.1-SNAPSHOT.jar"
      ],
      "cwd": "/Users/dbbaskette/Projects/mcp-server"
    }
  }
}
```

This sets the working directory and uses a relative path to the JAR file.

## Security Notes

- The server runs locally and does not expose any external network ports when using STDIO mode
- All communication happens through standard input/output pipes
- No sensitive data is logged or transmitted over the network