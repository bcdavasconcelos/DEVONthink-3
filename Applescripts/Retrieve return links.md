# Retrieve return links
This will find links leading to each selected note and add them to the `Return links` custom metadata field. See [this thread](https://discourse.devontechnologies.com/t/return-links-back-links/54390?u=bernardo_v) for a discussion.

## Updates
- 2020-05-24-13-17-38 Now that DT3 is displaying links properly in rtf text fields in the inspector while on in dark mode, the html before the conversion has been updated to turn the whole string into a link. Also, the sort list command is better now that it considers numeric strings (avoiding the issue of x10 being before x2).

```applescript
use AppleScript version "2.4" -- Yosemite (10.10) or later
use scripting additions

property UseAliases : true
--property theFont : "Source Sans Pro"
property theFont : "Garamond Premier Pro"
property MDField : "returnlinks"

on performSmartRule(theSources)
	tell application id "DNtp"
		show progress indicator "Updating return links"
		--	set theSources to the selection
		repeat with theSource in theSources
			
			set theSearchString to my prepare_search_string(theSource)
			set theName to name of theSource
			try
				set theShortName to texts 1 through 4 of theName
			on error
				set theShortName to texts 1 through 3 of theName
			end try
			set theShortName to my replaceText(theShortName, " ", "%20")
			set theList to my get_list(theSearchString, theShortName)
			set theResult to my print_to_DT(theList, theSource)
			
			
		end repeat
		hide progress indicator
		display notification "return links updated"
		
	end tell
end performSmartRule

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

on prepare_search_string(theSource)
	tell application id "DNtp"
		set theName to name of theSource
		set theNameString to "(\"" & theName & "\")"
		set theDB to the name of current database
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
		
		set theSearchString to "name!=" & theName & " content: " & theSearchString & space & "kind: markdown"
		
		return theSearchString
	end tell
end prepare_search_string

on get_list(theSearchString, theShortName)
	tell application id "DNtp"
		
		set theRecords to search theSearchString in current database
		set theNR to count theRecords
		--	log message "Found " & theNR & " return links to the record"
		
		set theList to {}
		repeat with each in theRecords
			
			set the end of theList to return & quoted form of ("<a id=" & name of each & "\" href=\"" & reference URL of each & "?search=" & theShortName & "&reveal=1" & "\">" & name of each & "</a></br>")
			
			
		end repeat
		
		considering numeric strings
			set theList to my sortlist(theList)
		end considering
		
		return theList
		
	end tell
end get_list

on print_to_DT(theList, theSource)
	tell application id "DNtp"
		
		set theList to "'<font size=\"6\" color=\"#8080BB\"><font face=\"" & theFont & "\">'" & theList & "'</font></font>'"
		
		-- Convert to RTF
		set theRTF to (do shell script "echo " & theList & " | textutil -stdin -stdout -inputencoding utf-8 -format html -convert rtf | pbcopy")
		set theRTF to the clipboard
		set theRTF to «class RTF » of theRTF
		add custom meta data theRTF for MDField to theSource
		
	end tell
end print_to_DT


```


#Applescript #DEVONthink