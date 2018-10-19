# **BASH**
Some BASH useful codes/scripts:

### Ask for passphrase when open the BASH
##### When you are using GIT with a private ssh key you will have to start the user agent, open the ssh key in agent and then type the passphrase. After a few close/open BASH, this will be very anoying! So I create this script to get over the first two steps and jump right to the step that you have to type the passphrase.
```BASH
#!/bin/bash

# This condition is to call the agent just if the current path is a valid git repository
# I did that to prevent to start the agent for ever BASH console that I open, and execute just for those that I
# got to the git repo folder and start the BASH console directly from there.
# You can remove this condition if you wannna.
if git rev-parse --is-inside-work-tree > /dev/null 2>&1; then
  
  # if we can't find an agent, start one, and restart the script.
  if [ -z "$SSH_AUTH_SOCK" ] ; then
      exec ssh-agent bash -c "ssh-add ; $0"
      exit
  fi

fi
```
