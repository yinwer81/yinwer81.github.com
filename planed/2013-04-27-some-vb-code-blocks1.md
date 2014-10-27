---
layout: post
title: "Some VB Code Blocks(1)..."
---

{% highlight vbnet %}
'block read
Dim countRows, countCols, codes
'Sheet("DATA") countRows
countRows = Worksheets("DATA").UsedRange.Rows.count
'Sheet("DATA") countCols
countCols = Worksheets("DATA").UsedRange.Columns.count
'Sheet("DATA") read countRows and countCols into codes start from A1
codes = Worksheets("DATA").Range("A1").Resize(countRows, countCols).Value

'block write
Dim matrix
ReDim matrix(1 To countRows, 1 To countCols)'countRows, countCols
'Sheet("Report") write matrix into countRows and countCols start from B3
Worksheets("Report").Range("B3").Resize(countRows, countCols) = matrix
{% endhighlight %}

{% highlight vbnet %}
'ex: 1->A, 2->B, ... 27->AA
Function NUM2APH(intx)
  Dim modX As Integer
  modX = intx \ 27
  If modX >= 1 Then
    NUM2APH = Chr(modX + 64) & Chr(intx - modX * 26 + 64)
  Else
    NUM2APH = Chr(intx + 64)
  End If
End Function
{% endhighlight %}

{% highlight vbnet %}
'regexp.test
Function ExpTest(str As String, strPattern As String, _
                				 blnIgnoreCase As Boolean) As Boolean
  Dim regEX As Object
  Set regEX = CreateObject("VBSCRIPT.REGEXP")
  regEX.Global = True
  regEX.Pattern = strPattern
  regEX.ignoreCase = blnIgnoreCase
  ExpTest = regEX.Test(str)
  Set regEX = Nothing
End Function
{% endhighlight %}

{% highlight vbnet %}
'Return Timed(yyyyMMdd) GUID(Globally Unique Identifier) without "-"
Public Function getTimedGUID() As String
  Dim guidStr 
  guidStr = Mid(CreateObject("Scriptlet.TypeLib").GUID, 2, 36)
  getTimedGUID = Format(Date, "yyyyMMdd") + Replace(guidStr, "-", "")
End Function
{% endhighlight %}

{% highlight vbnet %}
'dictionary ops
Function dictOPs()
  Dim dict, i
  Set dict = CreateObject("Scripting.Dictionary")
  For i = 1 To 100
    dict.Add "K:" + CStr(i), "V:" + CStr(100 - i)
  Next
  
  Dim k, v, Key, Value 
  k = dict.keys
  v = dict.Items
  For i = 0 To dict.count - 1
    Key = k(i)
    Value = v(i) 'dict.Item(Key)
    Debug.Print Key & "=" & Value
  Next
End Function
{% endhighlight %}