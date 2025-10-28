---
title: "Development Setup using Nix"
date: "2025-10-28 21:55"
template: post
draft: false
slug: "/posts/development-setup-using-nix"
category: Programming
tags:
  - coding
  - programming
  - nix
description: |
  This setup is managed entirely Ni and my https://github.com/faizhasim/dotfiles. It’s declarative, reproducible, and version-controlled — so a new Mac can be up and running in minutes, not hours. It also allow separation between my work and personal machines.
socialImage: "./pibb-full-dark-theme-transparent.svg"
---

## 🐦‍🔥 Preface

This setup is managed entirely through [Nix](https://nixos.org) " " and my [faizhasim/dotfiles](https://github.com/faizhasim/dotfiles). It’s declarative, reproducible, and version-controlled — so a new Mac can be up and running in minutes, not hours. It also allow separation between my work and personal machines.

## 💬 Why Nix?

I switched to Nix after my previous dotfiles setup ([faizhasim/starter](https://github.com/faizhasim/starter)) started to feel brittle.  
Over time, macOS updates and patchy shell scripts made reproducibility harder to maintain. I wanted something declarative and self-contained — not layers of “run-this-first” instructions.

Nix isn’t perfect, but it gives me:

- **Reproducibility** – Every package, config, and environment is declared, not installed ad-hoc.

- **Isolation** – No more “works on my machine” issues; everything lives in the Nix store.

- **Rollback** – If something breaks, I can revert or (more often) fix forward — just like Git.

- **Cross-machine consistency** – My work and home Macs stay in sync. When I tweak something like window management on one, it’s instantly reproducible on the other.


### Why _not_ Nix?

- **Immutability** is a double-edged sword. Some tools (like `gh` CLI) expect to mutate configs. I work around that using **1Password** to store tokens and secrets.

- **Learning curve** – Nix concepts take a bit to internalize, but the payoff in long-term maintainability is worth it.

## 🧠  Philosophy

1. **Reproducible environments** — If I nuke my laptop today, I can be coding again in an hour.
2. **Declarative over imperative** — Instead of `brew install`, I describe my desired state in Nix.
3. **Composable configs** — Each tool (zsh, wezterm, neovim) is its own module, easy to tweak or swap.
4. **Minimal friction** — I want the environment to disappear, not demand attention.

## 💻 Machine: At a glance...

- Shell: zsh 5.9
- Terminal: WezTerm
- Terminal Font: JetBrains Mono
- Packages: 524 (nix-system), 44 (nix-default), 5 (brew), 18 ()

### Packages

- Mostly managed through [Nix packages](https://github.com/faizhasim/dotfiles/blob/main/home-manager/packages/)
- A few remaining via [Homebrew](https://github.com/faizhasim/dotfiles/tree/main/darwin/homebrew)
- Inspired by many other public dotfiles projects out there

---

## 🧩 Highlights & Tools

### 🐚 Shell & Prompt

- [zsh](https://github.com/faizhasim/dotfiles/blob/main/home-manager/zsh.nix) configured via Home Manager
- [WezTerm config](https://github.com/faizhasim/dotfiles/blob/main/home-manager/wezterm/wezterm.lua) — minimal and themed with **Nord**
- Secrets stored securely in **1Password**, not in the filesystem
- **Toolchains** auto-configured with completions and environment isolation

### 🪟 Window Management

- Switched from Magnet to [**AeroSpace**](https://github.com/nikitabobko/AeroSpace) — more flexible tiling and automation
- Using **jackyborders** (via Homebrew) to make active windows visually stand out
- **Nord theme** consistently applied using:
    - [`stylix.nix`](https://github.com/faizhasim/dotfiles/blob/main/darwin/stylix.nix)
    - [`dircolors`](https://github.com/faizhasim/dotfiles/blob/main/home-manager/dircolors.nix)
    - [Neovim theme config](https://github.com/faizhasim/dotfiles/blob/main/home-manager/nvchad.nix)

### 🧑‍💻 Dev Environment

- **Mise-en-place** for toolchains ([config](https://github.com/faizhasim/dotfiles/blob/main/home-manager/mise.nix)): smooth replacement for `asdf` or `nvm`
- **direnv** integrated with Mise
- **Git & GitHub CLI** – [git.nix](https://github.com/faizhasim/dotfiles/blob/main/home-manager/git.nix) and [gh.nix](https://github.com/faizhasim/dotfiles/blob/main/home-manager/gh.nix)
    - 1Password handles tokens, avoiding config mutations
- **Neovim (NvChad)** – lightweight custom config with [a few tweaks](https://github.com/faizhasim/dotfiles/blob/main/home-manager/nvchad.nix)
- **IntelliJ IDEA** remains my main IDE (synced via JetBrains account, with [`.ideavimrc`](https://github.com/faizhasim/dotfiles/blob/main/home-manager/idea.nix))

### 🛠 Shell Utilities

From [`shell.nix`](https://github.com/faizhasim/dotfiles/blob/main/home-manager/shell.nix):

|Tool|Purpose|
|---|---|
|`carapace`|shell completion|
|`bat`|prettier `cat`|
|`zoxide`|smarter `cd`|
|`yazi`|terminal file manager|
|`atuin`|searchable shell history|
|`lsd`|better `ls`|
|`fzf`|fuzzy finder|
|`awsp`|AWS profile switcher (fzf-based)|

### 🧰 OS-Level Config

Using [**nix-darwin**](https://nix-darwin.github.io/nix-darwin/manual/), all macOS system configs live under [`darwin/os`](https://github.com/faizhasim/dotfiles/tree/main/darwin/os), and these includes things like:

- Font smoothing for readability
- Extra fonts ([fonts.nix](https://github.com/faizhasim/dotfiles/blob/main/darwin/os/fonts.nix))
- Activation scripts for display tweaks and general system tuning

> Many of these were ported from [my previous setup](https://github.com/faizhasim/starter/tree/master/system), and I’ll likely refactor the flake inputs to rely more on implicits soon.

## 🙋 Q&A

Collections of random questions and decisions.

#### Why `zsh`? Not `fish` or others?
`zsh` is still relative the same with standard shell. So, my mind can be at ease when writing for `sh` even. Some of ergonomics from `fish` are available by a bit of customisations. I did install `nushell`, which looks pretty cool, but did not configure it as default shell.

#### Why `wezterm` as terminal emulator?
- I heavily used iterm2 in the past and it's not bad to be honest. But, not easy to codify the config. There are faster, nicer terminals nowadays too.
- Tried alacrity - it's just too bare. Probably great if you're using tmux extensively.
- I want something that mostly works, while being fast enough like kitty/ghostly/alacrity.
- LUA support is nice. Works well with nix setup.
- Work restrict SSH and I practically have no use for tmux to share my session. Split screen from terminal is good enough. For pairing, I used Intellij Code With Me and seems to work fine with people.

#### What about theme / colour palette?
[Nord theme](https://www.nordtheme.com). That's it - done. I have `stylix` doing this, setting `dircolors` with that too. For manual
Catpuccin or Rose Pine looks nice too.

#### Any manual setup left?
- I use Jetbrains account to sync config. This include theming.
- Raycast - unfortunately I'm not a paying customer 😜

#### Why Raycast?
- It's just a beautiful product, developer friendly and their keyboard shortcut customisations is pretty nice. The launcher "understands" symlink.
- I am a paid customer for Alfred and been using it for near a decade. Still a close second.
- MacOS 26 spotlight is actually pretty nice. Some customisations possible via Shortcuts and it actually satisfy my launcher need.
- However, Raycast (and Alfred) bundled with text expansion, clipboard management - and they looks pretty good.
- I did use and codify my setup on text expansion and clipboard management with other tools like hammerspoon and espanso. However, they aren't pretty, like Raycast (or Alfred).


#### Nix setup: Why not let Nix manage homebrew installation itself too (via [zhaofengli/nix-homebrew](https://github.com/zhaofengli/nix-homebrew))?
I need my setup to be a bit more dynamic. `nix-homebrew` required the taps as input and immutability doesn't work really nicely in this case.
When homebrew itself being immutable, it's just annoying to do a quick `brew install xxx` to test some tools because homebrew is build in the nix store. Plus, "zap" strategy works well enough to keep my homebrew packages clean.
Work private homebrew tap is via private ssh, hence, not an easy setup to do either.
With majority of packages managed by nix itself, it's not worth to do ni. Hence, I only manage the formulas with nix-darwin.

---

## 💭 Final Notes

This setup evolves over time — but Nix gives me a **safe, reproducible foundation** for experimentation.  
Whether I’m tinkering with shell utilities or trying new window managers, it’s always easy to roll forward (or back) with confidence.

> The full configuration is available at [faizhasim/dotfiles](https://github.com/faizhasim/dotfiles).  
> Fork, adapt, or just browse through — you might find a few ideas worth stealing.

