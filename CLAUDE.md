# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

# Project Context

Refer to the user-provided 'PROJECT.md' file for specific project goals, tech stack, and architecture. That file is the primary source of truth for project-specific context.

---

# Core Development Guidelines

## Prime Directive: Safety and Accuracy
- **Your #1 rule is to not hallucinate.**
- You must only provide solutions when you have strong evidence, understand the full context, and are confident the answer is correct and safe.
- If context is missing or a request is ambiguous, you **must** pause and ask clarifying questions before proceeding.

## Interaction Workflow
1.  **Clarify:** Ask questions to resolve ambiguity before generating code.
2.  **Reason & Plan (Chain-of-Thought):** For any non-trivial request, you must use Chain-of-Thought (CoT) reasoning. Before producing code, outline a detailed, structured plan. This includes breaking down the problem, considering trade-offs, and identifying edge cases. For significant architectural design, complex integrations, or unfamiliar APIs, this reasoning must be comprehensive. **Wait for approval on the plan before generating code.**
3.  **Generate:** Create minimal, idiomatic code. Add comments only for complex logic.
4.  **Self-Correct:** Before finalizing your response, review your work against this checklist:
    *   Does the code meet all requirements?
    *   Is it idiomatic for the language and framework?
    *   Is it secure? (See Security section below).
    *   Does it include necessary tests?
    *   Are file paths and explanations clear?

## Output Formatting
- Use Markdown.
- Use language-tagged code blocks.
- When creating a new file, **always** state its full intended path.
- When modifying an existing file, present the changes in a `diff` format.
- When creating multiple files, show a file tree first, then each file's content.

## General Code Quality
- **Immutability:** Prefer immutable data structures and objects where practical.
- **Constants:** Avoid "magic strings" and "magic numbers." Use named constants.

## Security & Compliance
- Do not include secrets in examples.
- Redact creds and tokens.
- Flag insecure patterns and propose safe alternatives.

## Testing & Validation
- Provide unit or integration test examples for all new business logic.
- Include a simple command to run the tests (e.g., `mvn test`, `npm test`).

## Token & Output Efficiency
- **Always be mindful of token consumption and cost.** This is especially critical for: logs, responses from other LLMs, network requests, and CLI printouts.
- Minimize unnecessary output and avoid repetition. Keep responses, especially JSON or config files, tight and concise.
- Use logging (`log.debug`, `System.out.println`) judiciously and only where it adds significant value for debugging.
- When asked to summarize or explain, offer a "concise" and an "expanded" version if appropriate.
- If the request involves prompt engineering for another LLM, include advice on token budgeting.

---

# Spring Boot & MCP Server Guidelines

## Spring Boot Rules
1. Use annotations such as `@RestController`, `@Service`, and `@Repository` appropriately.
2. Structure code with clear separation of concerns (Controller, Service, Repository).
3. Use constructor injection (no field injection).
4. Prefer `ResponseEntity<?>` for REST responses and include relevant HTTP status codes.
5. Externalize configuration in `application.yml`. Show example snippets when adding new props.
6. Provide tests (JUnit + Testcontainers when DBs are involved).

## MCP Server Rules
1. Follow the client/tool semantics you are targeting; define clear tool schemas.
2. Keep handlers stateless and fast; enforce auth and rate limits.
3. Return structured JSON results and clear error objects.
4. Provide a local/dev runner and example client calls.
5. Add basic observability (request logs + latency counters).

---

# Project Overview

This is a Spring AI MCP (Model Context Protocol) Server project that serves as a foundation for building other MCP server applications. The project demonstrates both STDIO and SSE (Server-Sent Events) transport modes.

## Tech Stack

- **Java 21** - Primary language
- **Spring Boot 3.4.5** - Application framework  
- **Spring AI 1.1.0-SNAPSHOT** - AI integration framework with MCP support
- **Maven** - Build and dependency management
- **Base Package**: `com.baskettecase.mcpserver`

## Build and Development Commands

### Building the Project
```bash
mvn clean install
```

### Running Tests
```bash
mvn test
```

### Running the Server

