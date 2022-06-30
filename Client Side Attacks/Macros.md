## Word VS Exce
VBA rimane uguale ma il nome delle funzioni cambia, in particolare "AutoOpen" e' diverso per Excel.

Seguento [https://gist.github.com/mgeeky/9dee0ac86c65cdd9cb5a2f64cef51991](Questa compilation di revshell macro) vediamo le varie funzioni:

**Per Word**
```vba
Sub AutoOpen()
    ' Becomes launched as first on MS Word
    Launch
End Sub

Sub Document_Open()
    ' Becomes launched as second, another try, on MS Word
    Launch
End Sub
```


**Per Excel**
```vba
Sub Auto_Open()
    ' Becomes launched as first on MS Excel
    Launch
End Sub

Sub Workbook_Open()
    ' Becomes launched as second, another try, on MS Excel
    Launch
End Sub
```


Ottimo generatore:
https://github.com/glowbase/macro_reverse_shell