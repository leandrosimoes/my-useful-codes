# **VB.NET**
Some VB.NET useful codes:

### HttpListener sample
##### I have used this code to start a localhost server listener to be able to open a desktop application by browser

```VB
Imports System.IO
Imports System.Net
Imports System.Text
Imports System.Web.Script.Serialization

Module Module
    Dim resultMsg As String = ""
    Dim result As IAsyncResult    

    Sub Main()
        'Windows XP SP2 or Server 2003 is required to use the HttpListener class.
        If Not HttpListener.IsSupported Then
            Return
        End If

        ' Create a listener.
        Dim listener As New HttpListener()

        ' Add the prefixes.
        'listener.Prefixes.Add("http://localhost:8888/")
        listener.Prefixes.Add("http://*:8888/")
        listener.Start()

        While True
            'Listening...
            result = listener.BeginGetContext(New AsyncCallback(AddressOf ListenerCallback), listener)
            result.AsyncWaitHandle.WaitOne() 'Waiting for request to be processed asyncronously.
        End While
    End Sub

    Public Sub ListenerCallback(result As IAsyncResult)
        Dim listener As HttpListener = DirectCast(result.AsyncState, HttpListener)
        Dim context As HttpListenerContext = listener.EndGetContext(result)
        Dim request As HttpListenerRequest = context.Request

        resultMsg = New String(request.RawUrl.ToCharArray())

        Dim response As HttpListenerResponse = context.Response
        Dim returnString As String
        Dim param1 As String = ""
        Dim param2 As String = ""
        Dim errorMessage As String = ""
        Dim callback As String = request.QueryString.Get(NameOf(callback))

        If String.IsNullOrEmpty(callback) Then
            'Return an HTML string if not have a callback
            response.ContentType = "text/html"
            returnString = "Hello <b>World!</b>"
        Else
            'If have a callback function, set a object to be returned as a json string
            Dim retorno As New Dictionary(Of String, String)
            retorno.Add(NameOf(param1), param1)
            retorno.Add(NameOf(param2), param2)

            Dim sb As New StringBuilder()
            Dim js As New JavaScriptSerializer()
            sb.Append(callback & "(")
            sb.Append(js.Serialize(retorno))
            sb.Append(");")
            returnString = sb.ToString

            Try
                param1 = request.QueryString.Get(NameOf(param1))
                param2 = request.QueryString.Get(NameOf(param2))

                'Here you can use the values received on querystring to do something
                'In my case I use my vars to open some applications on users computer
                Select Case param1
                    Case "open"
                        Select Case param2
                            Case "notepad"
                                Process.Start("notepad.exe") 'Open notepad

                            Case "calc"
                                Process.Start("calc") 'Open calculator
                        End Select
                End Select

            Catch ex As Exception
                errorMessage = ex.Message
            End Try

            retorno.Add("error", errorMessage)
        End If

        Dim stream As Stream = response.OutputStream
        Dim bytes() As Byte = Encoding.UTF8.GetBytes(returnString)

        response.ContentLength64 = bytes.Length
        stream.Write(bytes, 0, response.ContentLength64)
        stream.Close()
    End Sub
End Module

```

##### Than, you can call the localhost url in the browser or by an ajax request like this:
```javascript
var callback = function(r) {
    console.log(r); //get the return object/string here
};
$.ajax({
    url: 'http://localhost:8888?callback=?',
    type: 'GET',
    data: { param1: 'open', param2: 'notepad' },
    crossDomain: true,
    dataType: 'jsonp',
    contentType: 'application/json; charset=utf-8',
    jsonpCallback: 'callback'
});
```

----------

### DataTable to Class
##### I have used this method to parse a DataTable to any Class type. The Class must have the property names equals to the table columns names.

```VB
Public Shared Function DataTableToClass(Of T)(ByVal dt As DataTable, ByVal obj As T) As IEnumerable(Of T)
    Dim objRetun As New List(Of T)()

    If (IsNothing(dt.Rows) OrElse dt.Rows.Count <= 0) Then
        Return Nothing
    End If

    Dim propInfo() As PropertyInfo = obj.GetType().GetProperties()
    For Each row As DataRow In dt.Rows
        For Each col As DataColumn In dt.Columns
            Dim colName As String = col.ColumnName
            Dim prop As PropertyInfo = propInfo.FirstOrDefault(Function(p) p.Name = colName)

            If (Not IsNothing(prop)) Then
                obj.GetType().GetProperty(colName).SetValue(obj, row(colName))
            End If
        Next

        objRetun.Add(obj)
    Next

    Return objRetun.AsEnumerable()
End Function
```

----------
