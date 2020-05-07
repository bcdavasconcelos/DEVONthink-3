# Filter tabs in think windows (for Alfred Workflow)

```applescript
use AppleScript version "2.4" -- Yosemite (10.10) or later
use scripting additions

-- 2020-05-07-14-38-39

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
					set FullID to WinID & "," & TabID -- preparing the argument
					set theSubtitle to "Select tab in " & class of theWin -- preparing the display info
					set theName to name of content record in theTab -- preparing the display info
					set theName to my replaceText(theName, "\"", "")
					
					
					set theString to "{
				      \"uid\": \"" & FullID & "\",
				        \"title\": \"" & theName & "\",
				        \"subtitle\": \"" & theSubtitle & "\",
				        \"arg\":  \"" & FullID & "\",
				        },"
					
					set theStrings to theStrings & theString & return
					
				end try
				
			end repeat -- end of inner loop
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

```
