# **GIT**
Some GIT useful codes:

## Summary

* [Fix Untracked Files](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/GIT/codes.md#fix-untracked-files)
* [Fix Corrupted Header](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/GIT/codes.md#fix-corrupted-header)
* [Configure Git Proxy](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/GIT/codes.md#configure-git-proxy)
* [Configure Kdiff3 As Default Diff Tool](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/GIT/codes.md#configure-kdiff3-as-default-diff-tools-for-git)
* [Remove Git Pager For Every Command](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/GIT/codes.md#remove-git-pager-for-every-command)

----------

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
----------

### Remove git pager for every command
```BASH
git config --global core.pager cat
```

##### References: [#1](https://stackoverflow.com/questions/2183900/how-do-i-prevent-git-diff-from-using-a-pager/14118014#14118014)

----------
