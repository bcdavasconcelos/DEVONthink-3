# Script filter: Tabs (for Alfred Workflow)

```applescript
-- Filter tabs in think windows (for Alfred Workflow)

-- 2020-05-07-14-38-39
-- 2020-05-07-23-44-00 - Added the index alongside the class of the window in the subtitles
-- 2020-05-08-11-14-48 Changed the name of docs to uppercase
-- 2020-05-08-15-40-09 Removed the `\"uid\": \"" & FullID & "\",` line so that DEVONthink manages the sort order and not Alfred.
-- 2020-05-08-16-45-57 JSON handler
-- 2020-05-08-18-48-24 Support for BBEdit, MMD Composer, Marked 2, OmniOutliner

-- This script can collect: i. all tabs (in all windows) or ii. tabs in specific think window 
-- if WinID is 0 → get tabs from all think windows
-- if WinID is id x → set theWin to get window id WinID & set theTabs to every tab in theWin

on run
	
	global theWins, theWin, theTabs, theTab, WinID, TabID, theSubtitle, theName, theString, theStrings, CompleteString
	
	
	set WinID to ((system attribute "WinID") as number)
	-- if this doesn't work, then other option is this:
	--	try
	--on error
	--	set WinID to 0
	--end try
	-- set WinID to 182
	
	tell application id "DNtp"
		
		set theStrings to {}
		
		if WinID is 0 then
			set theWins to think windows -- all think windows
		else
			set theWins to get window id WinID -- specific think window
		end if
		
		repeat with theWin in theWins -- outer loop -- collecting tabs in each window
			
			set theTabs to every tab in theWin
			set WinID to id of theWin
			
			repeat with theTab in theTabs -- inner loop -- collecting info on each tab 
				
				try
					set TabID to the id of theTab -- preparing the argument
					set theName to name of content record in theTab -- preparing the display info
					set theName to my replaceText(theName, "\"", "")
					
					set theSubtitle to "In " & (class of theWin)
					if theSubtitle is "In document window" then set the theName to my change_case(theName, "upper")
					set theSubtitle to theSubtitle & space & (index of theWin) -- preparing the display info
					
					
					set theArg to WinID & "," & TabID -- preparing the argument
					set theString to my json_string(theName, theSubtitle, theArg)
					
					set theStrings to theStrings & theString & return
					
				end try
				
			end repeat -- end of inner loop
		end repeat -- end of outer loop
		
		if application "BBEdit" is running then
			try
				tell application "BBEdit"
					set myFile to get file of front document of window 1
				end tell
				set thePath to POSIX path of myFile
				set theRecord to my lookup_path(thePath)
				set theString to my json_with_theRecord(theRecord, "BBedit")
				set theStrings to theStrings & theString & return
			end try
		end if
		
		if application "HoudahSpot" is running then
			try
				tell application "HoudahSpot"
					tell document 1
						set theSelection to selection
						set thisResult to item 1 of theSelection
						set thePath to path of thisResult
					end tell
				end tell
				set theRecord to my lookup_path(thePath)
				set theString to my json_with_theRecord(theRecord, "HoudahSpot")
				set theStrings to theStrings & theString & return
			end try
		end if
		
		if application "Marked 2" is running then
			try
				tell application "Marked 2"
					set thePath to path of document 1
				end tell
				set theRecord to my lookup_path(thePath)
				set theString to my json_with_theRecord(theRecord, "Marked 2")
				set theStrings to theStrings & theString & return
			end try
		end if
		
		if application "MultiMarkdown Composer" is running then
			try
				tell application "MultiMarkdown Composer"
					set doc to document of window 1
					set theFile to file of doc
				end tell
				set thePath to POSIX path of theFile
				set theRecord to my lookup_path(thePath)
				set theString to my json_with_theRecord(theRecord, "MultiMarkdown Composer")
				set theStrings to theStrings & theString & return
			end try
		end if
		
		if application "OmniOutliner" is running then
			try
				tell application "OmniOutliner"
					set theFile to file of document 1
					set thePath to POSIX path of theFile
				end tell
				set theRecord to my lookup_path(thePath)
				set theString to my json_with_theRecord(theRecord, "OmniOutliner")
				set theStrings to theStrings & theString & return
			end try
		end if
		
		set CompleteString to my complete_json_string(theStrings)
		
		set CompleteString to my replaceText(CompleteString, "in document window", "in doc window")
		set CompleteString to my replaceText(CompleteString, "in viewer window", "in vi window")
		
		return CompleteString
		
	end tell -- and everyone lived happily ever after
	
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


#Applescript #DEVONthink #Alfred #BBEdit #Script Filter#