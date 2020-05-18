# Multiple paths varying with number of selected records

This template allows for two pathways: one for a single record being displayed in the front window OR multiple records being selected in a viewer window.

```applescript


use AppleScript version "2.4" -- Yosemite (10.10) or later
use scripting additions


tell application id "DNtp"
	set Path_A to false
	set Path_B to false
	
	set n to (count selection)
	
	if n = 1 then set Path_A to true
	if n > 1 then set Path_B to true
	
	if Path_A then
		set theRecord to (content record of think window 1)
		
		
		
		
	end if
	
	
	if Path_B then
		set theRecords to the selection
		repeat with theRecord in theRecords
			
		

			
			
		end repeat
	end if
	
end tell


```
