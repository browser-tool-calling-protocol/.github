# Browser Tool Calling Protocol (BTCP)

<p align="center">
  <strong>A protocol that lets AI agents execute code directly in the browser - securely</strong>
</p>

The **Browser Tool Calling Protocol (BTCP)** is an open standard that enables AI agents to execute code directly in the browser within a secure sandbox environment. Instead of making multiple server round-trips for each tool call, BTCP allows complex multi-step workflows to execute in a single request, reducing latency from O(n) to O(1) for browser-based operations.

---

## Getting Started

* **Read the [RFC Specification](./RFC.md)** for the complete technical specification, security model, and protocol details

* **Read [About BTCP](./ABOUT.md)** for a conceptual overview, use cases, and architecture diagrams

* **MCP users:** Run the **BTCP-MCP Bridge** to execute browser code from any MCP-compatible agent → `btcp-mcp`

---

## Project Structure

| Repository | Description |
|------------|-------------|
| `btcp-rfc` | Formal specification, RFC and reference documentation |
| `btcp-client` | Browser-side executor interface (protocol-only) |
| `btcp-client-ses` | SES (Secure ECMAScript) sandbox implementation |
| `btcp-server-nodejs` | Node.js server with SSE command routing |
| `btcp-server-python` | Python server implementation |
| `btcp-mcp` | MCP server bridge for BTCP browser execution |
| `btcp-codemode` | Run complex workflows in one shot using BTCP sandbox |
| `btcp-chrome` | Chrome extension for BTCP-enabled browser automation |

---

<details>
<summary><h2 style="display: inline;">BTCP vs MCP</h2></summary>

### BTCP's Core Principle

If AI can describe what it wants to do, it should be able to express that as code and execute it directly in the browser - with strong security guarantees through sandboxed execution.

### Core Requirements

* **No round-trip tax**: BTCP executes multi-step workflows in a single request instead of one round-trip per tool call
* **Browser-native**: Code runs directly in the browser with access to browser-side state and context
* **Sandbox security**: Isolated execution environment with no access to globals, DOM, or unauthorized APIs
* **Tool provider model**: Controlled API surface through registered providers with introspection support

### Key Differences

| Aspect | MCP | BTCP |
|--------|-----|------|
| Execution location | Server-side | Browser-side |
| Multi-step operations | O(n) round-trips | O(1) single execution |
| Browser context access | No | Yes |
| Expressiveness | Tool definitions | Full code |
| Security model | Server-side isolation | Client-side sandbox |

### When to Use Each

* **MCP**: Server-side operations, database access, external API calls
* **BTCP**: Browser-side automation, UI interactions, client-state operations

**They work together**: Use MCP for server tools and BTCP for browser tools in the same agent.

</details>

---

<details>
<summary><h2 style="display: inline;">Security Model</h2></summary>

### Sandbox Requirements

BTCP requires a secure sandbox environment that provides:

* **Global isolation**: No access to `window`, `document`, `globalThis`, or browser globals
* **Controlled APIs**: Only registered tool providers are accessible to executed code
* **Frozen interfaces**: Tool methods cannot be modified at runtime
* **Network isolation**: No direct access to `fetch`, `XMLHttpRequest`, or WebSocket

### Resource Limits

| Limit | Purpose |
|-------|---------|
| Execution timeout | Prevent infinite loops and runaway code |
| Operation count | Limit tool API calls per execution |
| Code size | Prevent memory exhaustion |
| Result size | Limit response payload |

### Error Categories

* `syntax` - Code parse errors
* `runtime` - Execution exceptions
* `timeout` - Exceeded time limit
* `security` - Sandbox violation attempts
* `limit` - Resource limit exceeded

### Implementation Options

The protocol is agnostic to the specific sandbox implementation. Compliant implementations may use:

* SES (Secure ECMAScript) compartments
* Web Workers with restricted APIs
* iframe sandboxes
* WebAssembly-based isolates
* Custom JavaScript interpreters

</details>

---

<details>
<summary><h2 style="display: inline;">Transport Options</h2></summary>

BTCP is transport-agnostic and supports multiple communication channels:

* **SSE (Server-Sent Events)** - Primary transport for real-time browser communication
* **HTTP Polling** - Short and long polling for environments without SSE
* **WebSocket** - Bidirectional streaming for high-frequency interactions
* **Direct** - Function calls for testing and embedded scenarios

</details>

---

## Contributing

We welcome issues, pull requests, and design discussion. **If you'd like to add support for another sandbox implementation, language binding, or framework integration, open a discussion first so we can align on the design!**

---

## About

BTCP is an open-source protocol released under the **MPL-2.0** license. If your application needs low-latency AI-to-browser communication with strong security guarantees, we'd love to have you involved!

The protocol is designed to complement existing standards like MCP and UTCP - use BTCP for browser-side execution while using other protocols for server-side operations.