**Using the Unified Test Script (Recommended)**:
```bash
# SSE mode (default)
./test-mcp.sh --sse

# STDIO mode (Claude Desktop compatible)
./test-mcp.sh --stdio

# Build and run
./test-mcp.sh --build --sse

# Test tools interactively
./test-mcp.sh --stdio --test-tools

# Show all options
./test-mcp.sh --help
```

**Manual Execution**:
```bash
# SSE Mode (Default - Web Server)
java -Dspring.profiles.active=sse -jar target/mcp-server-0.0.1-SNAPSHOT.jar

# STDIO Mode
java -Dspring.profiles.active=stdio -jar target/mcp-server-0.0.1-SNAPSHOT.jar
```

### Testing
- **Unit Tests**: 27 comprehensive tests for the MCP tools
- **Test Script**: `./test-mcp.sh` provides unified testing interface with tool testing
- **Test Coverage**: Text capitalization, mathematical operations, edge cases

## Architecture Overview

### Transport Layer Architecture
The application supports dual transport modes:
- **SSE Transport**: Uses Spring WebFlux with reactive endpoints for web-based MCP clients
- **STDIO Transport**: Uses standard input/output for command-line MCP clients

### Core Components

1. **McpServerApplication.java** - Main Spring Boot application class
   - Registers tool callbacks using `MethodToolCallbackProvider`
   - Contains sample `toUpperCase` function tool
   - Entry point: `src/main/java/com/baskettecase/mcpserver/McpServerApplication.java:17`

2. **ToolsService.java** - Service implementing MCP tools
   - Uses `@Tool` annotation for automatic tool registration
   - Implements text capitalization and mathematical operations
   - Location: `src/main/java/com/baskettecase/mcpserver/ToolsService.java:7`

3. **Test Infrastructure**
   - `ToolsServiceTest.java` - Comprehensive unit tests (27 test cases)
   - `test-mcp.sh` - Unified testing script with tool discovery and execution

### Configuration Strategy
- **Profile-Based**: Separate configuration files for each transport mode
  - `application.properties` - Base configuration with default SSE profile
  - `application-sse.properties` - SSE transport configuration
  - `application-stdio.properties` - STDIO transport configuration
- **Key Properties**:
  - `spring.profiles.active` - Profile selection (sse/stdio)
  - `spring.ai.mcp.server.stdio` - Transport mode toggle
  - `spring.main.web-application-type` - Application type (reactive/none)
  - `spring.main.banner-mode=off` - Required for STDIO mode
  - Logging configuration - Critical for STDIO transport functionality

## Project Transformation Goals

**Completed Transformation Goals** (from PROJECT.md):

✅ **MCP Server Foundation**: Fully transformed from weather-specific to reusable foundation
✅ **Profile Separation**: Complete profile-based configuration system
✅ **Core Tools**: Implemented text capitalization and mathematical calculator
✅ **Testing Infrastructure**: Comprehensive unit tests and colorful testing script
✅ **Package Structure**: Migrated to `com.baskettecase.mcpserver`

**Available Tools**:
- `capitalizeText` - Capitalize first letter of each word in input text
- `calculate` - Perform basic math operations (+, -, *, /, %, ^)
- `toUpperCase` - Convert text to uppercase

## Development Patterns

### Tool Registration
Tools are registered using two patterns:
1. **Method-based**: Using `@Tool` annotation on service methods
2. **Function-based**: Using `FunctionToolCallback.builder()` for lambda functions

### Spring AI Integration
- Uses `spring-ai-starter-mcp-server-webflux` starter dependency
- Automatic tool discovery through Spring's component scanning
- Integration with Spring AI BOM for version management

## Usage and Testing

### Quick Start
1. **Build**: `mvn clean install`
2. **Test**: `./test-mcp.sh --sse` or `./test-mcp.sh --stdio`
3. **Test Tools**: `./test-mcp.sh --stdio --test-tools`
4. **Connect MCP Client**: 
   - SSE: `http://localhost:8080/mcp/message`
   - STDIO: Use the running process input/output

### Important Configuration Notes
- **STDIO Mode**: Banner and console logging automatically disabled
- **Profile Selection**: Use `--sse` or `--stdio` with test script, or set `spring.profiles.active`
- **Repository Configuration**: Uses Spring milestone and snapshot repositories for Spring AI
- **Transport Mode**: Profile-based selection at runtime, no compilation needed