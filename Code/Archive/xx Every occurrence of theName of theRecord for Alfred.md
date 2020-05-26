# Every occurrence of theName of theRecord for Alfred
**Status:** *Abandoned*
**Reason:** *Alfred can't handle it if the JSON string is excessively long. *

```applescript
use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions
on run
	--set whichWin to (system attribute "whichWin")
	--set windowId to (system attribute "windowId")
	--set windowId to windowId as number
	tell application id "DNtp"
		
		set theStrings to {}
		set theSubtitle to ""
		set theZero to "0,"
		
		set theDB to get database with uuid "D2AE76E3-7D5D-40DD-B67D-3D210AB97B2E"
		set theRecord to content record of think window 1
		set theName to the name of theRecord
		set theName1 to "\"" & theName & "\""
		set theSearchList to theName1
		set theRecs to search "content:" & theSearchList & space & "kind:markdown" in the theDB
		
		repeat with theRec in theRecs
			set theText to the plain text of theRec
			set theSearchPattern to "((^| .{0,35})" & theName & "\\s*.{0,35}\\s*)"
			set theResults to regex search theText search pattern theSearchPattern
			set theURL to reference URL of theRec
			repeat with theResult in theResults
				set theArg to theURL & "£" & theResult
				set theString to "{
				    \"uid\": \"" & (random number) & "\",
				    \"title\": \"" & theName & "\",
				    \"subtitle\": \"" & theSubtitle & "\",
				    \"arg\":  \"" & theArg & "\",
				\"mods\" : {
				    \"shift\": { \"valid\": true, \"subtitle\": \"n\" }
				}
								        },"
				set theStrings to theStrings & theString & return
			end repeat
		end repeat
		set theStrings to regex change theStrings search pattern "(.{900,1000}}),
								        {" replace template "$1"
		set theFUllString to "{\"items\": [" & return & theStrings & return & "]}"
		-- set theFUllString to regex change theFUllString search pattern return & return replace template return
		set theFUllString to regex change theFUllString search pattern ",
			
			\\]\\}" replace template return & "]}"
		
		return theFUllString
		
	end tell
end run
```


#Applescript #DEVONthink #Em teste#