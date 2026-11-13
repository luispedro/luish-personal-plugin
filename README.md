# Luis's Luish configuration (personal plugin)

My personal configuration for [luish](https://github.com/luispedro/luish), packaged as a luish plugin so that every
machine gets the same setup from one line of `config.toml`.

## What it sets

Most of it comes from my zsh configuration.

- **Plugins** (as dependencies in `plugin.toml`, loaded first): `std.bash-completion` (completion from
  bash-completion for commands without a completer of their own), `std.completion` (the full set of luish's
  standard completers, git's included), and from [luish-extra](https://github.com/luispedro/luish-extra) (made
  available in `[available]`, so that its other plugins, such as `luish-extra/conda`, can be enabled on any
  machine): `completion/all` (all its extra completers) and `themes` (its colour schemes).
- **Options** (`plugin.toml`): `autosuggest` (as zsh-autosuggestions), `expand.braces` (brace expansion),
  `highlight.paths` (underlines words that name existing files), `cd.auto` (`autocd`), `pushd.auto`
  (`auto_pushd`) with `pushd.ignore_dups` and `pushd.silent`, `prompt.percent`, `glob.star` (`**`) and
  `glob.bare_qualifiers` (`*(.)`), `vars.trace_after_startup` (records where each variable is set, from the first
  prompt on, for debugging), and for the history: `expand` (`!!`, `!$`, …), `share`, `ignore_space`,
  `reduce_blanks` and `save_no_dups`, with 10,000 entries (as zsh's `HISTSIZE` and `SAVEHIST`; luish keeps 1,000
  by default), in `~/.histfile`, in zsh's format.
- **Aliases** (`plugin.toml`): `ls --color=auto`, `open` for `xdg-open`, and the global aliases `...`, `....`, …
  up to seven dots, for `../..`, `../../..`, and so on (so `...` alone goes up two directories, with `cd.auto`).
  Up and Down already search the history for what was typed, as in zsh, so there are no key bindings.
- **`rc.lsh`**, for what `plugin.toml` can't hold:
  - `CDPATH`: the current directory, `~`, `~/Sync/work` and `~/work`;
  - `WORDCHARS` without `/`, so that Ctrl-W deletes one component of a path;
  - the prompt, `user@host : /path/ §` (`#` for root), with the directory in the colour scheme's `keyword` style
    and, if the last command failed,
    its exit status in red, as in `user@host : /path/ [1] §`;
  - `cd DIR/FILE`, which goes to the file's directory, and `cd DIR/ls`, which goes to `DIR` and lists it (a real
    directory of that name always wins);
  - `field N [FILE...]`, which prints the `N`th field of each line (`field NF`, the last);
  - `rename FILE`, which puts the file's name on the line to edit, then moves the file to the edited name.

Machine-specific setup (conda, nvm, cargo, pixi, `PATH`) is not here: it stays in each machine's
`~/.config/luish/rc.d/`, which runs after this plugin and can override what it sets.

## Installing

This needs luish 0.8 or newer (luish-extra's plugins require it), plus `bash` and bash-completion for
`std.bash-completion`.

Add the plugin to `~/.config/luish/config.toml`:

```toml
[plugins.enabled]
personal = { gh = "luispedro/luish-personal-plugin" }
```

then fetch it (and the `std` and luish-extra plugins it depends on) in an interactive luish:

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
├── plugin.toml   # dependencies, options and aliases
└── rc.lsh        # what plugin.toml can't hold (variables, functions); interactive shells only
```

Options go in `[options]` and key bindings in `[bindkey]` in `plugin.toml` (see luish's `docs/plugins.md`). Prefer
`plugin.toml` to shell; use `rc.lsh` only for what it can't express, and `init.lsh` for what scripts need too.
