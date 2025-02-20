---
layout: post
title: "Wait for it"
---
### _I dont like to_ *wait* _for *petty PIDS!*_

<!-- ![_config.yml]({{ site.baseurl }}/images/config.png) -->

Today I remembered that pidof is a thing. 
    
  I was trying to write a script that would wait for a command to finish and then run another 
command. Then I remembered that `pidof` is a thing. It that takes a process name and returns the PID of that process.
So I wrote a few bash lines as a test.


```bash
while true; do if ! pidof $awaitedcmd > /dev/null; then echo "your other command"; fi; sleep 1; done
```

for those moments when you must wait for a command to finish before running another command, and you want to _ensure_ 
that the first command has successfully finished before running the second command.

the key here is ```do if !``` pidof N . It will return true if the process is not running. So we can do some 
sane error checking in favour of, _hopefully_, omnipotentcy (??) and peace of mind.


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
    echo "Process $target_pid has finished"
    exit 0
else
    echo "Process $target_pid has finished"
    exit 1
fi

```
