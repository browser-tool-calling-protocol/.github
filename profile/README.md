# Browser Tool Calling Protocol (BTCP)

<p align="center">
  <strong>A protocol that lets AI agents call any browser tool - securely</strong>
</p>

The **Browser Tool Calling Protocol (BTCP)** is an open standard that enables AI agents to call tools within the browser through client-defined interfaces. Like MCP, tools are discoverable with schemas for AI awareness - but BTCP tools are defined and executed on the client side, enabling direct access to browser state, DOM, and web applications without server round-trips.

---

## Getting Started

* **Read the [BTCP Specification](https://github.com/browser-tool-calling-protocol/btcp-specification)** for the complete technical specification, security model, and protocol details

* **Install the [Chrome Extension](https://github.com/browser-tool-calling-protocol/btcp-chrome)** to enable BTCP in your browser

* **Use the [JavaScript Client](https://github.com/browser-tool-calling-protocol/btcp-client)** to integrate BTCP into your application

---

## Usage Options

### Server-Side

| Option | Description |
|--------|-------------|
| **[BTCP Server](https://github.com/browser-tool-calling-protocol/btcp-server)** | Deploy the BTCP server on your infrastructure for browser tool coordination |

### Client-Side

| Option | Description |
|--------|-------------|
| **[BTCP Client](https://github.com/browser-tool-calling-protocol/btcp-client)** | Integrate the JavaScript client directly into your application |
| **[Chrome Extension](https://github.com/browser-tool-calling-protocol/btcp-chrome)** | Install the Chrome extension to expose browser functionality to AI assistants |
| **[Browser Agent](https://github.com/browser-tool-calling-protocol/btcp-browser-agent)** | Browser-based agent utilities for automation |
| **[Code Mode](https://github.com/browser-tool-calling-protocol/btcp-code-mode)** | Plug-and-play library to enable agents to call BTCP tools via code execution |

---

## Privacy & Security

BTCP is designed with privacy and security as first-class concerns:

### 100% Open Source

All code is open source under MPL-2.0 - fully auditable and transparent. No hidden behaviors, no proprietary components.

### End-to-End Encryption

Built-in E2E encryption prevents sensitive information from being exposed to servers or LLMs. Your browser data stays private.

### Capability-Based Permissions

Every tool must declare its required capabilities upfront (network, storage, DOM access, etc.). Users see exactly what each tool can access before granting permission - 100% capability awareness.

```
┌─────────────────────────────────────────────────────────────────┐
│  Tool "gmail" requests the following capabilities:              │
│                                                                 │
│  ✓ Network access (mail.google.com)                            │
│  ✓ Read page content                                           │
│  ✓ Modify page content                                         │
│                                                                 │
│  [Allow Once]  [Allow Always]  [Deny]                          │
└─────────────────────────────────────────────────────────────────┘
```

---

## Project Structure

### Core

| Repository | Description |
|------------|-------------|
| [`btcp-specification`](https://github.com/browser-tool-calling-protocol/btcp-specification) | The specification for the Browser Tool Calling Protocol |
| [`btcp-client`](https://github.com/browser-tool-calling-protocol/btcp-client) | Browser Tool Calling Protocol JavaScript client |
| [`btcp-server`](https://github.com/browser-tool-calling-protocol/btcp-server) | Browser Tool Calling Protocol server |

### Tools & Extensions

| Repository | Description |
|------------|-------------|
| [`btcp-chrome`](https://github.com/browser-tool-calling-protocol/btcp-chrome) | Chrome extension that exposes browser functionality to AI assistants |
| [`btcp-code-mode`](https://github.com/browser-tool-calling-protocol/btcp-code-mode) | Plug-and-play library to enable agents to call BTCP tools via code execution |
| [`btcp-browser-agent`](https://github.com/browser-tool-calling-protocol/btcp-browser-agent) | Browser-based agent utilities |

---

<details>
<summary><h2 style="display: inline;">BTCP vs MCP</h2></summary>

### BTCP's Core Principle

AI agents should be able to call browser-side tools with the same discoverability as server-side tools - but with direct access to browser context and client-defined flexibility.

### Core Requirements

* **Client-defined tools**: Tool interfaces are defined on the client, not the server - enabling flexible, context-aware tool providers
* **AI-aware schemas**: Tools expose schemas for introspection, just like MCP - AI agents can discover available tools and their parameters
* **No round-trip tax**: Multi-step workflows execute in a single request instead of one round-trip per tool call
* **Browser-native**: Tools have direct access to browser state, DOM, and web application context

### Key Differences

| Aspect | MCP | BTCP |
|--------|-----|------|
| Tool definition | Server-defined | Client-defined |
| Execution location | Server-side | Browser-side |
| Multi-step operations | O(n) round-trips | O(1) single execution |
| Browser context access | No | Yes |
| Tool discovery | Server exposes schema | Client exposes schema |

### When to Use Each

* **MCP**: Server-side operations, database access, external API calls
* **BTCP**: Browser-side tools, web app automation, client-state operations

**They work together**: Use MCP for server tools and BTCP for browser tools in the same agent.

</details>

---

<details>
<summary><h2 style="display: inline;">Client-Defined Tools</h2></summary>

### Tool Provider Interface

BTCP tools are defined by the client (browser) and exposed to AI agents with discoverable schemas:

```typescript
interface BTCPToolProvider {
  namespace: string;              // e.g., 'gmail', 'sheets'
  getInterface(): ToolInterface;  // Schema for AI discovery
  getAPI(): Record<string, Function>;
}

interface ToolInterface {
  name: string;
  version: string;
  methods: MethodSchema[];        // AI-readable method definitions
}
```

### AI Discovery

AI agents can introspect available tools at runtime:

```javascript
__interfaces  // Returns all tool schemas
__getToolInterface('gmail')  // Returns specific tool schema
```

### Flexibility

Unlike server-defined tools, BTCP tool providers can be:
- Dynamically registered based on the current page/application
- Customized per user or session
- Extended with application-specific capabilities

</details>

---

<details>
<summary><h2 style="display: inline;">Security Model</h2></summary>

### Sandbox Requirements

BTCP requires a secure sandbox environment that provides:

* **Global isolation**: No access to `window`, `document`, `globalThis`, or browser globals
* **Controlled APIs**: Only registered tool providers are accessible
* **Frozen interfaces**: Tool methods cannot be modified at runtime
* **Network isolation**: No direct access to `fetch`, `XMLHttpRequest`, or WebSocket

### Resource Limits

| Limit | Purpose |
|-------|---------|
| Execution timeout | Prevent infinite loops and runaway code |
| Operation count | Limit tool API calls per execution |
| Code size | Prevent memory exhaustion |
| Result size | Limit response payload |

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

We welcome issues, pull requests, and design discussion. **If you'd like to add support for another browser tool provider, sandbox implementation, or framework integration, open a discussion first so we can align on the design!**

---

## About

BTCP is an open-source protocol released under the **MPL-2.0** license. If your application needs AI agents that can call browser-side tools with strong security guarantees, we'd love to have you involved!

The protocol is designed to complement existing standards like MCP and UTCP - use BTCP for browser tools while using other protocols for server-side operations.
