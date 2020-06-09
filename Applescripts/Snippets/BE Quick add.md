# BE Quick add


```applescript

on run
tell application "Bookends"
	set pubListitem to the clipboard
	set pubList to quick add {pubListitem}
end tell
end run

```
