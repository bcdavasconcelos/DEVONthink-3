# JSON Handler for theRecord

```applescript

set theString to my json_with_theRecord(theRecord, theSubtitle)

on json_with_theRecord(theRecord, theSubtitle)
	try
		tell application id "DNtp"
			set theName to name of theRecord -- preparing the display info
			set theName to my replaceText(theName, "\"", "")
			
			set theArg to the uuid of theRecord -- preparing the argument
			
			set theString to my json_string(theName, theSubtitle, theArg)
			return theString
		end tell
	end try
end json_with_theRecord

on json_string(theName, theSubtitle, theArg)
	set theString to "{
					        \"title\": \"" & theName & "\",
					        \"subtitle\": \"" & theSubtitle & "\",
					        \"arg\":  \"" & theArg & "\",
					        },"
	return theString
end json_string


```
