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

-------

### Open in default browser
##### Open url in users default browser safely

```CSHARP
public static string GetDefaultBrowserPath() {
    const string urlAssociation = @"Software\Microsoft\Windows\Shell\Associations\UrlAssociations\http";
    const string browserPathKey = @"$BROWSER$\shell\open\command";

    try {
        var userChoiceKey = Registry.CurrentUser.OpenSubKey(urlAssociation + @"\UserChoice", false);

        if (userChoiceKey == null) {
            var browserKey = Registry.ClassesRoot.OpenSubKey(@"HTTP\shell\open\command", false) ??
                                Registry.CurrentUser.OpenSubKey(urlAssociation, false);

            var path = browserKey?.GetValue(null).ToString() ?? string.Empty;
            browserKey?.Close();
            return path;
        }

        var progId = (userChoiceKey.GetValue("ProgId").ToString());
        userChoiceKey.Close();

        var concreteBrowserKey = browserPathKey.Replace("$BROWSER$", progId);
        var kp = Registry.ClassesRoot.OpenSubKey(concreteBrowserKey, false);
        var browserPath = kp?.GetValue(null).ToString() ?? string.Empty;
        kp.Close();
        return browserPath;
    } catch (Exception ex) {
        return string.Empty;
    }
}

private void OpenInDefaultBrowser(string url) {
    try {
        var path = GetDefaultBrowserPath();

        if (!string.IsNullOrEmpty(path)) {
            Process.Start(path.Split('-').FirstOrDefault()?.Trim() ?? "iexplore.exe", url);
            return;
        }

        throw new Exception("No default browser found.");
    } catch (Exception ex) {
        throw new Exception($"Can't open the url in default browser. {Environment.NewLine}{Environment.NewLine} Details: {ex.Message}");
    }
}
```

--------

### Force form focus
##### This code use the Windows API to force form focus when open it

```CSHARP
[DllImport("user32.dll")]
private static extern bool SetForegroundWindow(IntPtr hWnd);
[DllImport("user32.dll")]
private static extern bool ShowWindowAsync(IntPtr hWnd, int nCmdShow);
[DllImport("user32.dll")]
private static extern bool IsIconic(IntPtr hWnd);
[DllImport("user32.dll", SetLastError = true)]
private static extern bool SystemParametersInfo(uint uiAction, uint uiParam, IntPtr pvParam, uint fWinIni);
[DllImport("user32.dll", SetLastError = true)]
private static extern uint GetWindowThreadProcessId(IntPtr hWnd, IntPtr lpdwProcessId);
[DllImport("user32.dll")]
private static extern IntPtr GetForegroundWindow();
[DllImport("user32.dll")]
private static extern bool AttachThreadInput(uint idAttach, uint idAttachTo, bool fAttach);
[DllImport("user32.dll")]
static extern bool BringWindowToTop(IntPtr hWnd);
[DllImport("User32.dll")]
private static extern IntPtr GetParent(IntPtr hWnd);

private const int SW_SHOW = 5;
private const int SW_RESTORE = 9;

private const uint SPI_GETFOREGROUNDLOCKTIMEOUT = 0x2000;
private const uint SPI_SETFOREGROUNDLOCKTIMEOUT = 0x2001;
private const int SPIF_SENDCHANGE = 0x2;

//Use this function to force the form focus after form Form.Show() this way: WindowHelper.Activate(Handle)
public static void Activate(IntPtr hWnd) {
    if (IsIconic(hWnd))
        ShowWindowAsync(hWnd, SW_RESTORE);

    ShowWindowAsync(hWnd, SW_SHOW);

    SetForegroundWindow(hWnd);

    IntPtr foregroundWindow = GetForegroundWindow();
    IntPtr Dummy = IntPtr.Zero;

    uint foregroundThreadId = GetWindowThreadProcessId(foregroundWindow, Dummy);
    uint thisThreadId = GetWindowThreadProcessId(hWnd, Dummy);

    if (AttachThreadInput(thisThreadId, foregroundThreadId, true)) {
        BringWindowToTop(hWnd);
        SetForegroundWindow(hWnd);
        AttachThreadInput(thisThreadId, foregroundThreadId, false);
    }

    if (GetForegroundWindow() != hWnd) {
        IntPtr Timeout = IntPtr.Zero;
        SystemParametersInfo(SPI_GETFOREGROUNDLOCKTIMEOUT, 0, Timeout, 0);
        SystemParametersInfo(SPI_SETFOREGROUNDLOCKTIMEOUT, 0, Dummy, SPIF_SENDCHANGE);
        BringWindowToTop(hWnd);
        SetForegroundWindow(hWnd);
        SystemParametersInfo(SPI_SETFOREGROUNDLOCKTIMEOUT, 0, Timeout, SPIF_SENDCHANGE);
    }
}
```

