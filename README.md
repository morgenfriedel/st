# st

This is the repository for my build of `st` with the following patches applied:

- [alpha](https://st.suckless.org/patches/alpha/)
- [anysize](https://st.suckless.org/patches/anysize/)
- [boxdraw](https://st.suckless.org/patches/boxdraw/)
- [clipboard](https://st.suckless.org/patches/clipboard/)
- [scrollback](https://st.suckless.org/patches/scrollback/)
- [xresources](https://st.suckless.org/patches/xresources/)

## Installation

All of the patches have already been applied, so you can simply clone the repository and run `sudo make clean install`. If you need to build from scratch, clone the `st` repo from suckless and then install build dependencies:

```sh
sudo apt install build-essential libx11-dev libxft-dev libxrender-dev
```

Apply the patches:

```sh
patch -p1 < patches/st-scrollback-0.9.2.diff
patch -p1 < patches/st-clipboard-0.8.3.diff
patch -p1 < patches/st-alpha-20240814-a0274bc.diff
patch -p1 < patches/st-anysize-0.8.4.diff
patch -p1 < patches/st-xresources-20200604-9ba7ecf.diff
patch -p1 < patches/st-boxdraw_v2-0.8.5.diff
```

Make and install:

```sh
sudo make clean install
```

If you're iterating and testing changes to the `config.def.h` file:

```sh
rm -rf config.h && sudo make clean install && ./st
```
