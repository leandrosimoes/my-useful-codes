# **GIT**
Some GIT useful codes:

### Fix untracked files
##### Sometimes you put some things on .gitignore but you realise that git do not ignore them, so this fix it.
```BASH
git rm -r --cached .
git add .
git commit -m "fixed untracked files"
```

##### References: [#1](http://stackoverflow.com/a/11451731/1988289)
----------

### Fix corrupted header
#### I used this code to fix this error: unable to unpack XXX header
```BASH
rm -f PATH_TO_HEADER_FILE
git fsck --full
```

##### References: [#1](http://stackoverflow.com/questions/23725925/git-repository-corrupt-incorrect-header-check-loose-object-is-corrupt)
----------

### Configure GIT proxy
##### Clear old settings:
```BASH
git config --global --unset https.proxy
git config --global --unset http.proxy
```
##### Set new settings:
```BASH
git config --global https.proxy https://USER:PWD@proxy.whatever:80
git config --global http.proxy http://USER:PWD@proxy.whatever:80
```
##### Verify settings:
```BASH
git config --get https.proxy
git config --get http.proxy
```

##### References: [#1](http://stackoverflow.com/a/15647280/1988289)
----------

### Configure kdiff3 as default diff tools for GIT
```BASH
git config --global --add merge.tool kdiff3
git config --global --add mergetool.kdiff3.path "C:/Program Files/KDiff3/kdiff3.exe"
git config --global --add mergetool.kdiff3.trustExitCode false

git config --global --add diff.guitool kdiff3
git config --global --add difftool.kdiff3.path "C:/Program Files/KDiff3/kdiff3.exe"
git config --global --add difftool.kdiff3.trustExitCode false
```
