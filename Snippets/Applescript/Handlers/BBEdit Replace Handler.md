# BBEdit Replace Handler


```applescript
bbReplaceText("[[:blank:]]+", "")

-------------------
--» HANDLERS
-------------------
on bbReplaceText(findPattern, replacePattern)
   tell application "BBEdit"
      if (contents of the selection) ≠ "" then
         tell front text window's its text of selection
            replace findPattern using replacePattern options {search mode:grep, case sensitive:false}
         end tell
      else
         tell front text window's its text
            replace findPattern using replacePattern options {search mode:grep, case sensitive:false, starting at top:true}
         end tell
      end if
   end tell
end bbReplaceText
-------------------------------------------------------------------------------------------

```
