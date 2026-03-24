# DesktopConfig
Basic configuration settings I use with my Linux/MacOS/BSD. 

Guaranteed not to be interesting or exciting.

## How to use the files

There are different full or partial config files in this repo that I use
to help keep my sanity while using different programs. 
The classical example is that some Linux installs like to use as default
colours I can't see on the screen (for reasons). 

I will try to describe how each of these settings are put together so you
can customize them to your tastes: what I like and/or can see is different
than you.

There are two ways to get the files in this repo

1. Download the entire repo and then grab the files you want. 
You are staring at the repo. Click on the gree Code button and then 
it will give you a link 
(I think it is [https://github.com/raubvogel/DesktopConf.zip](https://github.com/raubvogel/DesktopConf.zip) but don't quote me on that)
which you can download from the command line (`wget` and `curl` comes to mind)
or just click on it and your web browser should do things.
Hey, I use command line most of the time, so I am guessing here. 

   In any case, once you download it, unzip it off you go.

   If you have `git` installed just do 

   ```bash
   git clone https://github.com/raubvogel/DesktopConf.git
   ```

2. Download just the files you want to use. 
Let's be honest: most of the time you only need a couple of three files
anyway, so why not only fetch them? 
In the following lines I will be focusing on that, from the command line
so you can script it out.

Let's now go into specific details for each file here (yes, that means this
file you are reading right now will be updated as time goes by):

### vi/vim: `vimrc`

#### Features

- vi sessions aware of the file type I am using 
- Show the different commands in colours I can see 
- When moving the arreow keys, or hjkl keys, vi will leave the insert mode

That is done editing the `~/.vim/vimrc` file. If you do not have one, you
can use mine by

#### Steps
1. Create a `~/.vim/` directory.

1. Download my `vimrc` and install it. If you have `curl` (MacOS and many
Linux distros have it by default, or it is a package away), you can then run

```bash
mkdir ~/.vim
curl https://raw.githubusercontent.com/raubvogel/DesktopConf/master/vimrc > ~/.vim/vimrc 
```

#### Issues

##### Sorry, the command is not available in this version: syntax on

After you install this `vimrc` file (or append it to yours), you may see the 
following error (`E319`) message when you try to open a file:

```bash
user@desktop:~$ vi some-file.sh 
Error detected while processing /home/woozle/.vim/vimrc:
line    7:
E319: Sorry, the command is not available in this version: syntax on
Press ENTER or type command to continue
```

The first thing you should do next is to find which version of `vi` you are
running; one way is to use the `readlink` command:

```bash
user@desktop:~$ readlink -f $(which vi)
/usr/bin/vim.tiny
user@desktop:~$ 
```

If the outcome is `/usr/bin/vim.tiny` as shown above, you have the bare minimal
`vi` package; people using ubuntu and debian may find that as the default. You
have then two options:

1. Stick to `vim.tiny`. Nothing wrong with that; it just means my `vimrc` will
not work for you as it stands. Well, it will if you just comment out the
`syntax on` line by placing a `"` on the beginning of the line.

   ```bash
   " syntax on
   ```
2. Upgrade to a more full-fledged version of `vim`. For a debian/ubuntu derived
distro, that would be `vim`.

```bash
apt-get install vim
```

### Bourne/Bash prompt: `prompt`
IMHO, the prompt should be helpful. Specifically like to know which user I 
am currently using, the name of the host I am logged into, and the path.
Imagine your username is `bob` and you just logged into the `hairless-ape`
host, which could be a laptop or some server somewhere in the cloud or under
the bed of a friend in [Wroclaw](https://en.wikipedia.org/wiki/Wroc%C5%82aw).
For some of you, this prompt suffices
 
```bash
sh-4.2$
```

but, for me, something like this is not only more useful but also can 
(actually, will. It happened before and it was not a pretty sight) 
avoid me running the wrong command in the wrong computer:

```bash
bob@hairless-ape:/tmp$ 
```

You decide. Now, if you are like me, you want to edit the prompt of your
account to look like that. This is what the `prompt` file in this repo does.

If you use Bash, 
you may already have a `.bashrc` file. So you either paste the contents of
the `prompt` file in it somewhere or save it as a separate file and then 
add instructions to your `.bashrc` to read it; something like this
(make sure you do not have a `~/.prompt` already in place. If you do, rename
the file you are getting from my repo; it will not care, I promise):

```bash
curl https://raw.githubusercontent.com/raubvogel/DesktopConf/master/prompt > ~/.prompt 

ln -s .bash_profile .profile

cat >> ~/.bashrc << 'EOF'

# Set the prompt
if [ -f ~/.prompt ]; then
        . ~/.prompt
fi
EOF
```
Now that is done, if you want to enable the prompt right now, just type
```bash
. ~/.prompt
``` 
and it should change automagically. The `.bashrc` editing
you did is just so the prompt will be set whenever you login or start a new
session.

### Screen with window labels and longer scroll size
I use [`screen`](https://www.gnu.org/software/screen/) a lot, but by default it does not tell me which window I am at,
which can be rather frustrating. But, it is nothing  a `.screenrc` file 
can't address and increase the scrollback/buffer size (useful to go back on 
what
happened in that window)i while we are at it.

Installing it is pretty much business as usual, but I would suggest to 
do it before running `screen`. Also note in the following example you are 
overwriting the current `~/.screenrc`. If you already have something
configured, you may want to look at my 
[version of the file](https://github.com/raubvogel/DesktopConf/blob/master/screenrc) and see if there is anything you want to steal from it.

```bash
curl https://raw.githubusercontent.com/raubvogel/DesktopConf/master/screenrc > ~/.screenrc 
```

### Run protonVPN whenever computer is connected to the network

As the name of this session implies, I run [protonVPN](https://protonvpn.com/).
And if you have been following this document, I like to do stuff using the
command line instead of GUI whenever possible. So, what about automating
starting the vpn as soon as a network connection is established?

Assuming you have `protonvpn-cli` already installed, and already configured
it to login as your user (in this case `vpnuser@example.com`)

```bash
protonvpn signin vpnuser@example.com
```

here is how to trigger
it using systemd. Since we would start protonvpn as your user, why not run
the systemd service as your user?

```bash
mkdir -p ~/.config/systemd/user
curl https://raw.githubusercontent.com/raubvogel/DesktopConf/master/protonvpn.service > ~/.config/systemd/user/protonvpn.service

systemctl --user enable protonvpn.service
systemctl --user start protonvpn.service
```

protonVPN here is set to start using the United States as its country; this
should work for both free and paid levels. However, you should adjust 
`~/.config/systemd/user/protonvpn.service` to
fit your needs.
