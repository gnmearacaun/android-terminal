# android-terminal
Set up the terminal on Android 16. Install the LXQt desktop with LabWC window compositor for greater choice of tools and terminals, running in a graphical environment


### Configuration steps for Android 16 Linux terminal.
 
Running LXQt with Wayland compositor LabWC on top of Debian on Android's Linux terminal. Wayland is the modern replacement for X11 (the default windowing system on Linux for decades). 

This repo will contain my dot files for terminals, ranger file manager and zsh shell. The first thing we may want to do is to change the shell to zsh for functioning on the commandline. Next tools we need are vim and neovim for editing files with a modicum of comfort and familiarity.
After installing the linux terminal we will update and install some initial packages such as git before cloning this repository. Commands have their own box to make it easier to copy and paste from the browser to the commandline

```
sudo apt-get update
```
```
sudo apt-get install git ranger vim aptitude alacritty kitty zsh zoxide
```
```
git clone https://github.com/gnmearacaun/android-terminal.git
```

Assuming you know where the files go, use ranger to place them into the ~/.config directory.
```
```
```
cd android-terminal
```
```
mv ~/.config/wayfire.ini wayfire.ini-original
```
```
mv -i wayfire.ini ~/.config 
```


```
git clone https://github.com/gnmearacaun/android-terminal.git
```
```
cd rpios-wayfirewm-config
```
```
mv ~/.config/wayfire.ini wayfire.ini-original
```
```
mv -i wayfire.ini ~/.config 
```
```
```

Install the LXQt and LabWC (which we will use to manage the windows)
```
sudo pacman -S lxqt lxqt-config lxqt-policykit lxqt-powermanagement lxqt-wayland-session labwc labwc-tweaks 
```
```
sudo apt-get install lxqt labwc
```
Set up a new passwd for the `droid` user
```
sudo passwd droid
```
You will be asked to type in the new password twice


I drew my configs, theme and other elements from various sources including https://github.com/stefonarch. His Vent-dark theme belongs in `~/.local/share/themes/` (& can also be specified via `labwc-tweaks`)

Log out and back in (`Ctrl-Alt-Backspace` to logout). Now you can move around the windows and 9 workspaces using `super`+`{a,s,f,w,b,h,j,k,l,Tab}` and create tiles out of windows and back to tiles with `Alt`+`{h,j,k,l}`

- If your desktop freezes at any point, log into another `tty` with `Ctrl+Alt+F3` and `reboot` 


#### Zsh and Zap

