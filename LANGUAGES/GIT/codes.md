# **GIT**
Some GIT useful codes:

### Fix untracked files
##### Sometimes you put some things on .gitignore but you realise that git do not ignore them, so this fix it.
```GIT
git rm -r --cached .
git add .
git commit -m "fixed untracked files"
```

##### References: [#1](http://stackoverflow.com/a/11451731/1988289)
----------

### Fix corrupted header
#### I used this code to fix this error: unable to unpack XXX header
```GIT
rm -f PATH_TO_HEADER_FILE
git fsck --full
```

##### References: [#1](http://stackoverflow.com/questions/23725925/git-repository-corrupt-incorrect-header-check-loose-object-is-corrupt)
----------

### Configure GIT proxy
##### Clear old settings:
```GIT
git config --global --unset https.proxy
git config --global --unset http.proxy
```
##### Set new settings:
```GIT
git config --global https.proxy https://USER:PWD@proxy.whatever:80
git config --global http.proxy http://USER:PWD@proxy.whatever:80
```
##### Verify settings:
```GIT
git config --get https.proxy
git config --get http.proxy
```

##### References: [#1](http://stackoverflow.com/a/15647280/1988289)
----------
