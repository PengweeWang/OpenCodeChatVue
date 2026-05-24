# OpenCode API Documentation

**Spec:** OpenAPI 3.1.0 | Title: "opencode" | Version: 1.0.0 | Description: "opencode api"

**Scale:** 113 paths, 131 operations, 290 schemas

**Base URL:** `http://localhost:4096`

---

## 1. ALL API ENDPOINTS

### Control (`/auth`, `/log`)

| Method | Path | Description |
|--------|------|-------------|
| `PUT` | `/auth/{providerID}` | Set authentication credentials for a provider (OAuth, API key, or well-known) |
| `DELETE` | `/auth/{providerID}` | Remove authentication credentials for a provider |
| `POST` | `/log` | Write a log entry (service, level, message, extra) |

### Global (`/global/*`)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/global/health` | Health check (returns `{healthy: true, version}`) |
| `GET` | `/global/event` | Subscribe to global server-sent events (SSE stream of `GlobalEvent`) |
| `GET` | `/global/config` | Get global configuration |
| `PATCH` | `/global/config` | Update global configuration |
| `POST` | `/global/dispose` | Dispose all instances, release resources |
| `POST` | `/global/upgrade` | Upgrade opencode (optional `target` version) |

### Config (`/config/*`)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/config` | Get current configuration |
| `PATCH` | `/config` | Update configuration |
| `GET` | `/config/providers` | List configured providers and default models |

### Event (`/event`)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/event` | Subscribe to instance events via SSE (`text/event-stream`) |

### Session (`/session/*`) -- 27 endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/session` | List sessions (params: `directory`, `workspace`, `scope`, `path`, `roots`, `start`, `search`, `limit`) |
| `POST` | `/session` | Create a new session (body: `parentID`, `title`, `agent`, `model`, `permission`, `workspaceID`) |
| `GET` | `/session/status` | Get status of all sessions (idle, retry, running, etc.) |
| `GET` | `/session/{sessionID}` | Get session details |
| `DELETE` | `/session/{sessionID}` | Delete session and all data |
| `PATCH` | `/session/{sessionID}` | Update session (title, permission, archived time) |
| `GET` | `/session/{sessionID}/children` | Get forked child sessions |
| `GET` | `/session/{sessionID}/todo` | Get session todo list |
| `GET` | `/session/{sessionID}/diff` | Get file diffs for a message |
| `GET` | `/session/{sessionID}/message` | List messages (params: `limit`, `before`) |
| `POST` | `/session/{sessionID}/message` | **Send message** (streaming AI response; body: `parts[]`, optional `model`, `agent`, `tools`, `format`, `system`) |
| `GET` | `/session/{sessionID}/message/{messageID}` | Get single message with parts |
| `DELETE` | `/session/{sessionID}/message/{messageID}` | Delete a message |
| `POST` | `/session/{sessionID}/fork` | Fork session at a message point |
| `POST` | `/session/{sessionID}/abort` | Abort active session |
| `POST` | `/session/{sessionID}/init` | Initialize session (analyze app, create AGENTS.md) |
| `POST` | `/session/{sessionID}/share` | Create shareable link |
| `DELETE` | `/session/{sessionID}/share` | Remove shareable link |
| `POST` | `/session/{sessionID}/summarize` | AI-compacted session summary |
| `POST` | `/session/{sessionID}/prompt_async` | Send message asynchronously (fire-and-forget) |
| `POST` | `/session/{sessionID}/command` | Send a command for execution |
| `POST` | `/session/{sessionID}/shell` | Run shell command in session context |
| `POST` | `/session/{sessionID}/revert` | Revert a message (undo file changes) |
| `POST` | `/session/{sessionID}/unrevert` | Restore reverted messages |
| `POST` | `/session/{sessionID}/permissions/{permissionID}` | Respond to permission request (`once`/`always`/`reject`) |
| `DELETE` | `/session/{sessionID}/message/{messageID}/part/{partID}` | Delete a message part |
| `PATCH` | `/session/{sessionID}/message/{messageID}/part/{partID}` | Update a message part |

### Message (`/session/{sessionID}/message`)

Messages contain an `info` (UserMessage or AssistantMessage) and `parts[]` (array of Part types).

