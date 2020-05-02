# New markdown record with selection as name
If no text is selected, a dialog window will ask for the new record's name. Optionally, the new record can already include the clipboard content as body.

```applescript
property WikiTop : "tags: #"
property OptionToAddClipboard : false
property OptionToAddReturnLink : false
property CreateInSameGroup : true
property OpenInSameWindow : true

tell application id "DNtp"
	try
		if not (exists think window 1) then error "No window opened."
		
		set theRecord to (content record of think window 1) -- 
		set theSource to name of theRecord
		set theGroup to parent 1 of theRecord
		
		-- Get note name
		
		try
			set invalidName to false
			set theName to selected text of think window 1
			if theName is "" then set invalidName to true
		on error
			set invalidName to true
		end try
		
		if invalidName is true then set theName to display name editor info "Insert name of new markdown file"
		
		
		
		-- Add clipboard to body
		
		if OptionToAddClipboard then
			set theClipOption to {"true", "false"}
			set theClip to choose from list theClipOption with prompt "Insert clipboard body of note?" default items {"true"}
			set theClipAns to item 1 of theClip
			if theClipAns is "true" then set theMDbody to "# " & theName & return & return & (the clipboard)
			if theClipAns is not "true" then set theMDbody to "# " & theName & return & return
			
		else
			set theMDbody to "# " & theName & return & return
			
		end if
		
		-- Add return link
		
		if OptionToAddReturnLink then
			set theBLC to {"true", "false"}
			set theBL to choose from list theBLC with prompt "Insert link back to source?" default items {"true"}
			set theBLR to item 1 of theBL -- Resultado da escolha
			if theBLR is "true" then set theMD to WikiTop & return & return & "Backlink: " & theSource & "  " & return & return & theMDbody
			if theBLR is not "true" then set theMD to WikiTop & return & return & theMDbody
		else
			set theMD to WikiTop & return & return & theMDbody
		end if
		
		-- Create new markdown file
		if CreateInSameGroup then
			set theResult to create record with {name:theName, type:markdown, content:theMD} in theGroup
		else
			set theResult to create record with {name:theName, type:markdown, content:theMD}
		end if
		
		-- Open in same window
		if OpenInSameWindow then
			open tab for record theResult in think window 1
		else
			open tab for record theResult
		end if
		
		-- Error
	on error error_message number error_number
		if the error_number is not -128 then display alert "DEVONthink" message error_message as warning
	end try
end tell
```


#Applescript #DEVONthink