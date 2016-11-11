# **C#**
Some C# useful codes:

### Copy directory recursively
##### With this code you can copy directory recursively copying all your files and subdirectories

```CSHARP
private static void DirectoryCopy(string sourceDirName, string destDirName, bool copySubDirs) {
    var dir = new DirectoryInfo(sourceDirName);

    if (!dir.Exists) {
        throw new DirectoryNotFoundException(
            "Source directory does not exist or could not be found: "
            + sourceDirName);
    }

    var dirs = dir.GetDirectories();
    if (!Directory.Exists(destDirName)) {
        Directory.CreateDirectory(destDirName);
    }

    var files = dir.GetFiles();
    foreach (FileInfo file in files) {
        var temppath = Path.Combine(destDirName, file.Name);
        file.CopyTo(temppath, true);
    }

    if (copySubDirs) {
        foreach (DirectoryInfo subdir in dirs) {
            var temppath = Path.Combine(destDirName, subdir.Name);
            DirectoryCopy(subdir.FullName, temppath, copySubDirs);
        }
    }
}
```