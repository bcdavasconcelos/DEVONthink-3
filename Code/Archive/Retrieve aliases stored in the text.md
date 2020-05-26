# Retrieve aliases stored in the text
**Status:** *Archived*
- 2020-05-24-13-24-51: The last update (v. 3.5) rendered this script obsolete. (Even though it cannot properly find strings beginning with `@`)

---- 

I store the aliases of certain markdown files at the bottom of the text preceded by `@`. E.g. `@alias1, alias 2`. The smart-rule can be downloaded [here][1].

```applescript
use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions

on performSmartRule(theRecords)
	
	tell application id "DNtp"
		--set theRecords to the selection
		repeat with theRecord in theRecords
			
			set theText to plain text of theRecord
			set theResult to regex search theText search pattern "@.+"
			set theAliases to regex change theResult search pattern "@(.+)" replace template "$1"
			
			if theAliases is not {} then
				set the aliases of theRecord to theAliases as text
			end if
			
			if theAliases is {} then
				set theAliases to the aliases of theRecord
				set theText to theText & return & "@" & theAliases
				set the plain text of theRecord to theText
			end if
			
		end repeat
	end tell
	
end performSmartRule

```

[1]:	https://bcdav.blot.im/DT3-Smart-rules

#Applescript #DEVONthink