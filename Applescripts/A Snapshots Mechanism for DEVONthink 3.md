# A Snapshots Mechanism for DEVONthink 3

## Warning
1. **Do not use this as a backup mechanism.** These snapshots should be considered *a convenience*. It was not intended to protect from loss of data. There is no safety net here.
2. These are **potentially destructive** scripts. **You cannot ⌘Z your way out of changes made by scripts.**
3. Finally, this is intended for markdown and/or plain text **only**.

***

# The idea

The idea is very simple. I created custom metadata fields to store snapshots of the text (**v1**-**v9**) and the modification time (**d1**-**d9**).


![](https://github.com/bcdavasconcelos/DEVONthink-3/blob/master/Images/e8c780d43bb773c740d79ffbe2af2f5f0671fa4c.jpeg?raw=true)
*I keep it at the bottom, so I don't see it if I don't want to.*


# The first script: Save Snapshot

The **first script** is for *storing the text*. It will store the current text in v1 and throw what was in v1 to v2, what was in v2 to v3 and so on. What was in v9 says goodbye.

```applescript
tell application id "DNtp"
	-- set theRecords to the selection
	-- repeat with theRecord in theRecords
	set theRecord to (content record of think window 1)
	
	set theText to the plain text of theRecord
	set theNewMod to the modification date of theRecord
	
	set theOldBackup to get custom meta data for "v9" from theRecord default value ""
	add custom meta data theOldBackup for "v10" to theRecord
	
	set theOldBackup to get custom meta data for "v8" from theRecord default value ""
	add custom meta data theOldBackup for "v9" to theRecord
	
	set theOldBackup to get custom meta data for "v7" from theRecord default value ""
	add custom meta data theOldBackup for "v8" to theRecord
	
	set theOldBackup to get custom meta data for "v6" from theRecord default value ""
	add custom meta data theOldBackup for "v7" to theRecord
	
	set theOldBackup to get custom meta data for "v5" from theRecord default value ""
	add custom meta data theOldBackup for "v6" to theRecord
	
	set theOldBackup to get custom meta data for "v4" from theRecord default value ""
	add custom meta data theOldBackup for "v5" to theRecord
	
	set theOldBackup to get custom meta data for "v3" from theRecord default value ""
	add custom meta data theOldBackup for "v4" to theRecord
	
	set theOldBackup to get custom meta data for "v2" from theRecord default value ""
	add custom meta data theOldBackup for "v3" to theRecord
	
	set theOldBackup to get custom meta data for "v1" from theRecord default value ""
	add custom meta data theOldBackup for "v2" to theRecord
	
	set theMod to get custom meta data for "v9" from theRecord default value ""
	add custom meta data theMod for "v10" to theRecord
	
	set theMod to get custom meta data for "d8" from theRecord default value ""
	add custom meta data theMod for "d9" to theRecord
	
	set theMod to get custom meta data for "d7" from theRecord default value ""
	add custom meta data theMod for "d8" to theRecord
	
	set theMod to get custom meta data for "d6" from theRecord default value ""
	add custom meta data theMod for "d7" to theRecord
	
	set theMod to get custom meta data for "d5" from theRecord default value ""
	add custom meta data theMod for "d6" to theRecord
	
	set theMod to get custom meta data for "d4" from theRecord default value ""
	add custom meta data theMod for "d5" to theRecord
	
	set theMod to get custom meta data for "d3" from theRecord default value ""
	add custom meta data theMod for "d4" to theRecord
	
	set theMod to get custom meta data for "d2" from theRecord default value ""
	add custom meta data theMod for "d3" to theRecord
	
	set theMod to get custom meta data for "d1" from theRecord default value ""
	add custom meta data theMod for "d2" to theRecord
	
	add custom meta data theNewMod for "d1" to theRecord
	add custom meta data theText for "v1" to theRecord
	
	log message "New version saved" info "Saved at " & ((current date) as string) --
	
end tell

```

***

# The second script: Compare/Restore Snapshot

The **second script** is for *restoring a snapshot*. When it is activated, it will ask whether you want to compare snapshots using BBEdit (could have used filemerge or something else, but I like BBEdit and you can install it for free) or simply restore to one of the previous snapshots.

![](https://github.com/bcdavasconcelos/DEVONthink-3/blob/master/Images/2020-05-22_09-59-56.png?raw=true)

The next dialog will prompt for the desired snapshot to be compared or restored.


![](https://github.com/bcdavasconcelos/DEVONthink-3/blob/master/Images/2020-05-22_10-00-19.png?raw=true)

If you chose to **restore**, that's it. The script will do so by replacing the text of the record by the stored snapshot and it will log the change in DEVONthink (I like how discrete the log is and prefer it over the notification function). If you chose to **compare** instead, you *can* make the changes you want and then *save*. 

*Here is the compare window open in BBEdit.*  
![](https://github.com/bcdavasconcelos/DEVONthink-3/blob/master/Images/2020-05-22_10-00-46.png?raw=true)
```applescript
-- BCDAVASCONCELOS 2020-05-22-09-58-27
-- DT3 Versioning Restore

tell application id "DNtp"
	set theRecord to the (content record of think window 1)
	
	
	set theOpts to {"Compare", "Restore", "Cancel"}
	set theAnswer1 to the button returned of (display dialog "Hi, I am Hal. What can I do for you?" buttons theOpts default button 1)
	
	set d0 to get custom meta data for "d0" from theRecord default value "" as string
	set myDateString0 to "v0 Template"
	
	try
		set d1 to get custom meta data for "d1" from theRecord default value "" as string
		set myDateString1 to ("v1" & " " & (month of d1) & " " & (day of d1) & ", " & (year of d1) & " - " & (time string of d1)) as string
	on error
		set myDateString1 to ""
	end try
	
	try
		set d2 to get custom meta data for "d2" from theRecord default value "" as string
		set myDateString2 to ("v2" & " " & (month of d2) & " " & (day of d2) & ", " & (year of d2) & " - " & (time string of d2)) as string
	on error
		set myDateString2 to ""
	end try
	
	try
		set d3 to get custom meta data for "d3" from theRecord default value "" as string
		set myDateString3 to ("v3" & " " & (month of d3) & " " & (day of d3) & ", " & (year of d3) & " - " & (time string of d3)) as string
	on error
		set myDateString3 to ""
	end try
	
	try
		set d4 to get custom meta data for "d4" from theRecord default value "" as string
		set myDateString4 to ("v4" & " " & (month of d4) & " " & (day of d4) & ", " & (year of d4) & " - " & (time string of d4)) as string
	on error
		set myDateString4 to ""
	end try
	
	try
		set d5 to get custom meta data for "d5" from theRecord default value "" as string
		set myDateString5 to ("v5" & " " & (month of d5) & " " & (day of d5) & ", " & (year of d5) & " - " & (time string of d5)) as string
	on error
		set myDateString5 to ""
	end try
	
	try
		set d6 to get custom meta data for "d6" from theRecord default value "" as string
		set myDateString6 to ("v6" & " " & (month of d6) & " " & (day of d6) & ", " & (year of d6) & " - " & (time string of d6)) as string
	on error
		set myDateString6 to ""
	end try
	
	try
		set d7 to get custom meta data for "d7" from theRecord default value "" as string
		set myDateString7 to ("v7" & " " & (month of d7) & " " & (day of d7) & ", " & (year of d7) & " - " & (time string of d7)) as string
	on error
		set myDateString7 to ""
	end try
	
	try
		set d8 to get custom meta data for "d8" from theRecord default value "" as string
		set myDateString8 to ("v8" & " " & (month of d8) & " " & (day of d8) & ", " & (year of d8) & " - " & (time string of d8)) as string
	on error
		set myDateString8 to ""
	end try
	
	try
		set d9 to get custom meta data for "d9" from theRecord default value "" as string
		set myDateString9 to ("v9" & " " & (month of d9) & " " & (day of d9) & ", " & (year of d9) & " - " & (time string of d9)) as string
	on error
		set myDateString9 to ""
	end try
	
	
	set l to {myDateString0, myDateString1, myDateString2, myDateString3, myDateString4, myDateString5, myDateString6, myDateString7, myDateString8, myDateString9} as list
	set theAnswer to (choose from list l with prompt {"Wise decision. 
Now, choose your destiny..."} default items "")
	
	set theAnswer to item 1 of theAnswer
	if theAnswer contains "v0" then set theAnswer to "v0"
	if theAnswer contains "v0" then set theTime to "Template"
	
	if theAnswer contains "v1" then set theAnswer to "v1"
	if theAnswer contains "v1" then set theTime to "d1"
	
	if theAnswer contains "v2" then set theAnswer to "v2"
	if theAnswer contains "v2" then set theTime to "d2"
	
	if theAnswer contains "v3" then set theAnswer to "v3"
	if theAnswer contains "v3" then set theTime to "d3"
	
	if theAnswer contains "v4" then set theAnswer to "v4"
	if theAnswer contains "v4" then set theTime to "d4"
	
	if theAnswer contains "v5" then set theAnswer to "v5"
	if theAnswer contains "v5" then set theTime to "d5"
	
	if theAnswer contains "v6" then set theAnswer to "v6"
	if theAnswer contains "v6" then set theTime to "d6"
	
	if theAnswer contains "v7" then set theAnswer to "v7"
	if theAnswer contains "v7" then set theTime to "d7"
	
	if theAnswer contains "v8" then set theAnswer to "v8"
	if theAnswer contains "v8" then set theTime to "d8"
	
	if theAnswer contains "v9" then set theAnswer to "v9"
	if theAnswer contains "v9" then set theTime to "d9"
	
	set theName to the name of theRecord
	set thePath to the path of theRecord -- Get the file path	
	
	set theText to the plain text of theRecord
	
	set theText to get custom meta data for (theAnswer as text) from theRecord default value ""
	try
		set theTime to get custom meta data for (theTime as text) from theRecord default value ""
		set theTime to ((year of theTime) & "-" & (month of theTime) & "-" & (day of theTime) & "-" & (time string of theTime)) as string
		set theTime to my replaceText(theTime, ":", "-")
	on error
		set theTime to "Template"
	end try
	--	set docName to theName & " " & theTime as text	
	
	if theAnswer1 is "Restore" then
		set the plain text of theRecord to theText
		log message "Version restored" info (theAnswer as text) & " " & theTime as text
	end if
	
	if theAnswer1 is "Compare" then
		set docName to theTime as text
		set _text to theText
		tell application "BBEdit"
			
			set tempFilePath to (path to temporary items as text) & docName
			set newDoc to make new document with properties {text:_text} initial save location tempFilePath
			save newDoc
			close newDoc
			--	open newDoc
			set theResult to compare file (thePath as POSIX file) against file (tempFilePath as alias)
			activate
		end tell
	end if
end tell


on replaceText(theString, old, new)
	set {TID, text item delimiters} to {text item delimiters, old}
	set theStringItems to text items of theString
	set text item delimiters to new
	set theString to theStringItems as text
	set text item delimiters to TID
	return theString
end replaceText
```

****

## A third little script - Restore Directly to v0

Side-note: I am also keeping a v0, which is sort of a template for the note and which won't be touched by the first script. I also set up a special shortcut to restore directly to this snapshot without any prompts. This will be the **third script**.


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