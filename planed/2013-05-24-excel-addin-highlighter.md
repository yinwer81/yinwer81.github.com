---
layout: post
title: "EXCEL加载项 highlighter"
---

有人每天多数时间和EXCEL打交道, 表格样式和行列数字, 时间长压力大, 于是头晕眼眩, 低级出错不少。心惜之。<br>
于是有了本案加载项(highlighter.xla): 鼠标点到哪里, 高亮表格对应的行和列。<br>

希望对大家都有用, 这里共享出来, 下载: [戳这里](/downloads/highlighter.xla), EXCEL2007环境测试通过。<br>

相较网上同类, 窃以为处理好了以下问题:<br>
1.	对于每个Workbook每个Sheet, 不会无限制添加条件格式项<br>
2.	不影响EXCEL中既有条件格式项<br>

代码ClsHighlighter:
{% highlight vbnet %}
Option Explicit

Private WithEvents highlighter As Excel.Application
Private fcsDict As Object

Private Sub Class_Initialize()
  On Error Resume Next
  Set highlighter = Application
  Set fcsDict = CreateObject("Scripting.Dictionary")
  fcsDict.CompareMode = vbTextCompare
End Sub

Private Sub Class_Terminate()
  Set highlighter = Nothing
  Set fcsDict = Nothing
End Sub

Private Sub highlighter_SheetSelectionChange(ByVal obj As Object, 
						          		 ByVal Target As Range)
  On Error Resume Next
  Dim currROW, currCOL, sheetName, bookName, dictKey, FCS
  currROW = Target.Row
  currCOL = Target.Column
    
  If Target.Cells.Count > 1 Then
    Exit Sub
  End If
    
  'Organize FormatConditions with bookName and sheetName
  bookName = Target.Application.Caption
  sheetName = Target.Application.ActiveSheet.Name
  dictKey = bookName & "," & sheetName
  If Not fcsDict.exists(dictKey) Then
    fcsDict.Add dictKey, "1"
    FCS = highlighter.ActiveSheet.Cells.FormatConditions.Count
    With highlighter.ActiveSheet.Cells
      'Column FormatConditions
      .FormatConditions.Add xlExpression, Formula1:="=COLUMN()=CELL(""col"")"
      .FormatConditions(FCS + 1).Interior.ColorIndex = 15
      'Row FormatConditions
      .FormatConditions.Add xlExpression, Formula1:="=ROW()=CELL(""row"")"
      .FormatConditions(FCS + 1 + 1).Interior.ColorIndex = 15
    End With
  End If
    
  'ScreenUpdating
  Range(NUM2APH(currCOL) & currROW).Activate
  highlighter.ScreenUpdating = True
    
End Sub

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

代码modHighlighter:
{% highlight vbnet %}
Option Explicit

Public highlighter As ClsHighlighter

Public Sub Auto_Open()
  Set highlighter = New ClsHighlighter
End Sub
{% endhighlight %}

安装方法:<br>
将highlighter.xla放到合适位置, 打开EXCEL, EXCEL选项->加载项->转到EXCEL加载项
在加载宏窗口中通过浏览并勾选highlighter

卸载方法:<br>
EXCEL选项->加载项->转到EXCEL加载项
在加载宏窗口中取消勾选highlighter