--------

### Set start of your application with windows startup
##### Set your application on Windows Startup (Do not work on WIN 10+)

```CSHARP
private bool SetStartup() {
    try {
        var key = Registry.CurrentUser.OpenSubKey("SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Run", true);
        if (key == null) { throw new Exception("The registry key do not exist or is bloqued."); }

        if (cbIniciar.Checked) {
            key.SetValue("YourApp.exe", Application.ExecutablePath);
            return true;
        }

        return true;
    } catch (Exception ex) {
        MessageBox.Show(ex.Message);
        return false;
    }
}
```

--------

### Keyboard delay
##### Keyboard delay, useful for searches

```CSHARP
private System.Windows.Forms.Timer _delay;

//This example is for a TextBox search
//On text change I restart the counter
private void txtSearch_TextChanged(object sender, EventArgs e) {
    RestartQueryTimer();
}

void queryTimer_Tick(object sender, EventArgs e) {
    RevokeQueryTimer();

    //Here you put your code to the search
}

void RevokeQueryTimer() {
    if (_delay != null) {
        _delay.Stop();
        _delay.Tick -= queryTimer_Tick;
        _delay = null;
    }
}

void RestartQueryTimer() {
    if (_delay == null) {
        _delay = new System.Windows.Forms.Timer { Enabled = true, Interval = 500 };
        _delay.Tick += queryTimer_Tick;
    } else {
        _delay.Stop();
        _delay.Start();
    }
}
```

--------

### Drag form
##### With this code you can drag the form clicking in any where you want

```CSHARP
public const int WM_NCLBUTTONDOWN = 0xA1;
public const int HT_CAPTION = 0x2;

[DllImportAttribute("user32.dll")]
public static extern int SendMessage(IntPtr hWnd, int Msg, int wParam, int lParam);
[DllImportAttribute("user32.dll")]
public static extern bool ReleaseCapture();

//Put this function on MouseDown event of the element that you want that the drag works
//Can be the whole form
private void Form1_MouseDown(object sender, MouseEventArgs e) {
    if (e.Button == MouseButtons.Left) {
        ReleaseCapture();
        SendMessage(Handle, WM_NCLBUTTONDOWN, HT_CAPTION, 0);
    }
}
```

--------

### Repositioning text cursor
##### Repositioning the text cursor at the end of the TextBox

```CSHARP
public void Reposition() {
    txtTest.Focus();
    txtTest.SelectionStart = txtTest.Text.Length;
    txtTest.SelectionLength = 0;
}
```

--------

### Creating a context menu for you app
##### Creating a context menu for you app when right click in files and folders

```CSHARP
private void CreateFilesContextMenu() {
    try {
        var rootDir = AppDomain.CurrentDomain.BaseDirectory;

        if (Registry.ClassesRoot.GetSubKeyNames().All(r => r != "*"))
            Registry.ClassesRoot.CreateSubKey("*");

        var allKey = Registry.ClassesRoot.OpenSubKey("*", true);

        if (allKey.GetSubKeyNames().All(r => r != "shell"))
            allKey.CreateSubKey("shell");

        var shellKey = allKey.OpenSubKey("shell", true);

        #region Registry
        if (shellKey.GetSubKeyNames().All(r => r != "Description of your function"))
            shellKey.CreateSubKey("Description of your function");

        var registryKey = shellKey.OpenSubKey("Description of your function", true);

        if (registryKey.GetValueNames().All(r => r != "Icon"))
            registryKey.SetValue("Icon", rootDir + "icon-of-your-app.ico");

        if (registryKey.GetSubKeyNames().All(r => r != "command"))
            registryKey.CreateSubKey("command");

        var commandKey = registryKey.OpenSubKey("command", true);
        commandKey.SetValue("", rootDir + "YouApp.exe %1"); //The params will be passed in args[]
        #endregion
    } catch (Exception ex) {
        MessageBox.Show(ex.Message);
    }
}

private void CreateDirectoryContextMenu() {
    try {
        var rootDir = AppDomain.CurrentDomain.BaseDirectory;

        if (Registry.ClassesRoot.GetSubKeyNames().All(r => r != "Directory"))
            Registry.ClassesRoot.CreateSubKey("Directory");

        var chaveDiretorio = Registry.ClassesRoot.OpenSubKey("Directory", true);

        if (chaveDiretorio.GetSubKeyNames().All(r => r != "shell"))
            chaveDiretorio.CreateSubKey("shell");

        var shellKey = chaveDiretorio.OpenSubKey("shell", true);

        #region Registry
        if (shellKey.GetSubKeyNames().All(r => r != "Description of your function"))
            shellKey.CreateSubKey("Description of your function");

        var registryKey = shellKey.OpenSubKey("Description of your function", true);

        if (registryKey.GetValueNames().All(r => r != "Icon"))
            registryKey.SetValue("Icon", rootDir + "icon-of-your-app.ico");

        if (registryKey.GetSubKeyNames().All(r => r != "command"))
            registryKey.CreateSubKey("command");

        var commandKey = registryKey.OpenSubKey("command", true);
        commandKey.SetValue("", rootDir + "YourApp.exe %1"); //The params will be passed in args[]
        #endregion
    } catch (Exception ex) {
        MessageBox.Show(ex.Message);
    }
}
```

