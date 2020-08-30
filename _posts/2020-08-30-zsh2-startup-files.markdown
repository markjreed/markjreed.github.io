---
layout: post
title:  "Bash-ing my head against Zsh, Part 2:Startup Files"
date: 2020-08-30 13:16:25 -0400
categories: jekyll update
---
## Zdots and Profiles and Env files, Oh my!

When adding customizations to your shell environment, one of the questions
that comes up is where to put them. I may dive into the history of shell
startup files later for anyone interested, but the first thing to know is
what the options are. Modern shells like zsh have different files for different
types of shell processes, namely:

**Interactive shells:** shells that run, well, interactively, reading commands
from a user at a terminal and responding in real time. You can tell if the
shell _thinks_ it's interactive by looking to see if the flags parameter `$-`
contains the latter `i`. The opposite of an interactive shell is a
non-interactive shell, sometimes called a "batch" shell, such as one invoked
just to run a script and then exit.

**Login shells:** these are a special subset of interactive shells; in ksh and
zsh, but not bash, you can tell if the shell thinks it's a login shell by
checking `$-` for the letter `l`. When a shell is launched at actual login
time, which these days usually means because the user has come in remotely via
SSH, it's automatically marked as a login shell; you can configure
terminal-emulator applications like Terminal.app and iTerm to launch all shells
as login, too. Most shells accept either `-l` or `--login` to make a new shell
a login shell, but the original method used by login daemons like `sshd` is to set
`argv[0]` to a value starting with `-` when `exec`'ing the shell.

So whether a shell is login or not, or interactive or not, will determine
which startup file(s) get executed.

## Ten! Ten beautiful RC files! Ha ha ha ha ha!

In the world of ZSH, there are five categories of file that get run
automatically, each with both a global and a personal location, for a
total of up to ten files to think about. While that's a few more than any
previous shells, a more significant difference is that zsh lets you configure
a location other than your home directory in which to look for the personal
version of all these files. If `$ZDOTDIR` is set in the environment, then zsh
looks for the files there instead of in the user's home directory.

Here are the files and when they get executed:

    /etc/zshenv

This file is always run, regardless of whether the shell is login or
not, interactive or not. There's no way to stop zsh from executing
this file. So it's a bad idea to put anything in here that writes
to standard output; that will confuse anything that runs a remote
non-interactive process, like `scp`. Only put things here that need
be set for all shells, everywhere.

While you can't stop zsh from sourcing `/etc/zshenv`, the code in
that file can stop zsh from sourcing any other files. Zsh sets
two environment variables on startup: `GLOBAL_RCS` and `RCS`; their
presence indicates whether zsh should continue executing the other
global and personal startup files, respectively. So `/etc/zshenv`
can stop either category from running by simply unsetting the
corresponding environment variable.

    ${ZDOTDIR-$HOME}/.zshenv

Unless `RCS` is unset after running `/etc/zshenv`, this file is likewise run
for all shells, regardless of type. So again, don't put anything that
writes output here, or assumes that it's running interactively
attached to a terminal or anything.

This is a convenient place to set things you want to make absolutely sure are
always set. Some folks put their like their PATH settings in here, to ensure
that e.g.  remotely-executed scripts can find and run other scripts that live
in custom locations. In my case, I haven't yet found anything that needs to
live here.

    /etc/zprofile
    ${ZDOTDIR-$HOME}/.zprofile

These run in login shells only. As a general rule, I set environment
variables here, and rely on all my non-login shells ultimately
being children of login shells and so inheriting those environment
variables.

    /etc/zshrc
    ${ZDOTDIR-$HOME}/.zshrc

These run for all interactive shells. Unlike the `bashrc` files, the
`zshrc` ones are automatically run along with the profile ones even in
login shells. So anything that's not inheritable that you want in all
interactive shells should be set here: custom command completion,
aliases, functions, prompt string settings, etc.
here.

    /etc/zlogin
    ${ZDOTDIR-$HOME}/.zlogin

Like the `zprofile` files, the `zlogin` files run in all login shells.
The distinction is that while the `zprofile` files are run before
the `zshrc` ones, the `zlogin` ones are run afterward. It's unlikely
for any given shell setup to need both; these are intended to be more
familiar to those coming from the csh family of shells, which run
`.[t]cshrc` before `.login`.

    /etc/zlogout
    ${ZDOTDIR-$HOME}/.zlogout

Anything in these is run as the last thing a login shell does before
exiting.

## My Setup

The actual filesystem layout I use is based on how I had things set up
all the way back in ksh days. Under `lib` in my home directory I have
a `zsh` subdirectory, which has a subdirectory for each of the above
files. The file `main` in each subdirectory is symbolically linked to the
actual dotfile, e.g.

    lrwxr-xr-x  1 mjreed  staff     21 Aug 21 21:39 .zprofile -> lib/zsh/zprofile/main
    lrwxr-xr-x  1 mjreed  staff     19 Aug 30 00:49 .zshenv -> lib/zsh/zshenv/main
    lrwxr-xr-x  1 mjreed  staff     18 Aug 21 21:39 .zshrc -> lib/zsh/zshrc/main

My `lib` directory is itself a symbolic link into a clone of a private GitHub
repository where I maintain my shell environment. I don't set `ZDOTDIR`, but
just put the symlinks directly under `$HOME`.

So far my `.zprofile` is pretty minimal, but my `.zshrc` has already
grown to over 100 lines, not counting the definitions of the various
custom functions I have. I'll talk about what those lines do as they come
up topically in future posts.
