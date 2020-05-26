# Three pane view (Alfred Script Filter)


```applescript


-- 3PV
-- BCDAV 2020-05-23-09-00-37

on run
	tell application id "DNtp"
		
		set theDatabases to {}
		set theDatabases to my get_databases(theDatabases)
		
		set theWks to {}
		set theWks to my get_workspaces(theWks)
		
		set theFavs to {}
		set theFavs to my get_favorites(theFavs)
		
		
		set theJSONString to theDatabases & theFavs & theWks
		
		
		set theJSONString to my complete_json_string(theJSONString)
		return theJSONString
		
		
	end tell
	
end run -- thank god, I was getting tired


on json_string(theName, theSubtitle, theArg)
	set theString to "{
					        \"title\": \"" & theName & "\",
					        \"subtitle\": \"" & theSubtitle & "\",
					        \"arg\":  \"" & theArg & "\",
					        }," & linefeed
	return theString
end json_string

on complete_json_string(theStrings)
	set CompleteString to ""
	set CompleteString to "{\"items\": [" & return & theStrings & "]}" -- adding the beggining and end of the string
	set CompleteString to my replaceText(CompleteString, "," & linefeed & "]}", linefeed & "]}") -- removal of the very last comma
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

on get_favorites(theFavs)
	
	set theFavs to do shell script "python /Users/bernardovasconcelos/Dropbox/Application\\ Support/Alfred/Alfred.alfredpreferences/workflows/user.workflow.3076C41F-B6C0-4A53-8783-FBA55EE64C73/getfavs.py"
	
	set theFavs to my replaceText(theFavs, "{\"items\": [", "")
	set theFavs to my replaceText(theFavs, "]}", "")
	set theFavs to theFavs & "," & linefeed
	
	
end get_favorites

on get_workspaces(theWks)
	set theWks to {}
	tell application id "DNtp"
		set theWorkspaces to get workspaces
		repeat with theWorkspace in theWorkspaces
			set theName to theWorkspace
			set theSubtitle to "🏢 Workspace"
			set theArg to theWorkspace
			set theWorkspacesString to my json_string(theName, theSubtitle, theArg)
			set theWks to theWks & theWorkspacesString & return
		end repeat
		return theWks
	end tell
end get_workspaces

on get_databases(theDatabases)
	tell application id "DNtp"
		set theDatabases to {}
		set theDbs to get every database
		repeat with theDb in theDbs
			set theName to name of theDb
			set theSubtitle to "🗃 Database"
			set theArg to (uuid of theDb)
			set theString to my json_string(theName, theSubtitle, theArg)
			set theDatabases to theDatabases & theString & return
		end repeat
	end tell
end get_databases



```


```applescript

		set theTags to {}
		set theTags to my get_tags(theDB)

on get_tags(theDB)
	tell application id "DNtp"
		set theTagString to {}
		set theTags to get name of tag group of theDB
		repeat with theTag in theTags
			set theName to theTag
			set theSubtitle to "🏷 Tag"
			set theArg to theTag
			set theString to my json_string(theName, theSubtitle, theArg)
			set theTagString to theTagString & theString & return
		end repeat
	end tell
end get_tags

```
