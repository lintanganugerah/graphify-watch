# graphify-watch

Detached watcher wrapper for [`graphify`](https://github.com/Graphify-Labs/graphify).

## Why

`graphify watch .` is foreground. This wrapper makes it practical for daily work:

- runs initial code-only extraction first with `graphify update .`
- starts `graphify watch .` detached
- stores watcher registry in `~/.graphify-watch/registry.tsv`
- writes per-project logs to `~/.graphify-watch/<safe-root>.log`
- supports `status`, `logs`, `stop`, and `restart` from any directory
- confirms destructive/long-running actions
- stops by exact PID with `/proc/<pid>/cmdline` guard, no `pkill`

## Requirements

```bash
uv tool install graphifyy --with watchdog
```

Or equivalent install that provides both:

```bash
graphify
graphify watch
```

## Install

```bash
install -Dm755 graphify-watch ~/.local/bin/graphify-watch
```

Optional shell alias:

```bash
alias graphify-watch="$HOME/.local/bin/graphify-watch"
```

## Usage

Start watcher for current directory:

```bash
graphify-watch start
```

Start watcher for any repo:

```bash
graphify-watch start /path/to/repo
```

List running watchers:

```bash
graphify-watch status
```

Tail logs:

```bash
graphify-watch logs
```

Stop selected watcher:

```bash
graphify-watch stop
```

Restart selected path:

```bash
graphify-watch restart /path/to/repo
```

Help:

```bash
graphify-watch help
```

## Behavior

`start` does:

```text
confirm target path
run graphify update .
run GRAPHIFY_WATCH_POLLING=1 nohup graphify watch .
record PID/root/log in registry
```

`graphify update .` is AST/code-only and does not need LLM API keys.

Docs, PDFs, images, and other semantic files still need Graphify's normal LLM-backed update flow.

## Linux watcher loops

The wrapper sets:

```bash
GRAPHIFY_WATCH_POLLING=1
```

This is meant for Graphify versions/patches that support polling mode to avoid Linux inotify self-trigger loops. If your Graphify ignores this env var, it is harmless, but the upstream watcher may still retrigger on its own file reads.

## License

MIT
