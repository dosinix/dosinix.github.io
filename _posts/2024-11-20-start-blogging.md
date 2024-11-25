---
# layout: posts
title:  "Start Blogging"
date:   2024-11-20 23:20:00 -0500
author_profile: false
categories: jekyll update
---
I’m trying to start a blog using [GitHub Pages](https://pages.github.com/). And the main purpose of the blog is to document notes as I learn various things - now that with [ChatGPT](https://chatgpt.com/), I feel like learning new things is much easier. So naturally, I asked ChatGPT how to set up a personal blog on [GitHub](https://github.com/). And as it turned out, this process already involved a lot of learning. So my blog of learning notes actually starts with learning how to start this blog site….

According to the instructions I got from ChatGPT, I did the following:

1. Signed in to my GitHub account
2. Created a new repository named `dosinix.github.io`
3. Set it to public (because I'm not paying for GitHub)
4. Enabled GitHub Pages in the repo’s settings

Here comes the fun part. Because it seems popular or natural to use [Jekyll](https://jekyllrb.com/) for GitHub Pages, I followed the instructions to set that up. But like any software related problem, the process is never straightforward, especially on Windows…

## Problem 1 - Install Ruby

Because Jekyll requires it...

[Ruby](https://www.ruby-lang.org/en/) is a programming language just like [Python](https://www.python.org/). I’ve never used it before. But I was able to find an [extractable archive file](https://rubyinstaller.org/downloads/) to install the tool. A quick note here: I usually prefer using extractable archive over installer to install things on Windows with reasons not to be mentioned.

## Problem 2 - Install Bundler

[Bundler](https://bundler.io/) is a package for Ruby which is used to manage Ruby's packages (like Python's [`pip`](https://pypi.org/project/pip/)). Installing bundler is straightforward, just `gem install bundler`. Oh, because I didn’t use installer to install Ruby, I had to manually add Ruby’s directory `(…\ruby\bin)` to Windows `PATH` so `cmd` knows about the `gem` command.

## Problem 3 - Install Jekyll

Remember what I needed to install in the beginning? I finally got to do it after tackling the previous two. And boom, here I hit the wall….

After I typed in the command `gem install jekyll` and hit enter, everything seems OK; and I saw the prompt saying `Building native extensions. This could take a while...`. And a while later, I saw a bunch of errors. Long story short, I found out it was because I didn’t have Ruby’s Devkit (aka [MSYS2](https://www.msys2.org/)), because I used the extractable archive to install Ruby….

So I found MSYS2, and again an [extractable archive](https://github.com/msys2/msys2-installer/releases/tag/nightly-x86_64) for installation. I set up Windows `PATH` and tried installing Jekyll again. Still errors….

Another long story short, I found out that was because the MSYS2 didn’t know about Ruby’s directory (it couldn’t find a file called `ruby.h`)…. So I set the environment variables for MSYS2’s shell, as following,
      
```bash
# Set the environment variables (for MSYS2):
export CFLAGS="-I.../ruby/include/ruby-3.3.0"
export LDFLAGS="-L.../ruby/lib"
# .../ruby is where I installed Ruby
```
        
```bash
# Make the settings persist, add them to shell's profile config
echo 'export CFLAGS="-I.../ruby/include/ruby-3.3.0"' >~/.bashrc
echo 'export LDFLAGS="-L.../ruby/lib"' >~/.bashrc
```
        
```bash
# Reload shell's profile
source ~/.bashrc
```
        
```bash
# Add Ruby's 'pkgconfig' directory to 'PKG_CONFIG_PATH'
export PKG_CONFIG_PATH=".../ruby/lib/pkgconfig:$PKG_CONFIG_PATH"
```

```bash
# Again, add the variable to shell's profile config and reload it
echo 'export PKG_CONFIG_PATH=".../ruby/lib/pkgconfig:$PKG_CONFIG_PATH"' >~/.bashrc
source ~/.bashrc
```

After all these steps, it's better to verify the directories were added successfully.

Open the MSYS2 shell (e.g. `msys2.exe`) and run:

```bash
echo $CFLAGS
echo $LDFLAGS
echo $PKG_CONFIG_PATH
```

Make sure the outputs match what were just set.

So I tried installing Jekyll again. Still errors…. This time it says `gcc: command not found`.

Yet another long story short, it appeared that I downloaded the MSYS2 base package and there was no [MINGW Development Toolchain](https://www.mingw-w64.org/) included. And those dev tools are required to install Jekyll.

Looks like it’s getting close. So I installed those dev tools by running `ridk install` in the command prompt. After about 10 mins wait, I got all 50-ish packages installed. Ready to try again! Fingers crossed. Errors…. Still `gcc: command not found`.

I thought it was the `bash shell` didn’t know where to find the `gcc` file, so I did:

```sh
export PATH="/mingw64/bin:$PATH"
echo 'export PATH="/mingw64/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Ran again in command prompt. Same errors. 

Should I use `shell` (instead of `cmd`) to install? So I ran `gem install jekyll` in `bash shell`. It turned out `bash` couldn’t even recognize the `gem` command. What the…. 

AH HA, it’s the Windows thing again. Did you guess it? Yes, I need to add mingw’s directory to Windows `PATH`.

After so long, I was finally able to install Jekyll successfully. And here I have it for the first writeup. I have to thank ChatGPT for the help. Otherwise it would probably be hours of searching online and reading/skimming through questions and answers. Anyways, on to the next time.