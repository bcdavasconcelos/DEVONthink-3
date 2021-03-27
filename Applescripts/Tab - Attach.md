# Attach tab

```applescript


-- 2021-03-27-08-40 bcdav

tell application id "DNtp"
	
	set WinClass to (class of front window) as text
	
	if WinClass is "viewer window" then
		set n to count the selection
		
		if n ≤ 1 then
			set theRecord to (content record of think window 1)
			set theTab to the current tab of think window 1
			set theURL to the reference URL of theRecord
			close theTab
			open tab for record theRecord in document window 1
		end if
		
		if n > 1 then
			set theRecords to the selection
			repeat with i in theRecords
				open tab for record i in document window 1
			end repeat
		end if
		
	else if WinClass is "document window" then
		
		set n to count think window
		set theRecord to (content record of document window 1)
		set theURL to the reference URL of theRecord
		set theTab to current tab of front window
		close theTab
		
		if n > 1 then
			open tab for record theRecord in think window 2
		else if n = 1 then
			open tab for record theRecord in viewer window 1
		end if

	end if

end tell
```