<div align="center">

## ShowScreensaverpwd


</div>

### Description

This source code show how simple it is to crack the windows screensaver password!

NEW! I've fixed some Bugs...
 
### More Info
 
Just start the form.

Just copy the code below and past it into the notepad. Save it as crackpwd.bas (Windows API) and crackpwd.frm! Then add it to a new projekt and start it.

It shows the password with the Print command...


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Sebastian Fahrenkrog](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/sebastian-fahrenkrog.md)
**Level**          |Unknown
**User Rating**    |4.3 (13 globes from 3 users)
**Compatibility**  |VB 4\.0 \(32\-bit\), VB 5\.0, VB 6\.0
**Category**       |[Registry](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/registry__1-36.md)
**World**          |[Visual Basic](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/visual-basic.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/sebastian-fahrenkrog-showscreensaverpwd__1-2360/archive/master.zip)

### API Declarations

```
'Save it as crackpwd.bas
'----------------8< Cut here -----------------------------------------
Attribute VB_Name = "Module1"
Public Const READ_CONTROL = &H20000
Public Const STANDARD_RIGHTS_READ = (READ_CONTROL)
Public Const STANDARD_RIGHTS_WRITE = (READ_CONTROL)
Public Const KEY_QUERY_VALUE = &H1
Public Const KEY_SET_VALUE = &H2
Public Const KEY_CREATE_SUB_KEY = &H4
Public Const KEY_ENUMERATE_SUB_KEYS = &H8
Public Const KEY_NOTIFY = &H10
Public Const KEY_CREATE_LINK = &H20
Public Const SYNCHRONIZE = &H100000
Public Const STANDARD_RIGHTS_ALL = &H1F0000
Public Const KEY_READ = ((STANDARD_RIGHTS_READ Or _
 KEY_QUERY_VALUE Or KEY_ENUMERATE_SUB_KEYS Or KEY_NOTIFY) _
 And (Not SYNCHRONIZE))
Public Const KEY_WRITE = ((STANDARD_RIGHTS_WRITE Or _
 KEY_SET_VALUE Or KEY_CREATE_SUB_KEY) And (Not SYNCHRONIZE))
Public Const KEY_ALL_ACCESS = ((STANDARD_RIGHTS_ALL Or _
 KEY_QUERY_VALUE Or KEY_SET_VALUE Or KEY_CREATE_SUB_KEY _
 Or KEY_ENUMERATE_SUB_KEYS Or KEY_NOTIFY Or KEY_CREATE_LINK) _
 And (Not SYNCHRONIZE))
Public Const KEY_EXECUTE = ((KEY_READ) And (Not SYNCHRONIZE))
Public Const ERROR_SUCCESS = 0&
Declare Function RegOpenKeyEx Lib "advapi32.dll" _
 Alias "RegOpenKeyExA" (ByVal hKey As Long, _
 ByVal lpSubKey As String, ByVal ulOptions As Long, _
 ByVal samDesired As Long, phkResult As Long) As Long
Declare Function RegQueryValueEx Lib "advapi32.dll" _
 Alias "RegQueryValueExA" (ByVal hKey As Long, _
 ByVal lpValueName As String, ByVal lpReserved As Long, _
 lpType As Long, lpData As Any, lpcbData As Long) As Long
Declare Function RegCloseKey Lib "advapi32.dll" _
 (ByVal hKey As Long) As Long
'Module Function-------------------------------------------------------------------------------
Function sdaGetRegEntry(strKey As String, _
 strSubKeys As String, strValName As String, _
 lngType As Long) As String
'* Demonstration of win32 API's to query
' the system registry
' Stu Alderman -- 2/30/96
On Error GoTo sdaGetRegEntry_Err
 Dim lngResult As Long, lngKey As Long
 Dim lngHandle As Long, lngcbData As Long
 Dim strRet As String
 Select Case strKey
 Case "HKEY_CLASSES_ROOT": lngKey = &H80000000
 Case "HKEY_CURRENT_CONFIG": lngKey = &H80000005
 Case "HKEY_CURRENT_USER": lngKey = &H80000001
 Case "HKEY_DYN_DATA": lngKey = &H80000006
 Case "HKEY_LOCAL_MACHINE": lngKey = &H80000002
 Case "HKEY_PERFORMANCE_DATA": lngKey = &H80000004
 Case "HKEY_USERS": lngKey = &H80000003
 Case Else: Exit Function
 End Select
 If Not ERROR_SUCCESS = RegOpenKeyEx(lngKey, _
  strSubKeys, 0&, KEY_READ, _
  lngHandle) Then Exit Function
 lngResult = RegQueryValueEx(lngHandle, strValName, _
  0&, lngType, ByVal strRet, lngcbData)
 strRet = Space(lngcbData)
 lngResult = RegQueryValueEx(lngHandle, strValName, _
  0&, lngType, ByVal strRet, lngcbData)
 If Not ERROR_SUCCESS = RegCloseKey(lngHandle) Then _
  lngType = -1&
 sdaGetRegEntry = strRet
sdaGetRegEntry_Exit:
 On Error GoTo 0
 Exit Function
sdaGetRegEntry_Err:
 lngType = -1&
 MsgBox Err & "> " & Error$, 16, _
  "GenUtils/sdaGetRegEntry"
 Resume sdaGetRegEntry_Exit
End Function
'End Function
```


