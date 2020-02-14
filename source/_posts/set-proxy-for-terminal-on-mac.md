---
title: Set proxy for terminal on mac
date: 2020-02-09 03:19:35
keywords: proxy shadowsocks mac
description:
tags: 
  - mac
---
## Shadowsocks Proxy

### [Server-side setup](https://github.com/shadowsocks/shadowsocks)

```sh
apt-get install python-pip
pip install shadowsocks

sudo ssserver -p 443 -k password -m aes-256-cfb --user nobody -d start
```


### Client-side setup

* Install Shadowsocks Client, e.g. run `brew cask install shadowsocksx` in macOS.
* SwitchSharp

> Only config `SOCKS Host` to `127.0.0.1:1080` and select `SOCKS v5`

<!-- more -->

## SOCKS 5 Through SSH Tunnel

```sh
# `-N`: do not execute commands
# `-D`: bind 1080 port and forward 1080 port to 22 port
# `-i`: use pre-shared key `hello.pem`
# `-p`: specify port used to connect to remote server
ssh -ND 1080 -i ~/.ssh/hello.pem <username>@<your-remote-server-ip> -p 22
```


### Reference

* [how-do-i-set-up-a-linux-proxy-for-my-mac by askubuntu](http://askubuntu.com/questions/143303/how-do-i-set-up-a-linux-proxy-for-my-mac)


## SOCKS 5 in Terminal

### Using Proxychains-ng to socksify your command

* Install `proxychains-ng` by running `brew install proxychains-ng`.
* Config `/usr/local/Cellar/proxychains-ng/4.7/etc/proxychains.conf`, modify the following parameters.

```config
[ProxyList]
socks5 127.0.0.1 1080
```

* Add `proxychains4` to the front of every command, e.g. `proxychains4 curl ipecho.net/plain`

> Also works for Application like `Google Chrome` while `Safari` does not work, try `proxychains4 /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome `.
> But this might slow down Chrome. 

> You can also use [dsocks](http://monkey.org/~dugsong/dsocks/) for mac(which does not work for me), [tsocks](http://tsocks.sourceforge.net/) for linux.

## Using Unix Proxy Environment Variable

Add this script to `~/.bash_profile`.

```sh
export http_proxy=socks5://127.0.0.1:1080
export https_proxy=socks5://127.0.0.1:1080
```


## Config `curl` to use SOCKS5(Also works for `homebrew`)

Add the following line to `~/.curlrc`. Since `homebrew` use `curl` to download the package, this will also enable `homebrew` to use SOCKS5.

```config
socks5 = "socks5://127.0.0.1:1080"
```

> There are no environment variables for SOCKS5 proxy servers in unix, so in order to use SOCKS5 in other utilities, check the man pages for existing tools to see if they have a configuration option for a SOCKS5 proxy and whether they have a configuration file that the configuration can be added to.


## Config `git` to use SOCKS5

* For `https://` and `http://`(e.g. `http://github.com`, `https://github.com`), run the following script.

```sh
git config --global http.proxy 'socks5://127.0.0.1:1080'
git config --global https.proxy 'socks5://127.0.0.1:1080'
```

* For `git://`(e.g. `git://github.com`), run `git config --global core.gitproxy 'socks5://127.0.0.1:1080'`
* For ssh(e.g. `git@github.com`,`ssh://git@github.com`), add `ProxyCommand nc -x localhost:1080 %h %p` to `~/.ssh/config` file.

> `git config --global ` is stored in `~/.gitconfig` while local config settings is in `./.git/config`.
> To remove a configuration, e.g. run `git config --global --unset core.gitproxy`.

## Reference

* [how to set socks5 proxy in the terminal by askubuntu](http://askubuntu.com/questions/610333/how-to-set-socks5-proxy-in-the-terminal)
* [OS X Terminal Ignoring SOCKS Proxy Setup](http://superuser.com/questions/377199/os-x-terminal-ignoring-socks-proxy-setup)
* [Git proxy through SOCKS 5 by cms-sw](http://cms-sw.github.io/tutorial-proxy.html)
* [git proxy on segmentfault](http://segmentfault.com/q/1010000000118837)


## Tools

### Test wheather proxy works. 
```sh
curl --socks5-hostname 127.0.0.1:1080 http://wtfismyip.com/json
```