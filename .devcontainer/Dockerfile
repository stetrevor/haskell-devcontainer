FROM mcr.microsoft.com/devcontainers/base:ubuntu

# Install system dependencies required by GHC and common Haskell libraries
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
    build-essential \
    curl \
    libffi-dev \
    libffi8 \
    libgmp-dev \
    libgmp10 \
    libncurses-dev \
    libncurses6 \
    libtinfo6 \
    pkg-config \
    zlib1g-dev \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Switch to the non-root vscode user provided by the base image
USER vscode
ENV PATH="/home/vscode/.ghcup/bin:${PATH}"

# Install GHCup, GHC 9.10.3, Cabal, and HLS, then prune caches, docs, profiling libs, and unused HLS versions
RUN curl --proto '=https' --tlsv1.2 -sSf https://get-ghcup.haskell.org | \
    BOOTSTRAP_HASKELL_NONINTERACTIVE=1 \
    BOOTSTRAP_HASKELL_GHC_VERSION=9.10.3 \
    BOOTSTRAP_HASKELL_CABAL_VERSION=recommended \
    BOOTSTRAP_HASKELL_INSTALL_HLS=1 \
    BOOTSTRAP_HASKELL_ADJUST_BASHRC=P \
    sh \
    && GHC_VER="$(/home/vscode/.ghcup/bin/ghc --numeric-version)" \
    && rm -rf /home/vscode/.ghcup/cache/* \
    /home/vscode/.ghcup/logs/* \
    /home/vscode/.ghcup/stack \
    /home/vscode/.ghcup/ghc/*/share/doc \
    /home/vscode/.ghcup/ghc/*/share/man \
    # Remove GHC profiling libraries
    && find /home/vscode/.ghcup/ghc -type f \( -name "*_p.a" -o -name "*.p_hi" \) -delete \
    # Keep only exact top-level HLS version directory matching $GHC_VER (preserves libHSghc-lib-parser-ex-*)
    && find /home/vscode/.ghcup/hls/*/lib/haskell-language-server-*/lib -mindepth 1 -maxdepth 1 \
    ! -name "${GHC_VER}" -exec rm -rf {} + \
    # Keep only the HLS binary for $GHC_VER and the wrapper
    && find /home/vscode/.ghcup/hls/*/bin -mindepth 1 -maxdepth 1 \
    ! -name "haskell-language-server-${GHC_VER}" \
    ! -name "haskell-language-server-wrapper" \
    -exec rm -rf {} + \
    # Remove static archives and interface files inside HLS (HLS only uses .so at runtime)
    && find /home/vscode/.ghcup/hls -type f \( -name "*.a" -o -name "*.hi" -o -name "*.dyn_hi" \) -delete \
    # Strip debug symbols from shared libraries and executables
    && find /home/vscode/.ghcup -type f \( -name "*.so*" -o -executable \) \
    -exec strip --strip-unneeded {} + 2>/dev/null || true \
    # Remove dangling symlinks left behind in ~/.ghcup/bin
    && find -L /home/vscode/.ghcup/bin -type l -delete
