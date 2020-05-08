# Filter think windows (for Alfred Workflow)

```applescript

-- Filter think windows (for Alfred Workflow)
-- 2020-05-07-14-50-03
-- 2020-05-07-23-53-04 Added the index alongside the class of the window in the subtitles
-- 2020-05-08-15-40-09 Removed the `\"uid\": \"" & FullID & "\",` line so that DEVONthink manages the sort order and not Alfred.

on run
	
	global theWins, theWin, WinID, theName, theString, theStrings, CompleteString
	
	tell application id "DNtp"
		
		set theStrings to {}
		set theWins to think windows -- get all viewer windows
		
		repeat with theWin in theWins -- the loop
			
			set WinID to the id of theWin
			set theSubtitle to (class of theWin as text)
			set theName to name of theWin -- preparing the display info
			if theSubtitle is "document window" then set the theName to my change_case(theName, "upper")
			set theSubtitle to theSubtitle & space & (index of theWin) -- preparing the display info
			set theName to my replaceText(theName, "\"", "")
			
			set theString to "{
			       \"title\": \"" & theName & "\",
			       \"subtitle\": \"" & theSubtitle & "\",
			       \"arg\":  \"" & WinID & "\"
			       },"
			
			set theStrings to theStrings & theString & return
			
		end repeat
		
		
		set CompleteString to "{\"items\": [" & return & theStrings & "]}" -- adding the beggining and end of the string
		
		set CompleteString to my replaceText(CompleteString, "," & return & "]}", return & "]}") -- removal of the very last comma
		
		set CompleteString to my replaceText(CompleteString, "document window", "doc win")
		set CompleteString to my replaceText(CompleteString, "viewer window", "vi win")
		
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

-- AppleScript 'change_case' handler
on change_case(this_text, this_case)
	if this_case is "lower" then
		set the comparison_string to "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
		set the source_string to "abcdefghijklmnopqrstuvwxyz"
	else
		set the comparison_string to "abcdefghijklmnopqrstuvwxyz"
		set the source_string to "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
	end if
	set the new_text to ""
	repeat with thisChar in this_text
		set x to the offset of thisChar in the comparison_string
		if x is not 0 then
			set the new_text to (the new_text & character x of the source_string) as string
		else
			set the new_text to (the new_text & thisChar) as string
		end if
	end repeat
	return the new_text
end change_case



```


#Applescript #DEVONthink #Alfred