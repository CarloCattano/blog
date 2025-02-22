---
layout: post
title: "Wait for it"
---
### _I'm not *waiting* for_ **petty PID's!**

<!-- ![_config.yml]({{ site.baseurl }}/images/image.jpg) -->
<img src="{{ site.baseurl }}/images/image.jpg" alt="Image" width="300" height="auto">

> While I was waiting for long build to finish while having to update and install some other packages right after it. Today I had a renewed appetite for automation.

A queue of sorts. Imagine you need to compile this really big project that takes ages
to finish. And right after you should do another repetitive job or run a test, and you dont want to be around to wait for _both_ commands or more.


Then I remembered that ```pidof``` is a thing. It that takes a process name and returns the PID of that process.

```bash
do if ! pidof $awaitedcmd;
then echo "your other command"; 
```

for those moments when you must wait for a command to finish before running another command, and you want to _ensure_ 
that the first command has successfully finished before running the second command.
And unlike using ```echo first && echo second``` , it can be run from any other terminal and after the process has already triggered.

the key here is ```do if !``` pidof N . It will return true if the process is not running. So we can do some 
sane error checking in favour of, _hopefully_, idempotency (??) and peace of mind.


```bash
#!/bin/bash

set -e

target_pid=$1
shift
command="$@"

if ! [[ $target_pid =~ ^[0-9]+$ ]]; then
    echo "Wrong PID provided"
    exit 1
fi

if [ -z "$command" ]; then
    echo "Usage: $0 <PID> <command> [args...]"
    exit 1
fi

if ! ps -p "$target_pid" > /dev/null; then
    echo "No such process $target_pid"
    exit 1
fi

while ps -p "$target_pid" > /dev/null; do
    sleep 1
done

eval "$command"

SUCCESS=$?

if [ $SUCCESS -eq 0 ]; then
    echo "Process $target_pid has finished and executed successfully"
    exit 0
else
    echo "Process $target_pid has finished but command failed"
    exit 1
fi
```
