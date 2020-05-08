# Retrieve the name of the Record (Alfred Workflow)

```applescript

on run
	try
	set WinID to ((system attribute "WinID") as number)
	set TabID to ((system attribute "TabID") as number)
	end try
	
	try -- via UUID
		set theUUID to ((system attribute "theUUID") as text)
		set theNr to count (theUUID)
		if theNr is not 36 then set theUUID to ""
	end try
	
	
	tell application id "DNtp"
		
		if theUUID is not "" then
			set theRecord to get record with uuid theUUID
		else
			set theRecord to content record of (tab id TabID) in (window id WinID)
		end if
		
		set theName to name of theRecord
		return theName
		
	end tell
	
end run


```
