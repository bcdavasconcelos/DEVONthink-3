# Retrieve return links

This will find links leading to each selected note and add them to the `Return links` custom metadata field. See [this thread][1] for a discussion.

```applescript
use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions
property MainUUID : ""

on performSmartRule(theRecords)
	tell application id "DNtp"
		repeat with theRecord in theRecords
			
			set theName to name of theRecord
			set theShortName to regex search once theName search pattern "^(.{4})" replace template "$1"
			set theShortName to my replaceText(theShortName, " ", "%20")
			set theGroup to get record with uuid MainUUID
			set theAliases to aliases of theRecord
			set theAliases to my trimtext(theAliases, ", ", "end")
			set theAliases to my replaceText(theAliases, ", ", "\") OR (\"")
			set theName to "(\"" & theName & "\")"
			set theSearchList to theName & " OR " & "(\"" & theAliases & "\")"
			
			set theRecs to search "content:" & theSearchList & space & "kind:markdown" in theGroup
			
			
			-- Prepare the list
			set theList to {}
			repeat with each in theRecs
				
				
				set the end of theList to return & quoted form of (name of each & "<a href=\"" & reference URL of each & "?search=" & theShortName & "&reveal=1" & "\">" & "**" & "</a></br>")
				
				
			end repeat
			
			
			-- Sort the list
			set theList to my sortlist(theList)
			
			set theFinalList to "'<font size=\"5\" color=\"#8080BB\"><font face=\"Menlo\">'" & theList & "'</font></font>'"
			
			-- Convert to RTF
			set theRTF to (do shell script "echo " & theFinalList & " | textutil -stdin -stdout -inputencoding utf-8 -format html -convert rtf | pbcopy")
			set theRTF to the clipboard
			set theRTF to «class RTF » of theRTF
			add custom meta data theRTF for "return links" to theRecord
			
			display notification "Hooray! Success!"
			
		end repeat
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

```

[1]:	https://discourse.devontechnologies.com/t/return-links-back-links/54390?u=bernardo_v

#Applescript #DEVONthink