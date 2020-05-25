# JSON Handler for tabs and windows

```applescript

on json_string(theName, theSubtitle, theArg)
	set theString to "{
					        \"title\": \"" & theName & "\",
					        \"subtitle\": \"" & theSubtitle & "\",
					        \"arg\":  \"" & theArg & "\",
					        },"
	return theString
end json_string

on complete_json_string(theStrings)
	set CompleteString to ""
	set CompleteString to "{\"items\": [" & return & theStrings & "]}" -- adding the beggining and end of the string
	set CompleteString to my replaceText(CompleteString, "," & return & "]}", return & "]}") -- removal of the very last comma
end complete_json_string

on replaceText(theString, old, new)
	set {TID, text item delimiters} to {text item delimiters, old}
	set theStringItems to text items of theString
	set text item delimiters to new
	set theString to theStringItems as text
	set text item delimiters to TID
	return theString
end replaceText


```
