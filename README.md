# Luis's Luish configuration (personal plugin)

My personal configuration for [luish](https://github.com/luispedro/luish), packaged as a luish plugin so that every
machine gets the same setup from one line of `config.toml`.

## What it sets

Most of it comes from my zsh configuration.

- **Plugins** (as dependencies in `plugin.toml`, loaded first): `std.bash-completion` (completion from
  bash-completion for commands without a completer of their own) and `std.git-completion`.
- **Options** (`plugin.toml`): `autosuggest` (as zsh-autosuggestions), `cd.auto` (`autocd`), `pushd.auto`
  (`auto_pushd`), `prompt.percent`, and for the history: `share`, `ignore_space`, `reduce_blanks` and `save_no_dups`,
  in `~/.histfile`, the file zsh uses (luish reads and writes zsh's format, so the two share it).
- **Aliases** (`plugin.toml`): `ls --color=auto`, `open` for `xdg-open`, and the global aliases `...`, `....`, …
  up to seven dots, for `../..`, `../../..`, and so on (so `...` alone goes up two directories, with `cd.auto`).
- **Key bindings** (`plugin.toml`): Alt-? for `insert-last-word`. Up and Down already search the history for what
  was typed, as in zsh.
- **`rc.lsh`**, for what `plugin.toml` can't hold:
  - `CDPATH`: the current directory, `~`, `~/Sync/work` and `~/work`;
  - `WORDCHARS` without `/`, so that Ctrl-W deletes one component of a path;
  - the prompt, `user@host:/path/ §`, with a bold `!` if the last command failed;
  - `cd FILE`, which goes to the file's directory, and `cd DIR/ls`, which goes to `DIR` and lists it;
  - `field N`, which prints the `N`th field of each line.

Machine-specific setup (conda, nvm, cargo, pixi, `PATH`) is not here: it stays in each machine's
`~/.config/luish/rc.d/`, which runs after this plugin and can override what it sets.

## Installing

This needs a luish with `[alias]` support in `plugin.toml` (commit 90b3619 or later), plus `bash` and
bash-completion for `std.bash-completion`.

Add the plugin to `~/.config/luish/config.toml`:

```toml
[plugins.enabled]
personal = { gh = "luispedro/luish-personal-plugin" }
```

then fetch it (and the `std` plugins it depends on) in an interactive luish:

```sh
plugin sync
```

New shells load it. `plugin sync` writes `~/.config/luish/plugins.lock` with the commits it used; to move to the
newest commit of this repository later, run `plugin update personal`.

To use a local checkout instead (for example, while editing it), use a `path` source:

```toml
[plugins.enabled]
personal = { path = "~/luish-personal-plugin" }
```

A `path` source is used where it is, so changes take effect in the next shell, without `plugin sync`.

## Layout

```text
luish-personal-plugin/
├── plugin.toml   # dependencies, and options, aliases and key bindings
└── rc.lsh        # what plugin.toml can't hold (variables); interactive shells only
```

Options go in `[options]` and key bindings in `[bindkey]` in `plugin.toml` (see luish's `docs/plugins.md`). Prefer
`plugin.toml` to shell; use `rc.lsh` only for what it can't express, and `init.lsh` for what scripts need too.
