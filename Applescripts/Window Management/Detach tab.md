# Detach tab

```applescript


# Detach tab



tell application id "DNtp"
	
	set theRecord to (content record of think window 1)
	set theTab to current tab of front window
	set theRecord to content record of theTab
	close theTab
	-- open window for record theRecord
	-- do shell script "open " & theURL
	
	open tab for record theRecord
	activate theRecord
	
	-- set thePath to (path of theRecord as string)
	-- tell me to do shell script "open " & quoted form of thePath
	
	
end tell



```