--------

### Creating a system tray icon
##### Creating a system tray icon for your app with context menu

```CSHARP
//First of all, put a NotifyIcon control in your form
//Than use this function to show the icon on Form.Show()
//To remove the icon on close, just do this: myNotityIcon.Icon = null
private void SetupSysTrayIcon() {
    try {
        var menu = new ContextMenu();

        var menu1 = new MenuItem();
        var menu2 = new MenuItem();
        var menu3 = new MenuItem();

        var menuList = new[] { menu1, menu2, menu3 };
        menu.MenuItems.AddRange(menuList);

        menu1.Index = 0;
        menu1.Text = "&Text of your menu item";
        menu1.Click += EventToThatWillBeExecutedOnClick;

        menu2.Index = 1;
        menu2.Text = "&Text of your menu item";
        menu2.Click += EventToThatWillBeExecutedOnClick;

        menu3.Index = 2;
        menu3.Text = "&Text of your menu item";
        menu3.Click += EventToThatWillBeExecutedOnClick;

        myNotityIcon.ContextMenu = menu;
    } catch (Exception ex) {
        MessageBox.Show(ex.Message);
    }
}
```

--------

### Verify write permissions on file or directory
##### With this code you can verify if the file or directory has write permissions

```CSHARP
public static bool FileHasPermisson(string path) {
    try {
        var writeAllow = false;
        var writeDeny = false;
        var accessControlList = File.GetAccessControl(path);
        if (accessControlList == null)
            return false;

        var accessRules = accessControlList.GetAccessRules(true, true, typeof(SecurityIdentifier));

        foreach (FileSystemAccessRule rule in accessRules) {
            if ((FileSystemRights.ReadAndExecute & rule.FileSystemRights) != FileSystemRights.ReadAndExecute)
                continue;

            if (rule.AccessControlType == AccessControlType.Allow)
                writeAllow = true;
            else if (rule.AccessControlType == AccessControlType.Deny)
                writeDeny = true;
        }

        return writeAllow && !writeDeny;
    } catch (Exception) {
        return false;
    }
}

public static bool DirectoryHasPermission(string path) {
    try {
        var writeAllow = false;
        var writeDeny = false;
        var accessControlList = Directory.GetAccessControl(path);
        if (accessControlList == null)
            return false;

        var accessRules = accessControlList.GetAccessRules(true, true, typeof(SecurityIdentifier));

        if (!Directory.EnumerateDirectories(path).Any() && !Directory.EnumerateFiles(path).Any()) { return false; }

        foreach (FileSystemAccessRule rule in accessRules) {
            if ((FileSystemRights.ReadAndExecute & rule.FileSystemRights) != FileSystemRights.ReadAndExecute)
                continue;

            if (rule.AccessControlType == AccessControlType.Allow)
                writeAllow = true;
            else if (rule.AccessControlType == AccessControlType.Deny)
                writeDeny = true;
        }

        return writeAllow && !writeDeny;
    } catch (Exception) {
        return false;
    }
}
```

--------

### Get default browser icon
##### With this code you can get the user default browser icon 

```CSHARP
private Icon GetDefaultBrowserIcon() {
    const string userChoice = @"Software\Microsoft\Windows\Shell\Associations\UrlAssociations\http\UserChoice";
    string progId;
    using (RegistryKey userChoiceKey = Registry.CurrentUser.OpenSubKey(userChoice, true)) {
        if (userChoiceKey == null) {
            return null;
        }

        object progIdValue = userChoiceKey.GetValue("Progid");

        if (progIdValue == null) {
            return null;
        }

        progId = progIdValue.ToString();
    }

    return GetIcon(progId);
}

private Icon PegarIconeBrowserPadrao(string progId) {
    const string exeSuffix = ".exe";
    string browserKeyPath = progId + @"\shell\open\command";
    FileInfo browserInfo;
    using (RegistryKey pathKey = Registry.ClassesRoot.OpenSubKey(browserKeyPath, true)) {
        if (pathKey == null) {
            return null;
        }

        try {
            string browserPath = pathKey.GetValue(null).ToString().ToLower().Replace("\"", "");
            if (!browserPath.EndsWith(exeSuffix)) {
                browserPath = browserPath.Substring(0, browserPath.LastIndexOf(exeSuffix, StringComparison.Ordinal) + exeSuffix.Length);
                browserInfo = new FileInfo(browserPath);
                return Icon.ExtractAssociatedIcon(browserInfo.ToString());
            }
        } catch {
            return null;
        }
    }

    return null;
}
```

