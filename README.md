# mcp-servers

Index of a suite of Model Context Protocol (MCP) servers that give LLM agents
scoped access to infrastructure and development systems. Each server lives in
its own repository and is written in Python.

## Servers

| Server | Purpose | Access |
| --- | --- | --- |
| [k8s-mcp](https://github.com/dafunn/k8s-mcp) | Kubernetes resources, events, and optional pod logs | Read-only |
| [loki-mcp](https://github.com/dafunn/loki-mcp) | Log queries against Loki, including logs from pods that no longer exist | Read-only |
| [pg-mcp](https://github.com/dafunn/pg-mcp) | PostgreSQL curated queries, optional metadata, and optional read-only SQL | Read-only |
| [forgejo-mcp](https://github.com/dafunn/forgejo-mcp) | Forgejo Actions CI/CD: runs, jobs, and build logs | Read-only |
| [repo-mcp](https://github.com/dafunn/repo-mcp) | Cross-repository git research: tree, glob, grep, read, log, and diff | Read-only |
| [base-mcp](https://github.com/dafunn/base-mcp) | Filesystem and HTTP tools for an agent in a locked-down container | Read; writes only when enabled |
| [exec-mcp](https://github.com/dafunn/exec-mcp) | Runs a build task's declared commands, plus git tools | Executes only manifest-declared commands |

## Shared design

- **Deny by default.** Each server enforces its own scope: namespace, database,
  repository, filesystem root, or host allowlists. The model cannot widen that
  scope through tool arguments.
- **Tools exposed by configuration.** Environment flags decide which tools a
  deployment registers, so sensitive capabilities such as pod logs, ConfigMaps,
  raw SQL, or filesystem writes stay off unless explicitly enabled.
- **One identity per deployment.** Servers that need credentials run as a single
  Kubernetes ServiceAccount, Postgres role, or token, and that identity's
  permissions are the outer boundary. exec-mcp holds no credentials at all.
- **Bounded output.** Row, line, and byte caps keep responses within agent
  context limits; larger results are truncated or paged rather than dropped
  silently.
- **Transports.** The servers run over stdio for a local client or SSE for
  in-cluster use, selected with `MCP_TRANSPORT`. exec-mcp ships as a container
  image started by its build launcher.

See each repository's README for tools, environment variables, and deployment
details.