### Provider (`/provider/*`)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/provider` | List all providers (all, default, connected) |
| `GET` | `/provider/auth` | Get auth methods for all providers |
| `POST` | `/provider/{providerID}/oauth/authorize` | Start OAuth flow |
| `POST` | `/provider/{providerID}/oauth/callback` | Handle OAuth callback |

### Permission (`/permission/*`)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/permission` | List pending permission requests |
| `POST` | `/permission/{requestID}/reply` | Reply with `once`/`always`/`reject` |

### Question (`/question/*`)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/question` | List pending questions |
| `POST` | `/question/{requestID}/reply` | Reply with answers |
| `POST` | `/question/{requestID}/reject` | Reject question |

### File (`/file/*`, `/find/*`)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/find` | Text search via ripgrep (`pattern` required) |
| `GET` | `/find/file` | Find files by name/pattern |
| `GET` | `/find/symbol` | LSP symbol search |
| `GET` | `/file` | List files/directories at path |
| `GET` | `/file/content` | Read file content (text or binary, with diff/patch) |
| `GET` | `/file/status` | Git file status |

### Instance (`/instance/*`, `/path`, `/vcs/*`, `/command`, `/agent`, `/skill`, `/lsp`, `/formatter`)

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/instance/dispose` | Dispose current instance |
| `GET` | `/path` | Get paths (home, state, config, worktree, directory) |
| `GET` | `/vcs` | Get VCS info (branch, default_branch) |
| `GET` | `/vcs/status` | Get changed files |
| `GET` | `/vcs/diff` | Get diff (mode: `git` or `branch`) |
| `GET` | `/vcs/diff/raw` | Get raw patch (`text/x-diff`) |
| `POST` | `/vcs/apply` | Apply a patch |
| `GET` | `/command` | List available commands |
| `GET` | `/agent` | List AI agents |
| `GET` | `/skill` | List skills |
| `GET` | `/lsp` | Get LSP server status |
| `GET` | `/formatter` | Get formatter status |

### MCP (`/mcp/*`)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/mcp` | Get MCP server status map |
| `POST` | `/mcp` | Add MCP server (local or remote config) |
| `POST` | `/mcp/{name}/auth` | Start MCP OAuth flow |
| `DELETE` | `/mcp/{name}/auth` | Remove MCP OAuth credentials |
| `POST` | `/mcp/{name}/auth/callback` | Complete OAuth with code |
| `POST` | `/mcp/{name}/auth/authenticate` | Full OAuth flow (opens browser) |
| `POST` | `/mcp/{name}/connect` | Connect MCP server |
| `POST` | `/mcp/{name}/disconnect` | Disconnect MCP server |

### Project (`/project/*`)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/project` | List all projects |
| `GET` | `/project/current` | Get current project |
| `POST` | `/project/git/init` | Initialize git repo |
| `PATCH` | `/project/{projectID}` | Update project (name, icon, commands) |

### PTY (`/pty/*`)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/pty/shells` | List available shells |
| `GET` | `/pty` | List PTY sessions |
| `POST` | `/pty` | Create PTY session |
| `GET` | `/pty/{ptyID}` | Get PTY session |
| `PUT` | `/pty/{ptyID}` | Update PTY (title, size) |
| `DELETE` | `/pty/{ptyID}` | Remove PTY session |
| `POST` | `/pty/{ptyID}/connect-token` | Create WebSocket token for PTY |
| `GET` | `/pty/{ptyID}/connect` | Connect to PTY session (WebSocket) |

### Sync (`/sync/*`)

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/sync/start` | Start workspace sync |
| `POST` | `/sync/replay` | Replay sync event history |
| `POST` | `/sync/steal` | Move session into workspace |
| `POST` | `/sync/history` | List sync events (event sourcing) |

### V2 / API (`/api/*`)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/api/session` | List sessions (paginated with cursor) |
| `POST` | `/api/session/{sessionID}/prompt` | Send prompt (body: `prompt`, `delivery`) |
| `POST` | `/api/session/{sessionID}/compact` | Compact session |
| `POST` | `/api/session/{sessionID}/wait` | Wait for idle |
| `GET` | `/api/session/{sessionID}/context` | Get active context messages |
| `GET` | `/api/session/{sessionID}/message` | Get messages (paginated with cursor) |
| `GET` | `/api/model` | List available models |
| `GET` | `/api/provider` | List providers |
| `GET` | `/api/provider/{providerID}` | Get single provider |

### TUI (`/tui/*`) -- Terminal UI control

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/tui/append-prompt` | Append text to prompt |
| `POST` | `/tui/submit-prompt` | Submit prompt |
| `POST` | `/tui/clear-prompt` | Clear prompt |
| `POST` | `/tui/execute-command` | Execute TUI command |
| `POST` | `/tui/show-toast` | Show toast notification |
| `POST` | `/tui/publish` | Publish TUI event |
| `POST` | `/tui/select-session` | Navigate to session |
| `POST` | `/tui/open-help` | Open help dialog |
| `POST` | `/tui/open-sessions` | Open sessions dialog |
| `POST` | `/tui/open-themes` | Open themes dialog |
| `POST` | `/tui/open-models` | Open models dialog |
| `GET` | `/tui/control/next` | Get next TUI request from queue |
| `POST` | `/tui/control/response` | Submit TUI response |

### Workspace (`/experimental/workspace/*`)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/experimental/workspace/adapter` | List workspace adapters |
| `GET` | `/experimental/workspace` | List workspaces |
| `POST` | `/experimental/workspace` | Create workspace |
| `POST` | `/experimental/workspace/sync-list` | Sync workspace list |
| `GET` | `/experimental/workspace/status` | Get workspace connection status |
| `DELETE` | `/experimental/workspace/{id}` | Remove workspace |
| `POST` | `/experimental/workspace/warp` | Warp session into workspace |

### Experimental (tools, worktrees, sessions, resources, console)

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/experimental/console` | Get Console provider metadata |
| `GET` | `/experimental/console/orgs` | List switchable Console orgs |
| `POST` | `/experimental/console/switch` | Switch active Console org |
| `GET` | `/experimental/tool` | List tools (requires `provider` and `model` query params) |
| `GET` | `/experimental/tool/ids` | List all tool IDs |
| `GET` | `/experimental/worktree` | List worktrees |
| `POST` | `/experimental/worktree` | Create worktree |
| `DELETE` | `/experimental/worktree` | Remove worktree |
| `POST` | `/experimental/worktree/reset` | Reset worktree |
| `GET` | `/experimental/session` | List sessions (global, cross-project) |
| `GET` | `/experimental/resource` | Get MCP resources |

---

## 2. REQUEST/RESPONSE FORMATS

**Content Types:**
- All endpoints use `application/json` for request and response bodies
- Event streams (`GET /event`, `GET /global/event`) use `text/event-stream` (SSE)
- Raw VCS diff (`GET /vcs/diff/raw`) returns `text/x-diff; charset=utf-8`

**Common Query Parameters** (on nearly all endpoints):
- `directory` (optional string) -- working directory
- `workspace` (optional string) -- workspace identifier

**ID Patterns:**
- Sessions: `^ses` prefix (e.g., `ses_abc123`)
- Messages: `^msg` prefix
- Parts: `^prt` prefix
- Permissions: `^per` prefix
- Questions: `^que` prefix
- PTY: `^pty` prefix
- Workspaces: `^wrk` prefix

**Pagination:**
- V2 endpoints (`/api/*`) use cursor-based pagination: `limit`, `order` (`asc`/`desc`), `cursor` (opaque string)
- Responses include `cursor.previous` and `cursor.next` for navigation
- Legacy session listing uses `start`/`limit` offset pagination

**Output Formats** (for message sending):
- `OutputFormatText` -- `{type: "text"}`
- `OutputFormatJsonSchema` -- `{type: "json_schema", schema: {...}, retryCount: N}`

---

## 3. AUTHENTICATION REQUIREMENTS

**No top-level security schemes are defined** in the OpenAPI spec (no `securitySchemes` or `security` section). The API is designed to run as a local serve instance (localhost:4096) and does not enforce API-level authentication.

However, the API manages authentication for **external providers**:

**Auth Credential Types** (set via `PUT /auth/{providerID}`):
- **OAuth** (`type: "oauth"`) -- `refresh`, `access`, `expires`, optional `accountId`, `enterpriseUrl`
- **API Key** (`type: "api"`) -- `key`, optional `metadata`
- **Well-Known** (`type: "wellknown"`) -- `key`, `token`

**Provider OAuth Flow:**
1. `GET /provider/auth` -- discover available auth methods per provider
2. `POST /provider/{providerID}/oauth/authorize` -- start OAuth (returns URL + instructions)
3. `POST /provider/{providerID}/oauth/callback` -- complete with authorization code

**MCP Server OAuth Flow:**
1. `POST /mcp/{name}/auth` -- start OAuth (returns `authorizationUrl` + `oauthState`)
2. `POST /mcp/{name}/auth/callback` -- complete with code
3. Or: `POST /mcp/{name}/auth/authenticate` -- full flow (opens browser)

**V2 API** endpoints return `401 Unauthorized` errors, suggesting they may support or require authentication.

---

## 4. KEY SCHEMAS / MODELS

### Core Domain Models

**Session** -- Central entity with `id` (^ses), `slug`, `projectID`, `workspaceID` (^wrk), `directory`, `path`, `parentID`, `title`, `cost`, `tokens` (input/output/reasoning/cache), `summary` (additions/deletions/files/diffs), `share` (url), time fields.

**Message** -- Union of `UserMessage` and `AssistantMessage`:
- **UserMessage**: `id` (^msg), `sessionID`, `role: "user"`, `time.created`, `format`, `summary`
- **AssistantMessage**: `id`, `sessionID`, `role: "assistant"`, `agent`, `model` (id/providerID/variant), `content[]` (text/reasoning/tool), `snapshot`, `time` (created/completed)

**Part** -- Union of 12 types:
- `TextPart` -- text content with timing
- `ToolPart` -- tool call with `callID`, `tool` name, `state` (pending/running/completed/error)
- `ReasoningPart` -- model reasoning text
- `FilePart` -- file attachment (mime, url, source)
- `SubtaskPart` -- delegated subtask (prompt, agent, model)
- `StepStartPart` / `StepFinishPart` -- step boundaries with cost/tokens
- `SnapshotPart` -- file snapshot reference
- `PatchPart` -- git patch (hash, files)
- `AgentPart` -- agent switch
- `RetryPart` -- retry attempt with error
- `CompactionPart` -- context compaction marker

**Prompt** -- Input for sending messages: `text` (required), `files[]`, `agents[]`, `references[]`

**ToolState** -- Union: `ToolStatePending` (input, raw), `ToolStateRunning` (input, title, time.start), `ToolStateCompleted` (input, output, title, attachments), `ToolStateError` (input, error)

### Provider/Model

**Provider**: `id`, `name`, `source` (env/config/custom/api), `env[]`, `key`, `options`, `models` map
**Model**: `id`, `providerID`, `api` (id/url/npm), `name`, `family`, `capabilities` (temperature/reasoning/attachment/toolcall, input types, output types)
**ModelV2Info** (v2): `id`, `apiID`, `providerID`, `family`, `name`, `endpoint` (unknown or openai/responses), `capabilities`
**ProviderV2Info** (v2): `id`, `name`, `enabled` (false, or via env/account)

### Agent/Command

**Agent**: `name`, `description`, `mode` (subagent/primary/all), `native`, `hidden`, `topP`, `temperature`, `color`, `permission` (ruleset), `model`, `variant`, `prompt`, `options`, `steps`
**Command**: `name`, `description`, `agent`, `model`, `source` (command/mcp/skill), `template`, `subtask`, `hints[]`

### Permission System

**PermissionRequest**: `id` (^per), `sessionID`, `permission`, `patterns[]`, `metadata`, `always[]`, `tool` (messageID/callID)
**PermissionAction**: `"allow"` | `"deny"` | `"ask"`
**PermissionRule**: `permission`, `pattern`, `action`
**PermissionRuleset**: array of PermissionRule
**PermissionConfig**: Per-tool rules for read/edit/glob/grep/list/bash/task/todowrite/question/webfetch/websearch/repo_clone/repo_overview/lsp/external_directory

### Question System

**QuestionRequest**: `id` (^que), `sessionID`, `questions[]` (question, header, options, multiple, custom)
**QuestionOption**: `label`, `description`
**QuestionAnswer**: array of selected label strings

### Event System

**73 event types** across categories:
- **Session lifecycle**: `session.created`, `session.updated`, `session.deleted`, `session.compacted`, `session.diff`, `session.error`, `session.idle`, `session.status`
- **Session streaming**: `session.next.prompted`, `session.next.text.started/delta/ended`, `session.next.tool.called/input.started/input.delta/input.ended/progress/success/failed`, `session.next.step.started/ended/failed`, `session.next.reasoning.started/delta/ended`, `session.next.compaction.started/delta/ended`, `session.next.shell.started/ended`, `session.next.model.switched`, `session.next.agent.switched`, `session.next.retried`, `session.next.synthetic`
- **Message**: `message.updated`, `message.removed`, `message.part.updated`, `message.part.removed`, `message.part.delta`
- **Infrastructure**: `file.edited`, `file.watcher.updated`, `project.updated`, `lsp.updated`, `lsp.client.diagnostics`, `mcp.tools.changed`, `mcp.browser.open.failed`, `command.executed`, `vcs.branch.updated`, `pty.created/updated/exited/deleted`, `question.asked/replied/rejected`, `permission.asked/replied`, `todo.updated`, `worktree.ready/failed`, `workspace.ready/failed/status`, `installation.updated/update-available`, `account.added/removed/switched`, `catalog.model.updated`, `models-dev.refreshed`, `server.instance.disposed`, `server.connected`, `global.disposed`
- **TUI**: `tui.prompt.append`, `tui.command.execute`, `tui.toast.show`, `tui.session.select`

### Sync (Event Sourcing)

Sync events have `type: "sync"`, `name` (e.g., `session.created.1`), `id`, `seq`, `aggregateID`, `data`. Used for workspace synchronization via `/sync/history`, `/sync/replay`, `/sync/steal`.

### File/VCS

**FileContent**: `type` (text/binary), `content`, `diff`, `patch` (with hunks)
**FileNode**: `name`, `path`, `absolute`, `type` (file/directory), `ignored`
**VcsInfo**: `branch`, `default_branch`
**VcsFileDiff/VcsFileStatus**: `file`, `patch`, `additions`, `deletions`, `status` (added/deleted/modified)

### MCP (Model Context Protocol)

**MCPStatus**: Union of `MCPStatusConnected`, `MCPStatusDisabled`, `MCPStatusFailed`, `MCPStatusNeedsAuth`, `MCPStatusNeedsClientRegistration`
**McpLocalConfig**: `type: "local"`, `command[]`, `environment`, `enabled`, `timeout`
**McpRemoteConfig**: `type: "remote"`, `url`, `enabled`, `headers`, `oauth` (McpOAuthConfig or false), `timeout`

### Workspace/Worktree

**Workspace**: `id` (^wrk), `type`, `name`, `branch`, `directory`, `extra`, `projectID`, `timeUsed`
**Worktree**: `name`, `branch`, `directory`

### Error Schemas

- `BadRequestError`, `NotFoundError`, `UnauthorizedError`, `InvalidRequestError`, `InvalidCursorError`
- `SessionNotFoundError`, `SessionBusyError`, `PermissionNotFoundError`, `QuestionNotFoundError`, `ProjectNotFoundError`, `PtyNotFoundError`, `McpServerNotFoundError`
- `APIError` (with statusCode, isRetryable, responseHeaders, responseBody)
- `ProviderAuthError`, `McpUnsupportedOAuthError`, `PtyForbiddenError`, `WorktreeError`, `VcsApplyError`, `WorkspaceWarpError`
- Effect framework errors: `effect_HttpApiError_BadRequest`, `effect_HttpApiError_Forbidden`, `effect_HttpApiError_InternalServerError`

---

## 5. KEY ARCHITECTURAL NOTES

1. **Local-first design**: The API runs on localhost (default port 4096), no auth required for API access itself
2. **Dual API versions**: Legacy v1 endpoints and newer `/api/*` v2 endpoints with cursor-based pagination
3. **Event-driven**: Heavy use of SSE for real-time streaming of session events, message parts, and tool execution progress
4. **Event sourcing**: Sync system uses sequenced events for workspace state synchronization
5. **Multi-workspace**: Supports multiple workspaces/worktrees per project with session warping between them
6. **Permission model**: Fine-grained per-tool permissions (allow/deny/ask) with pattern matching
7. **Tool state machine**: Tools progress through pending -> running -> completed/error states with full input/output tracking
