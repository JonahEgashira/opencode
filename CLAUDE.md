# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Essential Commands

### Build Commands
```bash
# Standard build
go build -o opencode

# Snapshot build with multiple architectures
goreleaser build --clean --snapshot --skip validate
# or use the wrapper script
./scripts/snapshot

# Build with version information
go build -ldflags "-s -w -X github.com/opencode-ai/opencode/internal/version.Version=X.Y.Z" -o opencode
```

### Test Commands
```bash
# Run all tests
go test ./...

# Run with coverage
go test -cover ./...

# Run with verbose output
go test -v ./...
```

### Development Commands
```bash
# Run the application
go run .
# or with debug mode
go run . -d

# Generate SQL code from queries
sqlc generate

# Check for hidden characters (security check)
./scripts/check_hidden_chars.sh

# Create a new release tag
./scripts/release          # patch version
./scripts/release --minor  # minor version
```

## High-Level Architecture

OpenCode is a terminal-based AI assistant built with Go. The architecture follows these key patterns:

### Core Architecture
- **Service-Oriented**: Core functionality organized into services (sessions, messages, agents, permissions)
- **Event-Driven**: Pub/sub pattern for loose coupling between components
- **Provider Pattern**: Unified interface for multiple AI providers (Anthropic, OpenAI, Gemini, etc.)

### Key Subsystems

1. **Application Core (`internal/app/`)**
   - Central `App` struct orchestrates all services
   - Handles both interactive TUI and non-interactive modes
   - Manages LSP clients and agent lifecycle

2. **LLM System (`internal/llm/`)**
   - Provider abstraction for AI services
   - Agent types: coder, summarizer, task, title
   - Extensible tool framework
   - Prompt templates for different roles

3. **TUI System (`internal/tui/`)**
   - Built on Bubble Tea framework
   - Component-based architecture
   - Dialog system for overlays
   - Theme support with multiple built-in themes

4. **Database (`internal/db/`)**
   - SQLite with sqlc for type-safe queries
   - Tables: sessions, messages, files
   - Embedded migrations

5. **Tool System (`internal/llm/tools/`)**
   - File operations (view, write, edit, patch)
   - Code search (grep, glob)
   - Shell execution
   - LSP integration
   - MCP protocol support

### Configuration
- Hierarchical: defaults → global → local → environment
- Locations: `~/.opencode.json`, `./.opencode.json`
- Provider API keys and model selection
- LSP server configurations
- MCP server definitions

### Key Interfaces
- `Provider`: AI provider abstraction
- `BaseTool`: Tool interface for AI capabilities
- Service interfaces for session, message, agent, and permission management

## Important Notes

- This project is continuing development at Charm under a new name
- Uses Go 1.24.0 or higher
- No Makefile - uses Go toolchain and shell scripts
- Permission system requires user approval for tool operations
- Auto-compact feature summarizes conversations near context limits