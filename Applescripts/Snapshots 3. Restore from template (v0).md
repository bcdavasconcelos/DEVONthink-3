# Snapshots 3. Restore from template (v0)

```applescript


tell application id "DNtp"
	
	set theRecord to (content record of think window 1)
	set thePath to the path of theRecord -- Get the file path	
	
	set theText to the plain text of theRecord
	
	set theText to get custom meta data for "v0" from theRecord default value ""
	
	set the plain text of theRecord to theText
	
	log message "Version restored" info "Restored to template version " & ((current date) as string) --	
	
end tell



```


#Applescript #DEVONthink