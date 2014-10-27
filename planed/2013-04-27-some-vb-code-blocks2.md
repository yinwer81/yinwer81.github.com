---
layout: post
title: "Some VB Code Blocks(2)..."
---

{% highlight vbnet %}
'Data Driven Chart, contiguous data blocks

Sheets(sheetName).Activate
'existing chart: EVMChart
ActiveSheet.ChartObjects("EVMChart").Activate
'Source: Sheet("DATA").Range("A4-D/weekno"),
'X-axis: A column/Y-axis: BCD, 3 SeriesCollections displayed
ActiveChart.SetSourceData Source:=Range("'DATA'!$A$4:$D$" & weekno)
'A4:B/weekno
ActiveChart.SeriesCollection(1).Formula = 
"=SERIES(""PV"",'DATA'!$A$4:$A$" & weekno & ",'DATA'!$B$4:$B$" & weekno & ",1)"
'A4:C/weekno
ActiveChart.SeriesCollection(2).Formula =
"=SERIES(""AC"",'DATA'!$A$4:$A$" & weekno & ",'DATA'!$C$4:$C$" & weekno & ",2)"
'A4:D/weekno
ActiveChart.SeriesCollection(3).Formula =
"=SERIES(""EV"",'DATA'!$A$4:$A$" & weekno & ",'DATA'!$D$4:$D$" & weekno & ",3)"

'Data Driven Chart, noncontinuous data blocks

'existing chart: RISKChart
ActiveSheet.ChartObjects("RISKChart").Activate
'Source: Sheet("DATA") A4-A/weekno and E4-E/weekno
'X-axis: A column/Y-axis: E column, 1 SeriesCollection displayed
Dim RSK As Range
RSK1 = Sheets("DATA").Range("$A$4:$A$" & weekno)
RSK2 = Sheets("DATA").Range("$E$4:$E$" & weekno)
Set RSK = Application.Union(RSK1, RSK2)
ActiveChart.SetSourceData Source:=RSK

ActiveChart.SeriesCollection(1).Formula = 
"=SERIES(""Risk"",'DATA'!$A$4:$A$" & weekno & ",'DATA'!$E$4:$E$" & weekno & ",1)"

ActiveSheet.Range("A1").Select
'PS: EXCEL keeps SeriesCollections as many as Y-axises by default.


'deal with Excel Sheet using ADO
Sub DoWhoWant()
  Dim deptStr As String
  sheetName = "Sheet1"
  deptStr = Worksheets(sheetName).Range("B2")
    
  Dim conn As Object
  Set conn = getConn
    
  Dim dateStr1 As String, dateStr2 As String
  dateStr2 = "2013/08/31"
  dateStr1 = Left(dateStr2, InStrRev(dateStr2, "/")) & "1"
  dateStr2 = "#" & dateStr2 & "#"
  dateStr1 = "#" & dateStr1 & "#"
        
  Dim linesStr
  linesStr = getLines(conn, "DATA", deptStr, dateStr1, dateStr2)
    
  conn.Close
  Set conn = Nothing
    
  MsgBox "Done!"

End Sub

'get connection from OLEDB
Function getConn() As Object
  Dim conn As Object
  Set conn = CreateObject("ADODB.Connection")
    
  With conn
    .Provider = "Microsoft.ACE.OLEDB.12.0"
    .Properties("Extended Properties").Value = "Excel 12.0 Macro;HDR=YES;"
    .Open "Data Source = D:\costdetails.xlsm"
  End With
  Set getConn = conn
End Function

'get recordset from connection and sql
Function getRs(conn, sql) As Object
  Dim rs As Object
  Set rs = CreateObject("ADODB.Recordset")
    
  With rs
    .Source = sql
    .ActiveConnection = conn
    .CursorLocation = adUseClient
    .CursorType = adOpenForwardOnly
    .LockType = adLockReadOnly
    .Open
  End With
  Set getRs = rs
End Function

Function getLines(conn, sheetName, deptStr, dateStr1, dateStr2)
  Dim sSQL As String
  sSQL = "SELECT [行程], Count([行程]) FROM [" & sheetName & "$] "
  sSQl = sSQL + "WHERE [客户部门]='" & deptStr & "' "
  sSQl = sSQL + "AND [出票日期]>=" & dateStr1 & " AND [出票日期]<=" & dateStr2 
  sSQl = sSQL + " group by [行程] order by Count([行程]) desc"
  '"SELECT * FROM [Sheet1$A1:B10]"
    
  Dim rs As Object
  Set rs = getRs(conn, sSQL)
    
  Dim result As String, i As Integer
  result = ""
  While Not rs.EOF And i <= 2
    result = result & rs.Fields(0).Value & "(" & rs.Fields(1).Value & ")" & vbCrLf
    i = i + 1
    rs.MoveNext
  Wend
    
  rs.Close
  Set rs = Nothing
    
  getLines = result
    
End Function
{% endhighlight %}