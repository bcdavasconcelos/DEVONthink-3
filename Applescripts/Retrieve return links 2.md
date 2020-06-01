# Retrieve return links 2
This version of the script offers several possibilities. Among them: adding the links as rtf text in the custom metadata fields, adding the links in markdown to the bottom of the note using auto wiki links or `[[wiki links]]`. 

See [this thread](https://discourse.devontechnologies.com/t/return-links-back-links/54390?u=bernardo_v) for a discussion.

## Updates

```applescript

use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions


property MainUUID : ""

tell application id "DNtp"
	
	try
		set invalid to false
		set options to {"RTF", "MD", "Cancel"}
		set options2 to {"Links", "Links and aliases", "Cancel"}
		set options3 to {"Auto Wiki Links", "Without Auto Wiki Links", "Cancel"}
		set answer to the button returned of (display dialog "Should the links be in RTF or MD?" buttons the options default button 2)
		
		if answer is "RTF" then
			set RTFLinks to true
			set answer2 to the button returned of (display dialog "Should the search include aliases?

NOTE: without aliases, the search command (?search=name) will be added to the link" buttons the options2 default button 1)
			set answer3 to ""
			
		else
			set RTFLinks to false
			set answer2 to the button returned of (display dialog "Should the search include aliases?

NOTE: without aliases, the search command (?search=name) will be added to the link" buttons the options2 default button 1)
			set answer3 to the button returned of (display dialog "Are you using Auto Wiki Links?

NOTE: with auto wiki links ON, there is no need for x-devonthink URLs" buttons the options3 default button 1)
			
		end if
		
		
		if answer2 is "Links and aliases" then
			set UseAliases to true
		else
			set UseAliases to false
		end if
		
		
		if answer3 is "Auto Wiki Links" then
			set AutoWikiLinks to true
		else
			set AutoWikiLinks to false
		end if
		
	on error error_message number error_number
		set invalid to true
		if invalid then error "Error"
	end try
	
	
	set theRecord to (content record of think window 1)
	set theText to plain text of theRecord
	set theName to name of theRecord
	
	-- Prepare the search string
	if UseAliases then
		set theName2 to "(\"" & theName & "\")"
		set theAliases to aliases of theRecord
		set theAliases to my trimtext(theAliases, ", ", "end")
		set theAliases to my replaceText(theAliases, ", ", "\") OR (\"")
		set theSearchList to "(\"" & theAliases & "\")"
		set theSearchList to theName2 & " OR " & "(\"" & theAliases & "\")"
	else
		
		set theSearchList to "\"" & theName & "\""
		
	end if
	
	
	-- Perform the search
	set theGroup to get record with uuid MainUUID
	set theRecords to search "content:" & theSearchList & space & "kind:markdown" in theGroup
	
	if RTFLinks is true then
		
		set theList2 to {}
		repeat with each in theRecords
			
			if UseAliases then
				set the end of theList2 to return & quoted form of ("<font size=\"5\"><font face=\"Menlo\"><a href=\"" & reference URL of each & "\">" & name of each & "</a></font></font></br>")
				
			else
				set theName to my replaceText(theName, " ", "%20")
				set the end of theList2 to return & quoted form of ("<font size=\"5\"><font face=\"Menlo\"><a href=\"" & reference URL of each & "?search=" & theName & "\">" & name of each & " " & "</a></font></font> </br>")
			end if
		end repeat
		
		set theList2 to regex change theList2 search pattern "^.+" & name of theRecord & ".+" replace template "$1"
		
		set theList2 to my sortlist(theList2)
		
		set theRTF to (do shell script "echo " & theList2 & " | textutil -stdin -stdout -inputencoding utf-8 -format html -convert rtf | pbcopy")
		set theRTF to the clipboard
		
		if UseAliases then
			add custom meta data «class RTF » of theRTF for "return links with aliases" to theRecord
			
		else
			add custom meta data «class RTF » of theRTF for "return links" to theRecord
		end if
		
		
		
	else
		
		
		set theList to {}
		repeat with each in theRecords
			
			-- if aliases are being used, we can't add further commands to the URL (such "?search")
			if UseAliases then
				
				-- if AutoWikiLinks is active, there is no need to add the x-devonthink address	
				if AutoWikiLinks then
					set the end of theList to name of each & " | "
				else
					-- Without AutoWikiLinks, we need the full address
					set the end of theList to "[" & name of each & "]" & "(" & reference URL of each & ")" & " | "
					--set the end of theList to return & "* [" & name of each & "]" & "(" & reference URL of each & ")"
				end if
				
			else
				
				if AutoWikiLinks then
					set the end of theList to name of each & " | "
				else
					-- Without AutoWikiLinks, we need the full address
					set the end of theList to "[" & name of each & "]" & "(" & reference URL of each & ")" & " | "
					--set the end of theList to return & "* [" & name of each & "]" & "(" & reference URL of each & ")"
				end if
				
			end if
			
		end repeat
		set theList to my sortlist(theList)
		
		
		-- Remove old Returnlinks section
		try
			set oldDelims to AppleScript's text item delimiters -- salvar o delimitador padrão
			set AppleScript's text item delimiters to {"# Return links"} -- declarar delimitador novo
			set delimitedList to every text item of theText
			set AppleScript's text item delimiters to oldDelims -- restaurar delimitador padrão
		on error
			set AppleScript's text item delimiters to oldDelims -- restaurar delimitador padrão em caso de erro
		end try
		
		-- Add new ReturnLinks section
		try
			set theText to item 1 of delimitedList
			set theText to my trimtext(theText, "
", "end")
			
			set the plain text of theRecord to theText & "

# Return links" & return & theList as text
		end try
		
		
	end if
	
	
	set answer to ""
	set answer2 to ""
	set answer3 to ""
	
	display notification "Hooray! Success!"
	
	-- end repeat
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


#Applescript #DEVONthink