--------

### Verify URL
##### With this code you can verify if an URL is valid 

```CSHARP
public bool VerifyUrl(string url, ref string returnProtocol) {    
    var protocols = new[] { "http://", "https://", "http://www.", "https://www." };

    foreach (var protocol in protocols) {
        if (url.IndexOf(protocol) != -1) { url = url.Replace(protocol, ""); }

        var wr = (HttpWebRequest)WebRequest.Create(protocol + url);
        wr.Timeout = 10000;
        wr.Credentials = CredentialCache.DefaultNetworkCredentials;

        try {
            var resposta = wr.GetResponse();
            returnProtocol = protocol;
            resposta.Close();
            return true;
        } catch (Exception ex) {
            MessageBox.Show(ex.Message);
        }
    }

    return false;
}
```


--------

### Send Push Notifications By Google Firebase
##### I Use this code to send push notifications by Google Firebase to my mobile apps

```CSHARP
public class PushNotificationModel {
	public PushNotificationModel(string title, string message = "", string icon = "", string action = "", string tag = "", string color = "", string sound = "") {
		this.Title = title;
		this.Message = message;
		this.Icon = icon;
		this.Action = action;
		this.Tag = tag;
		this.Color = color;
		this.Sound = sound;
	}

	public string Title { get; set; }
	public string Message { get; set; }
	public string Icon { get; set; }
	public string Action { get; set; }
	public string Tag { get; set; }
	public string Color { get; set; }
	public string Sound { get; set; }
}

public class PushNotificationService {
	private string _applicationId;
	private string _senderId;
	private string _deviceId;

	public PushNotificationService(string applicationId, string senderId, string deviceId) {
		_applicationId = applicationId;
		_senderId = senderId;
		_deviceId = deviceId;
	}

	public bool Send(PushNotificationModel obj, ref string error = "") {
		try {
			if ((string.IsNullOrEmpty(obj.Title))) {
				error = "\"Title\" is required";
				return false;
			}

			if ((string.IsNullOrEmpty(obj.Message))) {
				error = "\"Message\" is required";
				return false;
			}

			if ((string.IsNullOrEmpty(_applicationId) || string.IsNullOrEmpty(_senderId) || string.IsNullOrEmpty(_deviceId))) {
				error = "All class params are required.";
				return false;
			}

			dynamic applicationID = _applicationId;
			dynamic senderId = _senderId;
			string deviceId = _deviceId;

			Random rdm = new Random();
			dynamic data = new {
				to = deviceId,
				priority = "high",
				notification = new {
					title = obj.Title,
					body = obj.Message,
					icon = string.IsNullOrEmpty(obj.Icon) ? "ic_launcher" : obj.Icon,
					sound = string.IsNullOrEmpty(obj.Sound) ? "default" : obj.Sound,
					tag = string.IsNullOrEmpty(obj.Tag) ? rdm.Next(111111, 999999).ToString() : obj.Tag,
					color = string.IsNullOrEmpty(obj.Color) ? "#0086C9" : obj.Color,
					click_action = string.IsNullOrEmpty(obj.Action) ? "" : obj.Action
				}
			};

			dynamic serializer = new JavaScriptSerializer();
			dynamic json = serializer.Serialize(data);
			Byte[] byteArray = Encoding.UTF8.GetBytes(json);

			WebRequest tRequest = WebRequest.Create("https://fcm.googleapis.com/fcm/send");
			tRequest.Method = "POST";
			tRequest.ContentType = "application/json";
			tRequest.Headers.Add(HttpRequestHeader.Authorization, "key=" + applicationID);
			tRequest.Headers.Add("Sender", "id=" + senderId);
			tRequest.ContentLength = byteArray.Length;

			string str = "";
			using (Stream dataStream = tRequest.GetRequestStream()) {
				dataStream.Write(byteArray, 0, byteArray.Length);
				using (WebResponse tResponse = tRequest.GetResponse()) {
					using (Stream dataStreamResponse = tResponse.GetResponseStream()) {
						using (StreamReader tReader = new StreamReader(dataStreamResponse)) {
							String sResponseFromServer = tReader.ReadToEnd();
							str = sResponseFromServer;
						}
					}
				}
			}

			return true;
		} catch (Exception ex) {
			error = ex.Message;
			return false;
		}
	}
}
```
