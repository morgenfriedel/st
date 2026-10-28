# st

This is the repository for my build of `st` with the following patches applied:

- [alpha](https://st.suckless.org/patches/alpha/)
- [anysize](https://st.suckless.org/patches/anysize/)
- [boxdraw](https://st.suckless.org/patches/boxdraw/)
- [clipboard](https://st.suckless.org/patches/clipboard/)
- [ligatures](https://st.suckless.org/patches/ligatures/) (boxdraw variant, merged by hand for anysize and scrollback)
- [scrollback](https://st.suckless.org/patches/scrollback/)
- [xresources](https://st.suckless.org/patches/xresources/)

Plus local changes (`patches/st-local-modern-0.9.2.diff`):

- **Clickable URLs.** Ctrl+click opens the URL under the pointer in the default browser via `xdg-open`. [OSC 8 hyperlinks](https://gist.github.com/egmontkob/eb114294efbcd5adb1944c9f3cb5feda) are used when the program prints them; otherwise URLs are detected in the text, including across soft-wrapped lines. Only `https`, `http`, `ftp`, `file` and `mailto` URLs are opened (`urlschemes` in `config.def.h`). Ctrl+click works even inside apps that capture the mouse, such as Claude Code, vim and tmux. Set `st.urlopener` in `~/.Xresources` to use a program other than `xdg-open`.
- **Mouse-wheel scrollback.** In the shell, the wheel scrolls history 3 lines at a time; Shift+wheel scrolls a page. In full-screen apps (alternate screen), wheel events stiCll go to the app.
- **Synchronized output** (DEC mode 2026, with a DECRQM reply). TUIs such as neovim and tmux can ask the terminal not to draw until a frame is finished, which removes tearing and flicker during redraws. A frame is held for at most `su_timeout` (200 ms).
- **Curly and coloured underlines.** SGR `4:0`–`4:5` (none, single, double, curly, dotted, dashed), `21` (double) and `58`/`59` (underline colour), which neovim uses for diagnostics. The terminfo entry advertises them through `Smulx` and `Setulc`, so neovim picks them up once `make install` has run `tic`. Colon sub-parameters are now parsed properly; previously `4:3` came out as underline plus italic.
- **New terminal here.** Ctrl+Shift+Enter opens another `st` in the working directory of whatever is in the foreground (the shell, or a program it started, such as neovim or Claude Code).
- **Ligatures** (HarfBuzz), with JetBrains Mono as the font (`sudo apt install fonts-jetbrains-mono`). Without it, fontconfig substitutes another monospace font, which may have no ligatures. JetBrains Mono is used rather than Fira Code because Fira Code has no italic, and `st` then draws italic text in the `defaultattr` colour instead of slanting it. A font set with `st.font` in `~/.Xresources` overrides this.
- **Scrollback** of 10,000 lines (`scrollback` in `config.def.h`). History is allocated when a window opens, at about 2.4 KB per line per 100 columns, so 10,000 lines cost about 30 MB in a 120-column window.

## Installation

All of the patches have already been applied, so you can simply clone the repository and run `sudo make clean install`. If you need to build from scratch, clone the `st` repo from suckless and then install build dependencies:

```sh
sudo apt install build-essential libx11-dev libxft-dev libxrender-dev libharfbuzz-dev fonts-jetbrains-mono
```

`libharfbuzz-dev` is needed for ligatures, including when you build straight from this repository.

Apply the patches:

```sh
patch -p1 < patches/st-scrollback-0.9.2.diff
patch -p1 < patches/st-clipboard-0.8.3.diff
patch -p1 < patches/st-alpha-20240814-a0274bc.diff
patch -p1 < patches/st-anysize-0.8.4.diff
patch -p1 < patches/st-xresources-20200604-9ba7ecf.diff
patch -p1 < patches/st-boxdraw_v2-0.8.5.diff
patch -p1 < patches/st-ligatures-boxdraw-20241226-0.9.2.diff
patch -p1 < patches/st-local-modern-0.9.2.diff
```

Two hunks of the ligatures patch fail on this patch set and need merging by hand. In `xmakeglyphfontspecs` (`x.c`), keep anysize's `win.hborderpx`/`win.vborderpx` instead of `borderpx`. In `draw()` (`st.c`), add the two new `xdrawcursor` arguments inside the scrollback patch's `if (term.scr == 0)`. `st-local-modern` is a diff against that result, which also includes this repository's `config.def.h` tweaks, so its `config.def.h` hunks may need applying by hand on a fresh suckless checkout.

Make and install:

```sh
sudo make clean install
```

If you're iterating and testing changes to the `config.def.h` file:

```sh
rm -rf config.h && sudo make clean install && ./st
```
