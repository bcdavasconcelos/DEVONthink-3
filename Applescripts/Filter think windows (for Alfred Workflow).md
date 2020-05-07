# Filter think windows (for Alfred Workflow)

```applescript

-- 2020-05-07-14-50-03

on run
	
	global theWins, theWin, WinID, theName, theString, theStrings, CompleteString
	
	tell application id "DNtp"
		
		set theStrings to {}
		set theWins to think windows -- get all viewer windows
		
		repeat with theWin in theWins -- the loop
			
			set WinID to the id of theWin
			set theSubtitle to "Select " & class of theWin -- preparing the display info
			set theName to name of theWin -- preparing the display info
			set theName to my replaceText(theName, "\"", "")
			
			set theString to "{
			     \"uid\": \"" & WinID & "\",
			       \"title\": \"" & theName & "\",
			       \"subtitle\": \"" & theSubtitle & "\",
			       \"arg\":  \"" & WinID & "\"
			       },"
			
			set theStrings to theStrings & theString & return
			
		end repeat
		
		
		set CompleteString to "{\"items\": [" & return & theStrings & "]}" -- adding the beggining and end of the string
		
		set CompleteString to my replaceText(CompleteString, "," & return & "]}", return & "]}") -- removal of the very last comma
		
		return CompleteString
		
	end tell
	
end run


on replaceText(theString, old, new)
	set {TID, text item delimiters} to {text item delimiters, old}
	set theStringItems to text items of theString
	set text item delimiters to new
	set theString to theStringItems as text
	set text item delimiters to TID
	return theString
end replaceText

```
