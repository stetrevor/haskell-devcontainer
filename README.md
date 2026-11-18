# Haskell Dev Container (`ghcr.io/stetrevor/haskell-dev`)

Minimal Ubuntu-based Haskell dev container image (~670–720 MB compressed) with stripped binaries, pruned HLS targets, and no baked-in Hackage cache.

## Included Toolchain

- **GHC:** `9.10.3`
- **Cabal:** `3.16.1.0`
- **Haskell Language Server (HLS):** `2.14.0.0` (built for GHC `9.10.3`, with built-in `fourmolu` formatting)
- **Formatters:** `cabal-gild` (pre-installed in `/home/vscode/.ghcup/bin`)
- **Tags:** `ghcr.io/stetrevor/haskell-dev:nano`, `ghcr.io/stetrevor/haskell-dev:ghc-9.10.3`

## Quick Start in Any Haskell Project

1. Download the template `devcontainer.json` into your project root:

   ```bash
   mkdir -p .devcontainer
   curl -fsSL https://raw.githubusercontent.com/stetrevor/haskell-devcontainer/main/examples/.devcontainer/devcontainer.json \
     -o .devcontainer/devcontainer.json
   ```

2. Open the project in VS Code and run **Dev Containers: Reopen in Container** (or **Rebuild Container**).

3. If this is your first project using the shared `haskell-cabal-global` volume, populate the Hackage index once inside the container terminal:

   ```bash
   cabal update
   ```

## How Caching Works

The template mounts a named Docker volume (`haskell-cabal-global`) to `/home/vscode/.cabal`. All projects using this configuration share the same Hackage package index, precompiled `/home/vscode/.cabal/store`, and any executables installed into `/home/vscode/.cabal/bin` (on `PATH`) without inflating the base image or rebuilding dependencies per project.
