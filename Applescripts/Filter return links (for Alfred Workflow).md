# Filter return links (for Alfred Workflow)


```applescript
-- Retrieve return links (for Alfred Workflow)
-- 2020-05-07-19-53-21
-- 2020-05-08-15-40-09 Removed the `\"uid\": \"" & FullID & "\",` line so that DEVONthink manages the sort order and not Alfred.

property theSubtitle : "Attach this record to think window"
on run
	
	global WinID, TabID, theRecord, theDB, theAliases, theSearch, theResult, theResults, theResultName, theUUID, theName, theString, theStrings, CompleteString
	
	
	set WinID to ((system attribute "WinID") as number)
	set TabID to ((system attribute "TabID") as number)
	
	tell application id "DNtp"
		
		set theStrings to {}
		set theRecord to content record of (tab id TabID) in (window id WinID)
		--set theRecord to content record of think window 1
		set theDB to the database of theRecord
		set theName to the name of theRecord
		set theAliases to aliases of theRecord
		if theAliases is not "" then
			set theAliases to my trimtext(theAliases, ", ", "end")
			set theAliases to my replaceText(theAliases, ", ", "\") OR (\"")
			set theName to "(\"" & theName & "\")"
			set theSearch to theName & " OR " & "(\"" & theAliases & "\")"
		else
			set theName to "\"" & theName & "\""
			set theSearch to theName
		end if
		
		set theResults to search "content:" & theSearch & space & "kind:markdown" in theDB
		
		
		repeat with theResult in theResults -- outer loop -- collecting tabs in each window
			
			set theUUID to the uuid of theResult
			set theRecord to get record with uuid theUUID
			set theName to the name of theRecord -- preparing the display info
			set theName to my replaceText(theName, "\"", "")
			set theURL to the reference URL of theRecord
			
			
			set theString to "{
				        \"title\": \"" & theName & "\",
				        \"subtitle\": \"" & theSubtitle & "\",
				        \"arg\":  \"" & theURL & "\",
				        },"
			
			set theStrings to theStrings & theString & return
			
			
		end repeat -- end of outer loop
		
		set CompleteString to "{\"items\": [" & return & theStrings & "]}" -- adding the beggining and end of the string
		set CompleteString to my replaceText(CompleteString, "," & return & "]}", return & "]}") -- removal of the very last comma
		
		return CompleteString
		
	end tell -- and everyone lived happily ever after
	
end run -- thank god, I was getting tired


on replaceText(theString, old, new)
	set {TID, text item delimiters} to {text item delimiters, old}
	set theStringItems to text items of theString
	set text item delimiters to new
	set theString to theStringItems as text
	set text item delimiters to TID
	return theString
end replaceText

on trimtext(theText, theCharactersToTrim, theTrimDirection)
	set theTrimLength to length of theCharactersToTrim
	if theTrimDirection is in {"beginning", "both"} then
		repeat while theText begins with theCharactersToTrim
			try
				set theText to characters (theTrimLength + 1) thru -1 of theText as string
			on error
				-- text contains nothing but trim characters
				return ""
			end try
		end repeat
	end if
	if theTrimDirection is in {"end", "both"} then
		repeat while theText ends with theCharactersToTrim
			try
				set theText to characters 1 thru -(theTrimLength + 1) of theText as string
			on error
				-- text contains nothing but trim characters
				return ""
			end try
		end repeat
	end if
	return theText
end trimtext


```


#Applescript #DEVONthink #Alfred