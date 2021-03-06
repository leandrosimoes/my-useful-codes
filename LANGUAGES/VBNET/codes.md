﻿# **VB.NET**
Some VB.NET useful codes:

## Summary

* [HTTL Listener Sample](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/VBNET/codes.md#httplistener-sample)
* [DataTable To Class](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/VBNET/codes.md#datatable-to-class)
* [Class To Datatable](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/VBNET/codes.md#class-to-datatable)
* [Validate CPF](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/VBNET/codes.md#validate-cpf)
* [Validate CNPJ](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/VBNET/codes.md#validate-cnpj)
* [ModelStateDictionay To BaseReturnModel](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/VBNET/codes.md#return-modelstatedictionary-errors-to-basereturnmodel)
* [NameValueCollection To Query String](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/VBNET/codes.md#namevaluecollection-to-query-string)
* [Strip Invalid Chars From String](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/VBNET/codes.md#strip-invalid-chars-from-string)
* [Get Feed From A Website Feed Page](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/VBNET/codes.md#get-feed-from-website-feed-page)
* [Remove Special Chars From String](https://github.com/leandrosimoes/my-useful-codes/blob/master/LANGUAGES/VBNET/codes.md#remove-special-chars-from-string)

----------

### HttpListener sample
##### I have used this code to start a localhost server listener to be able to open a desktop application by browser

```VBNET
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

```VBNET
Public Shared Function DataTableToClass(Of T)(ByVal dt As DataTable, ByVal Optional columnsToExclude() As String = Nothing) As IEnumerable(Of T)
    Dim objType As Type = GetType(T)
    Dim objRetun As New List(Of T)()

    If (IsNothing(dt.Rows) OrElse dt.Rows.Count <= 0) Then
        Return {}
    End If

    For Each row As DataRow In dt.Rows
        Dim obj As Object = Activator.CreateInstance(Of T)
        Dim propInfo() As PropertyInfo = objType.GetProperties()

        For Each col As DataColumn In dt.Columns
            Dim colName As String = col.ColumnName
            Dim prop As PropertyInfo = propInfo.FirstOrDefault(Function(p) p.Name = colName)

            If (Not IsNothing(prop)) Then
                If (IsNothing(columnsToExclude) OrElse columnsToExclude.All(Function(e) e <> colName)) Then
                    Dim propType As Type = obj.GetType().GetProperty(colName).PropertyType

                    If (Not IsNothing(row(colName)) AndAlso Not IsDBNull(row(colName))) Then
                        If (propType = GetType(Boolean)) Then
                            Dim value As Boolean = False

                            Try
                                Dim propValue As String = row(colName).ToString().ToLower()
                                value = (propValue = "1" OrElse propValue = "true")
                            Catch ex As Exception
                                value = False
                            End Try

                            obj.GetType().GetProperty(colName).SetValue(obj, value, Nothing)
                        Else
                            obj.GetType().GetProperty(colName).SetValue(obj, row(colName), Nothing)
                        End If

                    End If
                End If
            End If
        Next

        objRetun.Add(obj)
    Next

    Return objRetun.AsEnumerable()
End Function
```

----------

### Class to DataTable
##### Inverse of the method above.

```VBNET
Public Shared Function ClassToDataTable(Of T)(items As List(Of T), ByVal Optional propsToExclude() As String = Nothing) As DataTable
    Dim dt = New DataTable(GetType(T).Name)

    Dim props As PropertyInfo() = GetType(T).GetProperties()

    For Each prop As PropertyInfo In props
        If (IsNothing(propsToExclude) OrElse propsToExclude.All(Function(p) p <> prop.Name)) Then
            Dim type As Type

            'DataTable do not accept nullable properties, so if is nullable, create with the real type
            If (prop.PropertyType.IsGenericType AndAlso prop.PropertyType.GetGenericTypeDefinition = GetType(System.Nullable(Of ))) Then
                type = prop.PropertyType.GetGenericArguments()(0)
            Else
                type = prop.PropertyType
            End If

            dt.Columns.Add(prop.Name, type)
        End If
    Next

    If (Not items.Any()) Then
        Return dt
    End If

    For Each item As T In items
        Dim values = New Object(props.Length - 1) {}
        For i As Integer = 0 To props.Length - 1
            If (IsNothing(propsToExclude) OrElse propsToExclude.All(Function(p) p <> props(i).Name)) Then
                values(i) = props(i).GetValue(item, Nothing)
            End If
        Next

        dt.Rows.Add(values)
    Next

    Return dt
End Function
```

----------

### Validate CPF
##### Used to validate CPF (CPF is a personal ID in Brazil).

```VBNET
Public Shared Function ValidaCPF(ByVal CPF As String) As Boolean
    Dim blackList() As String = {"00.000.000-00", "111.111.111-11", "222.222.222-22", "333.333.333-33", "444.444.444-44", "555.555.555-55", "666.666.666-66", "777.777.777-77", "888.888.888-88", "999.999.999-99"}
    Dim i, x, n1, n2 As Integer

    CPF = CPF.Trim
    For i = 0 To blackList.Length - 1
        If CPF.Length <> 14 Or blackList(i).Equals(CPF) Then
            Return False
        End If
    Next

    CPF = CPF.Substring(0, 3) + CPF.Substring(4, 3) + CPF.Substring(8, 3) + CPF.Substring(12)
    For x = 0 To 1
        n1 = 0
        For i = 0 To 8 + x
            n1 = n1 + Val(CPF.Substring(i, 1)) * (10 + x - i)
        Next
        n2 = 11 - (n1 - (Int(n1 / 11) * 11))
        If n2 = 10 Or n2 = 11 Then n2 = 0

        If n2 <> Val(CPF.Substring(9 + x, 1)) Then
            Return False
        End If
    Next

    Return True
End Function
```

----------


### Validate CNPJ
##### Used to validate CNPJ (CNPJ is a company ID in Brazil).

```VBNET
Public Shared Function ValidaCNPJ(ByVal CNPJ As String) As Boolean
    Dim blackList() As String = {"00.000.000/0000-00", "11.111.111/1111-11", "22.222.222/2222-22", "33.333.333/3333-33", "44.444.444/4444-44", "55.555.555/5555-55", "66.666.666/6666-66", "77.777.777/7777-77", "88.888.888/8888-88", "99.999.999/9999-99"}

    Dim i As Integer
    Dim valida As Boolean
    CNPJ = CNPJ.Trim

    For i = 0 To blackList.Length - 1
        If CNPJ.Length <> 18 Or blackList(i).Equals(CNPJ) Then
            Return False
        End If
    Next

    CNPJ = CNPJ.Substring(0, 2) + CNPJ.Substring(3, 3) + CNPJ.Substring(7, 3) + CNPJ.Substring(11, 4) + CNPJ.Substring(16)
    valida = Validate(CNPJ)

    If valida Then
        ValidaCNPJ = True
    Else
        ValidaCNPJ = False
    End If

End Function

Private Shared Function Validate(ByVal cnpj As String)
    Dim Numero(13) As Integer
    Dim soma As Integer
    Dim i As Integer
    Dim valida As Boolean
    Dim resultado1 As Integer
    Dim resultado2 As Integer
    For i = 0 To Numero.Length - 1
        Numero(i) = CInt(cnpj.Substring(i, 1))
    Next
    soma = Numero(0) * 5 + Numero(1) * 4 + Numero(2) * 3 + Numero(3) * 2 + Numero(4) * 9 + Numero(5) * 8 + Numero(6) * 7 +
               Numero(7) * 6 + Numero(8) * 5 + Numero(9) * 4 + Numero(10) * 3 + Numero(11) * 2
    soma = soma - (11 * (Int(soma / 11)))

    resultado1 = If(soma = 0 Or soma = 1, 0, 11 - soma)

    If resultado1 = Numero(12) Then
        soma = Numero(0) * 6 + Numero(1) * 5 + Numero(2) * 4 + Numero(3) * 3 + Numero(4) * 2 + Numero(5) * 9 + Numero(6) * 8 +
                     Numero(7) * 7 + Numero(8) * 6 + Numero(9) * 5 + Numero(10) * 4 + Numero(11) * 3 + Numero(12) * 2
        soma = soma - (11 * (Int(soma / 11)))
        If soma = 0 Or soma = 1 Then
            resultado2 = 0
        Else
            resultado2 = 11 - soma
        End If
        If resultado2 = Numero(13) Then
            Return True
        Else
            Return False
        End If
    Else
        Return False
    End If

End Function
```

----------

### Return ModelStateDictionary Errors to BaseReturnModel
##### Used to parse the ModelStateDictionary errors to a BaseReturnModel

```VBNET
'This is my model that I use to return success or errors
Public Class BaseReturnModel
    Public Property id As Integer

    Public Property success As Boolean
        Get
            Return IsNothing(errors) OrElse Not errors.Any()
        End Get
        Set(value As Boolean)
        End Set
    End Property

    Public Property errors As New List(Of BaseErrorModel)()
End Class

'This is the error model
Public Class BaseErrorModel
    Public Property Message As String
End Class

'This function get all ModelStateDictionary errors and put them on my BaseReturnModel
Public Shared Function GetModelErrors(modelState As ModelStateDictionary) As BaseReturnModel
    Dim retorno As New BaseReturnModel()

    For Each ms As ModelState In modelState.Values
        For Each modelError As ModelError In ms.Errors
            retorno.errors.Add(New BaseErrorModel With {
                .Message = modelError.ErrorMessage
            })
        Next
    Next

    Return retorno
End Function

'Then I use like this
Public Function MyAction() As ActionResult
    Dim ret As New JsonNetResult
    ret.ContentType = "application/json; charset=utf-8"
    ret.JsonRequestBehavior = JsonRequestBehavior.AllowGet

    If (Not ModelState.IsValid) Then
        ret.Data = GetModelErrors(ModelState)
        Return retorno
    End If
End Function
```

----------

### NameValueCollection to query string
##### Transform a NameValueCollection into a encoded query string

```VBNET
Public Function ToQueryString(ByVal parameters As NameValueCollection) As String
    Dim items = New List(Of String)()

    For Each name As String In parameters
        items.Add(String.Concat(name, "=", Web.HttpUtility.UrlEncode(parameters(name))))
    Next

    Return String.Join("&amp;", items.ToArray())
End Function
```

----------

### Strip invalid chars from string
##### Sometimes when I parse a text file with UTF8 encoding, some invisible chars are added to parts of string, so I have to do this to avoid this issue

```VBNET
Private Function CleanString(strIn As String) As String
    ' Replace invalid characters with empty strings.
    Try
       Return Regex.Replace(strIn, "[^\w\.@-]", "")
    ' If we timeout when replacing invalid characters, 
    ' we should return String.Empty.
    Catch e As Exception
       Return String.Empty         
    End Try
End Function
```

Reference: [#1](https://msdn.microsoft.com/en-us/library/844skk0h%28v=vs.110%29.aspx?f=255&MSPPError=-2147217396)

----------

### Get feed from website feed page
##### Days ago I have to retrieve some posts from an Wordpress feed, so I did this

```VBNET
Dim reader As Xml.XmlReader = Xml.XmlReader.Create("http://netspeed.com.br/mais/blog/feed/")
Dim feed As SyndicationFeed = SyndicationFeed.Load(reader)

'Than just get feed.Items that is an IEnumerable(Of SyndicationItem) and do what you want
```

----------

### Remove special chars from string
##### Well, this is it

```VBNET
Public Shared Function RemoveSpecialChars(ByVal text As String) As String
    text = text.Trim()
    
    Dim newText As String = ""
    Dim specials = "ÀÁÂÃÄÅÇÈÉÊËÌÍÎÏÒÓÔÕÖÙÚÛÜàáâãäåçèéêëìíîïòóôõöùúûü"
    Dim noSpecials = "AAAAAACEEEEIIIIOOOOOUUUUaaaaaaceeeeiiiiooooouuuu"

    For Each c In text
        If (specials.Contains(c)) Then
            c = noSpecials(specials.IndexOf(c))
        End If

        If ((c >= "0" And c <= "9") Or (c >= "A" And c <= "Z") Or (c >= "a" And c <= "z") Or c = "." Or c = "_" Or c = "@") Then
            newText = newText & c
        End If
    Next

    Return newText
End Function
```

----------

