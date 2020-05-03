# Open extension externally

```applescript
property fileExtension : {".doc", ".docx", ".xls", ".cmap", ".scap", ."webloc", ".ooutline", ".plist"}

repeat with each in fileExtension

if filename of theRecord contains each then
set thePath to (path of theRecord as string)

do shell script "open " & quoted form of thePath

end if
end repeat

```


#Snippets #DEVONthink