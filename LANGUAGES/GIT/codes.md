# **GIT**
Some GIT useful codes:

### Fix untracked files
```GIT
git rm -r --cached .
git add .
git commit -m "fixed untracked files"
```

##### References: [#1](http://stackoverflow.com/a/11451731/1988289)
----------

### Fix corrupted header
#### error: unable to unpack XXX header
```GIT
rm -f PATH_TO_HEADER_FILE
git fsck --full
```

##### References: [#1](http://stackoverflow.com/questions/23725925/git-repository-corrupt-incorrect-header-check-loose-object-is-corrupt)
----------
