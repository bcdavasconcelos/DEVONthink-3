# How many x in y

```applescript

set theText to "## The Title"

set howMany to (count my decoupe(theText, "#")) - 1

on decoupe(t, d)
	local oTIDs, l
	set {oTIDs, AppleScript's text item delimiters} to {AppleScript's text item delimiters, d}
	set l to text items of t
	set AppleScript's text item delimiters to oTIDs
	return l
end decoupe


```
