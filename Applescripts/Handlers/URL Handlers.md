# URL Handlers


```applescript
-- Helper library for template scripts
-- © 2009-2014 DEVONtechnologies, LLC

property pWhiteSpaces : " "


-- Simple URL clean up
on cleanUpURL(theURL)
	if theURL ≠ missing value and theURL ≠ "" then
		set theNewURL to every character of theURL
		
		-- Strip leading and trailing spaces
		repeat until item 1 of theNewURL is not in pWhiteSpaces
			set theNewURL to items 2 through -1 of theNewURL
		end repeat
		repeat until item -1 of theNewURL is not in pWhiteSpaces
			set theNewURL to items 1 through -2 of theNewURL
		end repeat
		
		-- Strip < and > characters
		if item 1 of theNewURL is "<" then set theNewURL to items 2 through -1 of theNewURL
		if item -1 of theNewURL is ">" then set theNewURL to items 1 through -2 of theNewURL
		
		return theNewURL as string
	else
		return missing value
	end if
end cleanUpURL


```


```applescript

-- Encode a string for adding it to a URL
on urlencode(theText)
	set theTextEnc to ""
	repeat with eachChar in characters of theText
		set useChar to eachChar
		set eachCharNum to ASCII number of eachChar
		-- if eachCharNum = 32 then
		-- 	set useChar to "+"
		-- else
		if (eachCharNum ≠ 42) and (eachCharNum ≠ 95) and (eachCharNum < 45 or eachCharNum > 46) and (eachCharNum < 48 or eachCharNum > 57) and (eachCharNum < 65 or eachCharNum > 90) and (eachCharNum < 97 or eachCharNum > 122) then
			set firstDig to round (eachCharNum / 16) rounding down
			set secondDig to eachCharNum mod 16
			if firstDig > 9 then
				set aNum to firstDig + 55
				set firstDig to ASCII character aNum
			end if
			if secondDig > 9 then
				set aNum to secondDig + 55
				set secondDig to ASCII character aNum
			end if
			set numHex to ("%" & (firstDig as string) & (secondDig as string)) as string
			set useChar to numHex
		end if
		set theTextEnc to theTextEnc & useChar as string
	end repeat
	return theTextEnc
end urlencode

```


```applescript

-- Open a URL in a new tab
on openURLInTab(theURL)
	tell application id "DNtp"
		try
			if exists think window 1 then
				set theTab to open tab for URL theURL in think window 1
				set current tab of think window 1 to theTab
			else
				open tab for URL theURL
			end if
		on error error_message number error_number
			if the error_number is not -128 then display alert "DEVONthink" message error_message as warning
		end try
	end tell
end openURLInTab

```
