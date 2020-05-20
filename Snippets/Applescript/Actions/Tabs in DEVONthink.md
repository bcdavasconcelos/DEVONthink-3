# Tabs in DEVONthink

## Attach

```applescript

# Attach tab

tell application id "DNtp"
	
	set theRecord to (content record of think window 1)
	set thePath to (path of theRecord as string)
	set theURL to the reference URL of theRecord
	set theTab to current tab of front window
	
	close theTab
	open tab for record theRecord in think window 2
	-- activate theRecord
	
	do shell script "open " & theURL
	-- tell me to do shell script "open " & quoted form of thePath
	
	
end tell


```


## Detach
```applescript



tell application id "DNtp"
	
	set theRecord to (content record of think window 1)
	set theTab to current tab of front window
-- set thePath to (path of theRecord as string)

	close theTab
	open tab for record theRecord
	activate theRecord

-- do shell script "open " & theURL
-- tell me to do shell script "open " & quoted form of thePath
	
	
end tell



```


#Applescript #DEVONthink