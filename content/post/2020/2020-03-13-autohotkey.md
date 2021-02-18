---
title: autohotkey
author: penguin
type: post
date: 2020-03-13T12:00:45+00:00
url: /2020/03/13/autohotkey/
categories:
  - Snippets
tags:
  - autohotkey
  - windows

---
Under macOS I use TextExpander, under Windows there's the fantastic [AutoHotkey.][1] One of the few softwares I can't live without.

This is my default configuration:

```
; ---------- "auto reload" ----------

FileGetTime ScriptStartModTime, %A_ScriptFullPath%
SetTimer CheckReload, 1000, 0x7FFFFFFF ; ms & priority

; from here: https://stackoverflow.com/a/45488494
CheckReload() {
    global ScriptStartModTime
    FileGetTime curModTime, %A_ScriptFullPath%
    If (curModTime &lt;&gt; ScriptStartModTime) {
        Loop
        {
            reload
            Sleep 300 ; ms
            MsgBox 0x2, %A_ScriptName%, Reload failed. ; 0x2 = Abort/Retry/Ignore
            IfMsgBox Abort
                ExitApp
            IfMsgBox Ignore
                break
        } ; loops reload on "Retry"
    }
}


; ---------- actual content here ----------

; removed all my email address shortcuts ...

:*:-dt::
  ; from here: https://is.gd/3u6MKQ
  Send, %A_YYYY%-%A_MM%-%A_DD%
  Return

:*://ts::
  Send, %A_YYYY%%A_MM%%A_DD%_%A_Hour%%A_Min%%A_Sec%
  Return
```

&nbsp;

 [1]: https://www.autohotkey.com/