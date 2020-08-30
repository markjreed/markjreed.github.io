---
layout: post
title:  "Bash-ing my head against Zsh, Part 1:Introduction"
date:   2020-08-29 16:02:01 -0400
categories: jekyll update
---
As promised in my [last post]({% post_url 2020-08-29-welcome %}), this is
the documentation of my adoption of the Z shell (zsh), which is the default
user shell as of MacOS Catalina. Hitherto I've used the Bourne-Again Shell
(bash).

I live and work at the command line. Virtually all my professional time – what
isn't spent in the web browser – is spent typing at the shell prompt. So I've
invested a significant chunk of that time over the years in getting my
environment just so. And while I'm always tweaking and adding to those
customizations, I haven't really revisited most of them since the last time I
changed login shells, from the Korn Shell (ksh) – which was over 20 years ago
now.

So while I had resisted the change for a while, I have finally taken the plunge.
I am, of course, hardly the first to do so or to document it, as a web search
will reveal; there's even [a
book](https://www.amazon.com/Bash-Shell-Conquering-Command-Line/dp/1590593766)
on the topic. As I find useful posts from previous adventurers
online, I'll link to them here. But the particulars of my setup will
necessarily be different, and I hope that seeing what I'm doing and why will
help you make your shell environment easier to get things done in. 

Some housekeeping first: while Catalina comes with a `/bin/zsh`, it is slightly
out of date: version 5.7.1 as of MacOS 10.15.6.
Plus, I use a couple Macs that I have kept back on Mojave for various reasons,
which has an even older version; I want my shell environment to be consistent
across my usual machines. So I have used [Homebrew](https://brew.sh) to install
the latest version in /usr/local/bin, which as of this writing is 5.8. I'll try
to point out when I'm doing something that won't work in older versions. 

To ensure that I'm always running the correct zsh, I have this in my ~/.zshrc
file (see below):

    if [[ $(zsh -c 'echo $ZSH_VERSION') > $ZSH_VERSION ]]; then
      exec zsh ${-+-$-} "$@"
    fi

The condition should prevent an infinite loop. :) 

But I mentioned my `.zshrc`, which brings us to our next topic: startup files.
Where do I put this stuff? That's the subject of the next post.

