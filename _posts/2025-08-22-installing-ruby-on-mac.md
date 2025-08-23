---
layout: post
section-type: post
has-comments: false
title: Installing Ruby on Mac ... Again.
category: tech
tags: [ "ruby", "mac" ]
---

Ever since i started coding and dabbling with ruby, i've been installing Ruby on my mac as most devs do, through Brew, and everything was smooth sailing... until it wasn't. somehow i managed to break my path so bad that nothing was running properly, gems were installing but i was not able to use them, a lot of loading errors, not even bundle was safe, so after an immense amount of time trying to set everything straight and back to working order, i finally gave up and went nuclear, uninstall everything and start from scratch.

I went looking through dev posts and several articles highlighting options on how to do it properly, and this is the result, documented mostly for my own benefit so i can keep track.

The first thing i read on every post or article where this happens is get rid of every ruby environment manager you may have installed before rbenv, rvm, asdf, mostly because those will create another set of headaches if you don't, after that you need to have brew installed and ready to go, you can test this when you run:
```bash
    $: brew doctor
```
and get this output:
```bash
    Your system is ready to brew.
```

Once you are ready we can just take off, the best solution i found and what is widely recommended as the easiest and best approach in the long run is by using `chruby` and `ruby-install`, the gist of this is `chruby` allows you to maintain the system environment variables, while `ruby-install` compiles a version of ruby for your system from scratch, this takes some time, but you can go get a cup of coffee while it runs, it's not really that long, newer machines are fasters so its worth it.

lets start installing `chruby` and `ruby-install` by running:
```bash
    brew install chruby ruby-install
```

Once is done, remember to source `chruby` on your shell's RC, I'm using `zsh` so add this into the `./zshrc`:
```bash
    source "/opt/homebrew/opt/chruby/share/chruby/chruby.sh"
    source "/opt/homebrew/opt/chruby/share/chruby/auto.sh"
```

At this point you need to reset your shell profile, or simply close and reopen your terminal, now install the latest ruby through `ruby-install`:
```bash
    $: ruby-install ruby
```
Once Ruby is successfully installed you should see something like this:
```bash
    >>> Successfully installed ruby 3.4.5 into
```
for the final step switch to the newly installed ruby version with `chruby`:
```bash
    $: chruby 3.4.5
    $: ruby --version
    ruby 3.4.5 (2025-07-16 revision 20cda200d3) +PRISM [x86_64-darwin24]
```

As a bonus addition for convenience you should add this into your Shell RC's, to automatically `chruby` into the latest version:
```bash
    command -v chruby 2>&1 >/dev/null && chruby 3.3.5
```

Now you have a clean install of ruby on your system, with less hassle and hopefully less issues, we can go on our merry way and venture into whatever catches our fancy.