To set zsh as your default shell, execute the following.
```
sudo sh -c "echo $(which zsh) >> /etc/shells" && chsh -s $(which zsh)
```
Log out and back in. You're prompt will be basic. Install [zap](https://github.com/zap-zsh/zap) zsh plugin manager (replaces the need for `oh-my-zsh`)
```
zsh <(curl -s https://raw.githubusercontent.com/zap-zsh/zap/master/install.zsh) --branch release-v1
```
Reopen the shell, `zap` automajically installs the default plugins. Plugins can be found on the [Zap homepage](https://www.zapzsh.com/) 
Other plugins to be installed will be listed in the .zshrc file

#### [Nerdfonts](https://github.com/getnf/getnf)

Often required by modern shell programs like zsh and neovim.

```
curl -fsSL https://raw.githubusercontent.com/ronniedroid/getnf/master/install.sh | bash
```
Run `getnf` in the terminal and follow the prompts.

The fonts you select will be available system-wide.

#### Build Neovim 

Neovim is improving rapidly. To take advantage of recent developments in the plugins infrastructure we need a newer version of Neovim than Bookworm offers. Neovim plays nicely with the system clipboard for copy and pasting, commenting lines easily (`gcc`) and searching for files with `telescope` and so much more.  

- Note `CMAKE_BUILD_TYPE=RelWithDebInfo` would make a build with Debug info. `Release` runs a bit lighter.

- Use `git checkout nightly` if you need the very latest.

```
sudo apt-get install ninja-build gettext cmake unzip curl build-essential ripgrep fd-find fzf wl-clipboard 
```
```
git clone https://github.com/neovim/neovim.git
```
```
git checkout stable 
```
```
make CMAKE_BUILD_TYPE=Release
```
```
cd build && sudo cpack -G DEB && sudo dpkg -i nvim-linux64.deb
```
```
nvim -V1 -v
```
https://www.lazyvim.org/installation
If you don't have an `nvim` config of your own, [LazyVim](https://www.lazyvim.org/installation) is a great option.

Now that Neovim is available, make it the default in your `.zshrc`

```
export EDITOR="nvim"
```

#### Install LazyVim 

Install the LazyVim Starter

Make a backup of your current Neovim files:

# required
```
mv ~/.config/nvim{,.bak}
```

# optional but recommended
```
mv ~/.local/share/nvim{,.bak}
```
```
mv ~/.local/state/nvim{,.bak}
```
```
mv ~/.cache/nvim{,.bak}
```

Clone the starter

```
git clone https://github.com/LazyVim/starter ~/.config/nvim
```

Remove the .git folder, so you can add it to your own repo later

```
rm -rf ~/.config/nvim/.git
```

Start Neovim!
`nvim`

Refer to the comments in the files on how to customize LazyVim.

    TIP:

    It is recommended to run :LazyHealth after installation. This will load all plugins and check if everything is working correctly.

#### Install nodejs 

Utilizing [nodesource](https://github.com/nodesource/distributions), run as root:

```
sudo su
```
```
curl -fsSL https://deb.nodesource.com/setup_21.x | bash - &&\
```
```
apt-get install -y nodejs
```

### Optional Extras

To passthrough the Windows key to the terminal (it is usually used for navigating the workspaces and so on) 
(try using Alt instead of Windows key in the rc.xml instead if space2meta doesn't work )

#### _Caps2esc_ and _Space2meta_

- [caps2esc](https://gitlab.com/interception/linux/plugins/caps2esc): _transforming the most useless key ever into the most useful one_ `<Caps_lock>` is `esc` when tapped and `ctrl` when held down with another key. 

- [space2meta](https://gitlab.com/interception/linux/plugins/space2meta): _turn your space key into the meta (a.k.a. super) key when chorded to another key_. Window managers typically make liberal use of the `super` key to move around. 

`Caps2esc` is available in the repo, however `space2meta` needs to be built manually.

```
sudo apt-get update
```
```
sudo apt-get install interception-caps2esc interception-tools interception-tools-compat 
```
```
git clone https://gitlab.com/interception/linux/plugins/space2meta.git
```
```
cd space2meta
```
```
cmake -Bbuild
```
```
cmake --build build
```
```
sudo mv build/space2meta /usr/local/bin  
```
```
cd .. && rm -r space2meta
```
```
```
Put the udevmon config in place, and enable and start the service (you may have to logout/login to get the effect). 

```
cd android-terminal
```
```
sudo mkdir -p /etc/interception/udevmon.d
```
```
sudo mv udevmon.yaml /etc/interception/udevmon.d/
```
```
sudo systemctl enable --now udevmon.service
```
```
```

The following command increases the priority. 

```
sudo nice -n -20 udevmon -c udevmon.yaml >udevmon.log 2>udevmon.err &
```

#### Upgrading Neovim

Later when you want to upgrade, go back into the neovim directory (wherever it's stashed). Assuming you're on the branch you want, to rebuild from scratch and replace the current build:

```
git pull
sudo make distclean && make CMAKE_BUILD_TYPE=Release
cd build && sudo cpack -G DEB && sudo dpkg -i nvim-linux64.deb
nvim -V1 -v
```

#### Rebuilding Neovim

In case you have previously built the image and want to switch branches:
```
cd neovim && sudo cmake --build build/ --target uninstall
```

Alternatively, just delete the CMAKE_INSTALL_PREFIX artifacts:
```
sudo rm /usr/local/bin/nvim
```
```
sudo rm -r /usr/local/share/nvim/
```

#### A Minimal Vim Configuration 

I use a simple config (no plugins) authored by [jdhao](https://github.com/jdhao) while I'm  setting thing up and before neovim is built. It's also useful for occasional editing as `sudo`.

- The package `vim-gtk3` has better clipboard support than `vim` proper. Wayland users need `wl-clipboard` to copy and paste (both were installed with the previous `apt-get` command). 

```
sudo apt-get install vim-gtk3 wl-clipboard 
```
```
mv ~/.vimrc ~/.vimrc.bak
```
```
mkdir -p ~/.vim && cd ~/.vim
```
```
git clone https://github.com/jdhao/minimal_vim.git .
```
```
cd && sudo cp -r .vim /root
```

- Tip: add the following line to your `init.vim` to yank to `wl-clipboard`. So you would visually highlight the text with `v` or `shift+v` and the motion keys `h,j,k,l` and press `<leader>` (it's mapped to the `<spacebar>`) and then `y` to copy. Most terminals have `Ctrl+Shift+v` as the paste command. 
```
xnoremap <silent> <leader>y y:call system("wl-copy --trim-newline", @*)<cr>:call system("wl-copy -p --trim-newline", @*)<cr>
```

