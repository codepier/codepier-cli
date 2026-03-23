<p align="center">
  <picture>
    <source media="(prefers-color-scheme: light)" srcset=".github/logo-dark.svg">
    <source media="(prefers-color-scheme: dark)" srcset=".github/logo-light.svg">
    <img alt="CodePier" src=".github/logo-light.svg" width="320">
  </picture>
</p>

<h3 align="center">Swap Kubernetes pods with your local dev environment</h3>

<p align="center">
  <a href="https://github.com/codepier/codepier-cli/releases/latest"><img alt="Latest Release" src="https://img.shields.io/github/v/release/codepier/codepier-cli?style=flat-square&color=blue"></a>
  <img alt="Platforms" src="https://img.shields.io/badge/platform-macOS%20%7C%20Linux-lightgrey?style=flat-square">
  <a href="https://codepier.dev"><img alt="Website" src="https://img.shields.io/badge/codepier.dev-visit-blue?style=flat-square"></a>
</p>

---

**CodePier CLI** lets you develop locally against live Kubernetes clusters. Swap any running pod with your local machine — your code syncs in real-time, ports forward automatically, and everything cleans up when you're done.

No containers to rebuild. No waiting for CI. Just code and reload.

## Features

- **Hot-reload development** — Bidirectional file sync via [Mutagen](https://mutagen.io) keeps your local code in the cluster
- **Pod swap & restore** — Seamlessly replace Deployments or StatefulSets, restore originals with one command
- **SSH access** — Drop into any running pod instantly
- **Log streaming** — Tail pod logs in real-time from your terminal
- **HTTPS proxy** — Local reverse proxy with auto-generated TLS certificates for K8s services
- **Port forwarding** — Forward cluster ports to localhost
- **Self-updating** — Built-in update mechanism, no package manager needed
- **Interactive UX** — Guided prompts for namespace, deployment, and container selection

## Installation

Download the latest binary for your platform from [Releases](https://github.com/codepier/codepier-cli/releases/latest):

```bash
# macOS (Apple Silicon)
curl -fsSL https://github.com/codepier/codepier-cli/releases/latest/download/codepier-darwin-arm64 -o /usr/local/bin/codepier
chmod +x /usr/local/bin/codepier

# macOS (Intel)
curl -fsSL https://github.com/codepier/codepier-cli/releases/latest/download/codepier-darwin-x64 -o /usr/local/bin/codepier
chmod +x /usr/local/bin/codepier

# Linux (x64)
curl -fsSL https://github.com/codepier/codepier-cli/releases/latest/download/codepier-linux-x64 -o /usr/local/bin/codepier
chmod +x /usr/local/bin/codepier

# Linux (ARM64)
curl -fsSL https://github.com/codepier/codepier-cli/releases/latest/download/codepier-linux-arm64 -o /usr/local/bin/codepier
chmod +x /usr/local/bin/codepier
```

To update later, just run:

```bash
codepier update
```

## Quick Start

**1. Create a `codepier.yaml`** in your project root:

```yaml
namespaces: my-namespace
deployment: my-api

sync:
  - "./src:/app/src"

forward:
  - "3000:3000"

image: node:20-alpine

ignore:
  - "node_modules"
  - ".git"

cache:
  - "npm:node_modules"

env:
  - name: NODE_ENV
    value: development
```

**2. Swap and develop:**

```bash
codepier up
```

Your local `./src` folder is now live inside the cluster pod. Edit files locally, and changes appear instantly in the running container.

**3. When you're done:**

```bash
codepier down
```

The original deployment is restored — no trace left behind.

## Commands

| Command | Description |
|---------|-------------|
| `codepier up` | Swap a K8s pod with your local dev environment |
| `codepier down` | Stop dev swaps and restore original deployments |
| `codepier ssh` | SSH into a running pod |
| `codepier tail` | Stream pod logs in real-time |
| `codepier proxy` | Start an HTTPS reverse proxy to K8s services |
| `codepier clean` | Remove orphaned sync folders from cluster nodes |
| `codepier mutagen` | Debug or control the Mutagen file sync layer |
| `codepier sync-status` | Display current file sync status |
| `codepier update` | Update the CLI to the latest version |

### Global Options

| Option | Description |
|--------|-------------|
| `--config <path>` | Path to kubeconfig file |
| `--context <name>` | Kubernetes context to use |
| `--namespace <name>` | Skip namespace selection prompt |
| `--deployment <name>` | Skip deployment selection prompt |
| `--container <name>` | Skip container selection prompt |
| `--temp-dir` | Use emptyDir instead of hostPath for sync volumes |

## Configuration

The `codepier.yaml` file configures how your local environment maps to the cluster:

| Field | Type | Description |
|-------|------|-------------|
| `namespaces` | `string \| string[]` | Kubernetes namespace(s) to target |
| `deployment` | `string \| string[]` | Deployment or StatefulSet name(s) |
| `kind` | `string` | Workload kind: `Deployment` (default) or `StatefulSet` |
| `sync` | `string[]` | Folder mappings, e.g. `"./src:/app/src"` |
| `image` | `string \| object` | Container image to use for the swap |
| `forward` | `string[]` | Port forwards, e.g. `"3000:3000"` |
| `ignore` | `string[]` | File patterns to exclude from sync |
| `cache` | `string[]` | Persistent cache paths, e.g. `"npm:node_modules"` |
| `workdir` | `string` | Working directory inside the container |
| `supplemental` | `boolean` | Add as a sidecar instead of replacing the main container |
| `env` | `array` | Environment variables (static values or forwarded from host) |
| `proxy` | `array` | HTTPS proxy route definitions |

## Requirements

- A running **Kubernetes cluster** with `kubectl` configured

## Links

- [CodePier Desktop App](https://github.com/codepier/codepier) — The visual command center for Kubernetes
- [Website](https://codepier.dev) — Learn more about CodePier
