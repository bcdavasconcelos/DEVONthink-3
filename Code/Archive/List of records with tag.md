# List of records with tag
**Status:** *Archived*.
Already incorporated into *All record in database organized by tag*.


```applescript

use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions




tell application id "DNtp"
	-- set theRecords to the selection
	-- repeat with theRecord in theRecords
	
	-- Filter open databases (for Alfred Workflow)
	
	set theRecord to (content record of think window 1)
	set theName to the name of theRecord
	
	--set theStrings to {}
	--set theTags to tag groups of current database
	--	
	--repeat with theTag in theTags
	set theList to {}
	set theRecordss to search "kind:markdown tag:" & theName
	repeat with theRecordd in theRecordss
		
		set theRecName to "* " & the name of theRecordd
		set the end of theList to linefeed & theRecName
		
	end repeat
	
	considering numeric strings
		set theList to my sortlist(theList)
	end considering
	--	return theList
	
	set theText to the plain text of theRecord
	set theText to theText & theList
	set the plain text of theRecord to theText
	
end tell


-- Handlers section

on replaceText(theString, old, new)
	set {TID, text item delimiters} to {text item delimiters, old}
	set theStringItems to text items of theString
	set text item delimiters to new
	set theString to theStringItems as text
	set text item delimiters to TID
	return theString
end replaceText

on trimtext(theText, theCharactersToTrim, theTrimDirection)
	set theTrimLength to length of theCharactersToTrim
	if theTrimDirection is in {"beginning", "both"} then
		repeat while theText begins with theCharactersToTrim
			try
				set theText to characters (theTrimLength + 1) thru -1 of theText as string
			on error
				-- text contains nothing but trim characters
				return ""
			end try
		end repeat
	end if
	if theTrimDirection is in {"end", "both"} then
		repeat while theText ends with theCharactersToTrim
			try
				set theText to characters 1 thru -(theTrimLength + 1) of theText as string
			on error
				-- text contains nothing but trim characters
				return ""
			end try
		end repeat
	end if
	return theText
end trimtext

on sortlist(theList)
	set theIndexList to {}
	set theSortedList to {}
	repeat (length of theList) times
		set theLowItem to ""
		repeat with a from 1 to (length of theList)
			if a is not in theIndexList then
				set theCurrentItem to item a of theList as text
				if theLowItem is "" then
					set theLowItem to theCurrentItem
					set theLowItemIndex to a
				else if theCurrentItem comes before theLowItem then
					set theLowItem to theCurrentItem
					set theLowItemIndex to a
				end if
			end if
		end repeat
		set end of theSortedList to theLowItem
		set end of theIndexList to theLowItemIndex
	end repeat
	return theSortedList
end sortlist



```
