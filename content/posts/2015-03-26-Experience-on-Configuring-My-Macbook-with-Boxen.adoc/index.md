# Experience on Configuring My Macbook with Boxen

:published_at: 2015-03-26
:hp-tags: 


## Some Introduction About https://boxen.github.com[Boxen]

Boxen is effectively a project consisting of Puppet configuration, with somewhat opinionated way of configuring OS X (yea just OS X, not even Linux box).

It's created by developers it Github themselves, to help them automate their dev machine for their own development. When new guy join Github, they'll just let the new guy use the boxen script and customize it as needed.

Read more on their website.

## Why I do it?

I have lots of crapware on my mac. You know when you feel lazy and install Ruby gem globally. I also try out stuff on mac and it generates lots of funny dotfiles on my home directory. Really annoying to clean those...

I decided the extreme. I want to format the OS X and start fresh and clean with some Boxen configuration management!

I'm pretty confident on formatting the machine with my TIme Machine backup.

## My Setup

I forked their https://github.com/boxen/our-boxen[main template] to https://github.com/faizhasim/our-boxen[my own repo].

I've added and configure some additional apps for mine, which includes:

- ohmyzsh
- dropbox
- tmux
- iterm2
- alfred
- spotify
- imagemagick
- intellij
- etc...

I;ve also configured some OSX settings like Dock behaviour and accessibility options using Boxen. Pretty good imo...

Regardless, as of now, without actually hacking OSX plist, you can't manage apps from the App Store. Not a big thing for me.

Configuration-wise, I have my own private `facter` YML which I manage on my own without committing publically to Github. You know like those IP addresses of your work etc. (Note: Most of my secrets are properly managed in 1Password - including certs etc).


## Notes

- Some apps doesn't really work well with Boxen. For eg, for some reasons, Omnifocus didn't really work and different version of KeePass is being installed.
- I removed `hub` package. It screw up my git completion.
- I have not yet manage my `.vimrc` file using Boxen. Will need to do it later.
- You still need to configure app-based configuration manually. (At least without requiring too much effort).
- `dnsmasq` is awesome. I never use it. I tried it and I like it.

