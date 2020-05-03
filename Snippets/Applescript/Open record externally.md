# Open record externally

```applescript
tell application "DEVONthink 3"
	set theRecord to get record with uuid ""
	set thePath to (path of theRecord as string)
	do shell script "open " & quoted form of thePath
end tell
```


#Snippets #DEVONthink