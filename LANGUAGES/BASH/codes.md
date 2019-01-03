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
	  eval `ssh-agent -s`
	  ssh-add
  fi

fi
```

---

### Persist aliases
##### I use this script inside my .bash_profile to persist my aliases everytime that I open bash

```BASH
#!/bin/bash

# If doesn't exist, create a Google Drive directory in case I didn't install it
mkdir -p ~/Google\ Drive/

# If doesn't exist, create a tmp directorythat will store my aliases file path
mkdir -p ~/tmp/

# Create a aliases_path.dat file if doesn't exist
touch ~/tmp/aliases_path.dat

# Store the path of my .bash_aliases file in my /tmp/aliases_path.dat file
# to be readed and reused after in my functions
echo ~/Google\ Drive/.bash_aliases>~/tmp/aliases_path.dat

# Create a function to easy add aliases to a file
# In my case I'm saving a .bash_aliases file in my Google Drive folder because
# everytime that I change it, it will be automatically uploaded to my Google Drive
function addalias {
	aliases_path=`cat ~/tmp/aliases_path.dat`
	echo "alias $1=\"$2\"">>"$aliases_path" && source "$aliases_path"
}

# Create a function to easy remove aliases from a file
# In my case I'm saving a .bash_aliases file in my Google Drive folder because
# everytime that I change it, it will be automatically uploaded to my Google Drive
function removealias {
	aliases_path=`cat ~/tmp/aliases_path.dat`
	sed -i "/alias $1\=/d" "$aliases_path"
	unalias "$1"
}

# Then here I load the .bash_aliases file from my Google Drive directory
# everytime that you open bash
aliases_path=`cat ~/tmp/aliases_path.dat`
source "$aliases_path"
```

---

### Do a fast commit
##### Just a simple sintax sugar to execute git add, commit and push at once

```BASH
function fastpush {
	if [ ! -z "$1" ]; then
		eval "git add . && git commit -m \"$1\" && git push"
	else
		echo "You need to provide a commit message!"
	fi
}
```

---

### Fetch all repositories in the current path
##### I use this function to get all the repositories that exists inside the current path and fetch them all

```BASH
function fetchall() {
	echo ""
	nogitdirs=1
	for i in * ; do
		if [ -d "$i" ] ; then
			cd "$i"
			if git rev-parse --is-inside-work-tree > /dev/null 2>&1; then
				nogitdirs=0
				current_branch=$(git branch | grep \* | cut -d ' ' -f2 2>&1)
				
				if [ ! -z "$current_branch" ]; then
					
					echo -e "\e[94mFetching $i ($current_branch) ..."
					git fetch
					echo -e "\e[94mFetching $i ($current_branch) done"
					echo ""
						
				else
					
					echo -e "\e[94m$i \e[93m! Not a git repository or do not has a upstream set"
					
				fi
			fi
			cd ..
		fi
	done
	
	if [ "$nogitdirs" -gt 0 ] ; then
		echo -e "\e[91mNo git directories found"
	fi
}
```

---

### Check if there is changes to commit, push or pull
##### This function show if there is local or remote changes in all repositories inside the current path

```BASH
function statusall() {
	echo ""
	nogitdirs=1
	for i in * ; do
		if [ -d "$i" ] ; then
			cd "$i"
			if git rev-parse --is-inside-work-tree > /dev/null 2>&1; then
				nogitdirs=0
				changestoupmessage=""
				changestodownmessage=""
				current_branch=$(git branch | grep \* | cut -d ' ' -f2 2>&1)
				
				if [ ! -z "$current_branch" ]; then
					
					if [ -n "$(git diff --exit-code)" ]; then
						changestoupmessage="\e[93m> Changes to commit";
					elif [ -n "$(git diff --cached --exit-code)" ] ; then
						changestoupmessage="\e[93m> Stagged changes to commit";
					elif [ -n "$(git diff --exit-code origin/$current_branch $current_branch)" ]; then
						changestoupmessage="\e[93m> Commited changes to push"
					else
						changestoupmessage="\e[32m- No local changes";
					fi
					
					if [ -z "$current_branch" ]; then
						changestodownmessage=""
					elif [ -n "$(git remote show origin | grep "pushes to develop" | grep "local out of date")" ]; then
						changestodownmessage="\e[93m< You have changes to pull"
					else
						changestodownmessage="\e[32m- No remote changes"
					fi
					
					echo -e "\e[94m$i ($current_branch) $changestoupmessage $changestodownmessage"
					
				else
					
					echo -e "\e[94m$i \e[93m! Is not a git project or do not has a upstream set"
					
				fi
			fi
			cd ..
		fi
	done
	
	if [ "$nogitdirs" -gt 0 ] ; then
		echo -e "\e[91mNo git directories found"
	fi
}
```
