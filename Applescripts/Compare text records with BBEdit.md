# Compare text records with BBEdit

For more information, see [this thread][1].

```applescript
property err : " is not a plain text file." -- Just a default error message as a convenience

tell application id "DNtp"
	
	set theRecords to the selection
	if (count selection) = 2 then -- Verify only two files are selected
		
		-- Check Record 1
		set theRecord1 to item 1 of theRecords
		set recType to type of theRecord1 -- Get the type
		if (recType as string) = "text" then -- If it's plain text, proceed
			set thePath1 to the path of theRecord1 -- Get the file path
		else
			display alert "Record 1 " & err -- If it's not plain text, alert and stop.
			return
		end if
		
		-- Check Record 2, if 1 passes…
		set theRecord2 to item 2 of theRecords
		set recType to type of theRecord2
		if (recType as string) = "text" then
			set thePath2 to the path of theRecord2
		else
			display alert "Record 2" & err
			return
		end if
		
	else -- If two files aren't selected, alert and stop.
		display alert "Please select two plain text files to compare."
		return
	end if
end tell

tell application "BBEdit"
	set theResult to compare file (thePath1 as POSIX file) against file (thePath2 as POSIX file)
	activate
end tell
```

[1]:	https://discourse.devontechnologies.com/t/compare-text-files-with-bbedit/55183/2?u=bernardo_v

#Applescript