### Source Code

```
'Save it as crackpwd.frm, add crackpwd.bas (the code above)
'and compile it!
'-------------- 8< Cut here----------------------------------------------------
VERSION 5.00
Begin VB.Form Form1
  BackColor    =  &H00000000&
  BorderStyle   =  4 'Festes Werkzeugfenster
  Caption     =  "Password Cracker"
  ClientHeight  =  4905
  ClientLeft   =  45
  ClientTop    =  300
  ClientWidth   =  6855
  ForeColor    =  &H00FFFFFF&
  LinkTopic    =  "Form1"
  MaxButton    =  0  'False
  MinButton    =  0  'False
  ScaleHeight   =  4905
  ScaleWidth   =  6855
  ShowInTaskbar  =  0  'False
  StartUpPosition =  3 'Windows-Standard
End
Attribute VB_Name = "Form1"
Attribute VB_GlobalNameSpace = False
Attribute VB_Creatable = False
Attribute VB_PredeclaredId = True
Attribute VB_Exposed = False
Private Sub Form_Load()
Me.Show
Print
Print "Read Registry..."
Print
Print "Screensaver Password: " + Screensavepwd
End Sub
Function Screensavepwd() As String
'Dim's for the Registry
Dim lngType As Long, varRetString As Variant
Dim lngI As Long, intChar As Integer
'Dim's for the Password decoding
Dim Ciphertext As String, Key As String
Dim temp1 As String, temp2 As String
'Registry Path to the encrypted Password
varRetString = sdaGetRegEntry("HKEY_CURRENT_USER", _
  "Control Panel\desktop", "ScreenSave_Data", "1")
'the Encrypted Password
Ciphertext = varRetString
If Len(Ciphertext) <> 1 Then
Ciphertext = Left$(varRetString, Len(Ciphertext) - 1)
Print Ciphertext
'Micro$oft's "Secret" Key
Key = "48EE761D6769A11B7A8C47F85495975F414141"
'XOR every Ciphertextbyte with the Keybyte to get
'the plaintext
For i = 1 To Len(Ciphertext) Step 2
temp1 = Hex2Dez(Mid$(Ciphertext, i, 2))
temp2 = Hex2Dez(Mid$(Key, i, 2))
plaintext = plaintext + Chr(temp1 Xor temp2)
Next i
Screensavepwd = plaintext
Else
Screensavepwd = " no Password"
End If
End Function
Function Hex2Dez&(H$)
If Left$(H$, 2) <> "&H" Then
  H$ = "&H" + H$
End If
  Hex2Dez& = Val(H$)
End Function
```

