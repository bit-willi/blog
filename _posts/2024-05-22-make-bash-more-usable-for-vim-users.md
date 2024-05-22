---
title: Make bash more usable for VIM users
published: true
---


# Make bash more usable for VIM users

For Vim users, transitioning between the editor and the terminal can sometimes feel jarring due to the differences in key bindings and functionalities. However, with a few tweaks, you can make Bash more Vim-friendly. This article will guide you through configuring your Bash environment to feel more like Vim.


## Enable Vi Mode in Bash

The first step is to enable Vi mode in your Bash shell. This will allow you to use Vi-style key bindings for command-line editing.

```bash
set -o vi;export EDITOR='vim';
```

Add these lines to your '~/.bashrc' file to make the changes permanent. After saving the file, run `source ~/.bashrc` or restart your terminal to apply the changes.


## Navigating Bash with Vim Key Bindings

With Vi mode enabled in Bash, you can navigate and edit your command line using familiar Vim key bindings:

Normal Mode: Press `Esc` to enter normal mode.

Insert Mode: Press `i` to enter insert mode.

Visual Mode: Press `v` to enter visual mode (It will open a vim in a temporary buffer. when its saved, the script runs).


### Command Navigation:

`h, j, k, l`: Move left, down, up, and right, respectively.

`w, b`: Move forward and backward by word.

`0, $`: Move to the beginning and end of the line.


### Editing Commands:

`x`: Delete a character.

`dw, db`: Delete a word forward and backward.

`dd`: Delete the entire line.
