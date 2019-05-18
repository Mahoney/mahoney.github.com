---
layout: post
title: Script to Install Public Key on Multiple Hosts
date: '2013-02-26T11:01:00.000Z'
author: Robert Elliot
tags: 
modified_time: '2013-06-18T15:28:03.124+01:00'
blogger_id: tag:blogger.com,1999:blog-8805447266344101474.post-2743063306351283016
blogger_orig_url: http://blog.lidalia.org.uk/2013/02/script-to-install-public-key-on.html
---

Here's a little script that can upload a public key onto a server - you could 
run it for multiple servers at the same time. Requires sshpass to be installed.
```bash
stty -echo
read -p "Password: " passw; echo
stty echo

public_key=`cat ~/.ssh/id_rsa.pub`

command="
mkdir -p ~/.ssh;
chmod og=,u=rwx ~/.ssh;
if [ ! -f ~/.ssh/authorized_keys ];
  then touch ~/.ssh/authorized_keys;
fi;
if ! grep -Fxq \"$public_key\" ~/.ssh/authorized_keys;
  then echo \"$public_key\" >> ~/.ssh/authorized_keys;
fi
"

function updateKey {
  sshpass -p $passw ssh -oLogLevel=Error -oStrictHostKeyChecking=no $1 $command
  if [ $? -eq 0 ]
    then echo "Updated key on $1"
  else
    echo "Failed to update key on $1"
  fi
}

updateKey myhostname

```
