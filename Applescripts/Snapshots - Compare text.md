# Snapshots - Compare text

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


#Applescript #DEVONthink