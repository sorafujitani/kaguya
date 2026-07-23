# Kaguya

<img height="128" alt="WezTerm Icon" src="https://raw.githubusercontent.com/wezterm/wezterm/main/assets/icon/wezterm-icon.svg" align="left">

Kaguya is a personal fork of [WezTerm](https://github.com/wezterm/wezterm), a GPU-accelerated cross-platform terminal emulator and multiplexer written in Rust.

This repository is where I experiment with custom terminal features while keeping WezTerm's strengths as the base.

User-facing WezTerm documentation: https://wezterm.org/

![Screenshot](docs/screenshots/two.png)

*Screenshot of wezterm on macOS, running vim*

## About This Fork

Kaguya tracks upstream WezTerm and optimizes for a **local macOS daily driver**: smaller binaries, faster clean builds, and leaner startup defaults than a full upstream WezTerm build.

### Lean-by-default changes

* **Windows support removed** — less code and no Windows packaging assets
* **Slim font bundle** — JetBrains Mono (Regular/Bold/Italic) + Roboto + Nerd Symbols; emoji from the OS (`Apple Color Emoji`)
* **No MetalANGLE dylibs** in the macOS app tree (CGL / Metal path)
* **System OpenSSL** on macOS (via Homebrew) instead of vendoring OpenSSL into every clean build
* **Single SSH backend** (`libssh-rs`) by default
* **Builtin color schemes opt-in** — avoids embedding ~1000 TOML schemes (~730KB)
* **macOS startup defaults** — update checks off, `prefer_egl` off, mux SSH agent off
* **Release profile** — thin LTO, single codegen unit, symbol strip; faster macOS incremental `dev` builds via `split-debuginfo = "unpacked"`

### Build (macOS)

```console
git clone --recursive https://github.com/sorafujitani/kaguya.git
cd kaguya
./get-deps   # installs openssl@3 / pkgconf via Homebrew when needed
cargo build --release -p wezterm -p wezterm-gui -p wezterm-mux-server -p strip-ansi-escapes
```

Optional features:

| Feature | Effect |
|---|---|
| `builtin-color-schemes` | Embed upstream's named color schemes (`-p wezterm-gui --features builtin-color-schemes`) |
| `wezterm-ssh/dual-ssh-backends` | Build both `libssh-rs` and `ssh2` |
| `wezterm-ssh/vendored-openssl` | Force vendored OpenSSL if Homebrew OpenSSL is unavailable |

Package a local `.app` / zip with:

```console
TAG_NAME=kaguya-local ./ci/deploy.sh target
```

### Measure size / startup

Use the bundled benchmark skill script on macOS:

```console
python3 .agents/skills/kaguya-benchmark/scripts/benchmark_kaguya.py \
  --repo "$PWD" \
  --before-ref <upstream-or-old-ref> \
  --after-ref HEAD \
  --runs 8
```

## Configuration and Usage

Kaguya follows the same configuration model as WezTerm. Use the upstream docs for general setup:

* Installation: https://wezterm.org/installation
* Configuration: https://wezterm.org/config/files.html
* Upstream repository: https://github.com/wezterm/wezterm

For color schemes without the builtin table, put `.toml` scheme files under your `color_scheme_dirs` (or `~/.config/wezterm/colors`) and set `color_scheme = "name"`.

## Support

For general WezTerm usage, configuration, and upstream bugs, use the upstream WezTerm documentation and community channels.

For Kaguya-specific changes, use this repository.
