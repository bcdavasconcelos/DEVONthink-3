# Get contents of database

```applescript

tell application "DEVONthink Pro"
	set theFiles to {}
	set theContents to contents of current database
	repeat with theContent in theContents
		set thePath to path of theContent
		if thePath is not "" then set theFiles to theFiles & thePath
	end repeat
end tell

```
