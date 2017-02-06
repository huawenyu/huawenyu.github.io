## dotfiles

linux programmer's config files: zsh, tmux, vim, script, ...

## Install

### If first time
You can git download dotfiles to any where
```Shell
$ cd ~
$ apt-get install git
$ git clone https://github.com/huawenyu/dotfiles.git
$ cd dotfiles/script
$ ./update_dot.sh -a pull
```

### Apply dotfiles
- All your old dotfiles will backup to `$HOME/dotfiles_bak` if it's not softlink
- The script will create softlink of all dotfile into your `$HOME`, includes: `config for vim, tmux, zshrc, xterm, git, ag, ...`
  * script, include the update_dot.sh, update_all.sh, init_ubuntu.sh, ...
  * the config files

```Shell
$ cd ~/dotfiles/script
$ ./update_dot.sh -a pull
```

#### [optional] ubuntu setup develop env
If want setup develop env for ubuntu type OS: ubuntu, linux mint, ...
- install gcc build env, cscope, ctags, ...
- install tools: git, svn, vim-gnome, tftp, samba, ...
- config tftp-service, samba service

```Shell
$ cd ~/dotfiles/script
$ ./init_ubuntu.sh
```

## Usage

There have two script under dotfiles/script:
- update_dot.sh: use to sync the dotfile under dotfiles
- update_all.sh: use to sync all git dirs which list at `array git_repos`

For your convenience, please add the `<your-download-dir>/dotfiles/script` to `$PATH` by
`export PATH=$HOME/dotfiles/script:$PATH` to our `.bashrc` or `.zshrc`

For example, use update_dot.sh:
```Shell
$ update_dot.sh
Usage: update_dot.sh [-hvdn] [-a <action>] [-m <message>]

Options:
  -h  help
  -v  verbose
  -d  debug
  -n  dry-run
  -*a [push|pull]
  -*m "commit message"

Samples:
  update_dot.sh -na pull
  update_dot.sh -na push -m "auto update"

$ update_dot.sh -a pull

<do some modify>

$ update_dot.sh -a push -m "do some modify"
```

Rollback  
```Shell
  git reset --hard <old-commit-id>  
  git push -f origin branch  
```
## Tools
### vim server
```Shell
<terminal-1> $ vim --servername foo                           <<< start vim-server named 'foo'
<terminal-2> $ vim --servername foo --remote-silent bar.hs    <<< open current dir's file 'bar.hs' but display on the vim-server
<terminal-3> $ vim --servername foo --remote-tab file2.txt file3.txt   <<< open these files into new tabs on the vim-server
<terminal-4> $ vim --servername foo --remote-send '<Esc>:mksession ~/sessionFile.vim<CR>:wqa<CR>'    <<< save session and used by another vim-server
<terminal-5> $ vim --servername bar -S ~/sessionFile.vim
```
### gdb-dashboard
https://github.com/cyrus-and/gdb-dashboard

using gdb's python interface, if use ubuntu 14.04, the gdb using python3 interface, so if some python module need, please `sudo pip3 install <module>`

  - start GDB in one terminal: enter python mode by `(gdb) source ~/.gdb-dashboard`, or if want do it automatic, please `ln -s ~/.gdb-dashboard ~/.gdbinit`
    * config show model: `dashboard -layout !registers source !assembly !stack !threads`
    * debug as usual, the only different is python prompt `>>> `, not `(gdb) `, but we can change by `dashboard -style prompt '(gdb)'`
  - Use vim as source viewer:
    * In another terminal (e.g. tmux pane), start vim (+clientserver): `vim --servername "GDB.SOURCE"`
  - Output to another terminal by tty:
    * In another terminal (e.g. tmux pane) and get its TTY with the tty command (e.g. /dev/ttys001, the name may be different for a variety of reasons);
    * Issue the command gdb-python mode '>>> dashboard -output /dev/ttys001` to redirect the dashboard output to the newly created terminal;
  - Or output to another terminal by tail:
    * In another terminal (e.g. tmux pane): `tail -f /tmp/gdb.output`, if you hate the tail's `file truncated` message:

```Shell
function _mytail()
{
  if [ -t 0 ]; then
    tail "$@" 2> >(grep -v truncated >&2)
  else
    tail "$@" 2> >(grep -v truncated >&2)
  fi
};
alias tail='_mytail'
```
## script

### sample: script template

Can output like this in vim-plugin `VOom`:
```Code
= |###./sample.sh:0: main
  . |###./sample.sh:168: Main
  . . |###./sample.sh:163: Demo
  . . . |###./sample.sh:125: LogicAndOr
  . . . |###./sample.sh:125: LogicAndOr
  . . |###./sample.sh:163: Demo
  . |###./sample.sh:168: Main
  |###./sample.sh:0: main
```

### bashdb: shell script debugger

To use this script you simply prepend the call to the script you want to test with `bashdb`.
```Shell
$ ./bashdb header.sh . .
 $1 - The name of the original script we are debugging
 $2 - The directory where temporary files are stored
 $3 - The directory where bashdb.pre and bashdb.fns are stored
```
bashdb will read off the first parameter as the script you want to debug and forward any other parameters to that script when it calls it. The script will then return a prompt for you to enter commands and step through debugging.

You can enter h or ? at the prompt for a full listing of available commands.

This is an extension of an example from the book "Learning The Bash Shell" by O'Reilly

### ShellCheck: a static analysis tool for shell scripts

- [code](https://github.com/koalaman/shellcheck)
- [doc](http://www.shellcheck.net)

On Debian based distros:
`apt-get install shellcheck`

On Fedora based distros:
`dnf install ShellCheck`

### bsfl: shell script library

## plugin

### eclipse dark theme

[eclipse-moonrise-theme](https://github.com/guari/eclipse-ui-theme)
  - Modify the active view should click two times: 1st focus, 2nd click item.  
  - If sometimes the plugin invalid (Examp: update a plugin just make the `moonrise` theme disappear), we can install directly from:
    * [update-site](https://raw.github.com/guari/eclipse-ui-theme/master/com.github.eclipseuitheme.themes.updatesite)
  - If install succ, please replaced it by our modified version.
  - Please choose the moonrise, not moonrise(standalone)
```Shell
$ cd eclipse-moonrise-theme
$ jar cf com.github.eclipseuitheme.themes.moonrise-ui_0.8.9.201404052318.jar *
$ mv com.github.eclipseuitheme.themes.moonrise-ui_0.8.9.201404052318.jar  <your-install-dir>/eclipse/plugins/.
```
