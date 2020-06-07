# Retrieve return links C

```applescript


-- bcdavasconcelos 
-- Source: https://github.com/bcdavasconcelos/DEVONthink-3
-- See also: https://discourse.devontechnologies.com/t/return-links-back-links/

property UseAliases : true
property AutoWiki_Links : true -- change to false for wiki links between double brackets, e.g. "[[link]]"
property theKind : "extension:md" -- the extension you will be looking for
property theDelimiter : "#### Backlinks" -- Delimiter of choice. e.g. # Backlinks
property limit : 60 -- limit for the number of backlinks
property removeduplicates : false

on performSmartRule(theSources)
	tell application id "DNtp"
		--	set theSources to selection
		
		show progress indicator "Updating return links" with cancel button
		
		repeat with theSource in theSources
			step progress indicator (the name of theSource) as text
			set theList to my get_list(theSource)
			set theText to my replace_section(theSource, theList)
			set the plain text of theSource to theText
			
		end repeat
		
		hide progress indicator
		
	end tell
end performSmartRule

-- Handlers section
on get_list(theSource)
	tell application id "DNtp"
		set theName to name of theSource
		set theNameString to "\"" & theName & "\""
		set theDB to the database of theSource
		
		set theAliases to ""
		if UseAliases then set theAliases to aliases of theSource
		if theAliases is not "" then
			set theAliasesString to my trimtext(theAliases, ", ", "end")
			set theAliasesString to my trimtext(theAliases, " ", "end")
			set theAliasesString to my replaceText(theAliasesString, ", ", "\") OR (\"")
			set theSearchString to theNameString & " OR " & "(\"" & theAliasesString & "\")"
		end if
		if theAliases is "" then
			set theSearchString to theNameString
		end if
		
		set theSearchString to "name!=" & theName & " content: " & theSearchString & space & theKind
		
		set theList to {}
		set m to 0
		set theRecords to search theSearchString in theDB
		repeat with theRecord in theRecords
			set m to m + 1
			if m < limit then
				set theRecordName to (name of theRecord) as text
				
				if AutoWiki_Links is false then
					set theText to the plain text of theRecord
					if theText contains "[[" & theName & "]]" then
						set the end of theList to "[[" & theRecordName & "]] | "
					else if theText does not contain "[[" & theRecordName & "]]" then
						set the end of theList to ""
					end if
				end if
				
				if AutoWiki_Links is true then
					set the end of theList to theRecordName & " | "
					
				end if
			end if
		end repeat
		
		considering numeric strings
			set theList to my sortlist(theList)
		end considering
		
		if removeduplicates then set theList to my removeDuplicateRecords(theList)
		
		return theList
		
	end tell
end get_list


on replace_section(theSource, theList)
	tell application id "DNtp"
		
		set theText to plain text of theSource
		
		try
			set OldDelimiter to AppleScript's text item delimiters
			set AppleScript's text item delimiters to theDelimiter
			set theDelimitedList to every text item of theText
			set AppleScript's text item delimiters to OldDelimiter
		on error
			set AppleScript's text item delimiters to OldDelimiter
		end try
		
		try
			set theText to item 1 of theDelimitedList
			set theText to my trimtext(theText, linefeed, "end")
			
			set theText to theText & linefeed & linefeed & theDelimiter & linefeed & linefeed & theList as text
			
			return theText
		end try
	end tell
end replace_section

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

on removeDuplicateRecords(inputList)
	set itemCount to count of items in inputList
	set outputList to {}
	repeat with anItem from 1 to itemCount
		set firstListItem to item anItem of inputList
		set occurrenceCount to 0
		repeat with anotherItem from 1 to count of items in outputList
			set secondListItem to item anotherItem of outputList
			if firstListItem is secondListItem then set occurrenceCount to occurrenceCount + 1
		end repeat
		if occurrenceCount = 0 then copy firstListItem to end of outputList
	end repeat
	
	return outputList
end removeDuplicateRecords

-- 2020-06-06-23-14-32 first version
-- 2020-06-07-20-50-57 fixed "AutoWiki_Links false" not finding any matches
-- 2020-06-09-00-10-07 added "limit"; changed "step progress indicator"; "considering numeric string", remove duplicates.


```
