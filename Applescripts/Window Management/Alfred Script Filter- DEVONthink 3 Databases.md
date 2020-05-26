# Alfred Script Filter: DEVONthink 3 Databases

```applescript
-- Filter open databases (for Alfred Workflow)

on run
	
	tell application id "DNtp"
		
		set theStrings to {}
		set theDbs to get every database
		
		repeat with theDb in theDbs

			set theName to name of theDb
			set theSubtitle to ""
			set theArg to (uuid of theDb)
			
			set theString to my json_string(theName, theSubtitle, theArg)
			set theStrings to theStrings & theString & return
		end repeat
		
		set CompleteString to my complete_json_string(theStrings)
		
		return CompleteString
		
		
	end tell
	
end run -- thank god, I was getting tired


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

on json_with_theRecord(theRecord, theApp)
	try
		tell application id "DNtp"
			set theName to name of theRecord -- preparing the display info
			set theName to my replaceText(theName, "\"", "")
			
			set theSubtitle to "In " & theApp
			
			set theArg to the uuid of theRecord -- preparing the argument
			
			set theString to my json_string(theName, theSubtitle, theArg)
			return theString
		end tell
	end try
end json_with_theRecord

on lookup_path(thePath)
	tell application id "DNtp"
		try
			set theDatabases to databases
			set theResults to {}
			
			repeat with thisDatabase in theDatabases
				set thisDatabasesResults to lookup records with path thePath in thisDatabase
				set theResults to theResults & thisDatabasesResults
			end repeat
			
			set thePath to ""
			set theRecord to item 1 of theResults
			return theRecord
			
		end try
	end tell
end lookup_path

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


#Applescript #DEVONthink #Alfred #Script Filter#