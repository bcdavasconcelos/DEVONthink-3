# Todas ocorrências do termo

```applescript
-- Script filter: Return links (for Keyboard Maestro Macro)

use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions

on run
	
	global theDB, theRecord, theName, theAliases, theSearch, theResults, theResult, theResultName, theUUID, theResultURL, theListItem, theList
	
	
	
	tell application id "DNtp"
		
		set theList to {}
		
		set theRecord to content record of think window 1
		
		set theDB to the database of theRecord
		set theName to the name of theRecord
		set theAliases to aliases of theRecord
		set theURL to the reference URL of theRecord
		if theAliases is not "" then
			set theAliases to my trimtext(theAliases, ", ", "end")
			set theSearchAliases to my replaceText(theAliases, ", ", "\") OR (\"")
			set theQuotedName to "(\"" & theName & "\")"
			set theSearch to theQuotedName & " OR " & "(\"" & theSearchAliases & "\")"
		else
			set theQuotedName to "\"" & theName & "\""
			set theSearch to theQuotedName
		end if
		
		set theResults to search "content:" & theSearch & space & "{any: tags:02_Corpus_Platonicum;03_Corpus_Aristotelicum} name!=" & theName & space & "kind:markdown" in theDB
		
		
		repeat with theResult in theResults
			set theUUID to the uuid of theResult
			--	set theRecord to get record with uuid theUUID
			set theResultName to the name of theResult -- preparing the display info
			set theResultName to my replaceText(theResultName, "\"", "")
			set theResultURL to the reference URL of theResult
			
			set theText to the plain text of theResult -- Texto para busca
			set theOccurrences to ""
			set theListOfOccurrences to {}
			set theHeader to false
			set theOccurrences to regex search theText search pattern "(\\s{0,1}.{0,40}" & theName & ".{0,50}\\s{0,1})" -- Padrão de busca
			
			if theOccurrences is not "" then -- se houver ocorrências...
				
				
				repeat with theOccurrence in theOccurrences -- para cada uma...
					if theOccurrence does not contain "|" then
						if theOccurrence does not contain "#" then
							set theOccurrence to my replaceText(theOccurrence, return, "")
							set theOccurrence to my replaceText(theOccurrence, theName, "<mark>" & theName & "</mark>") -- marcar
							set theURL to theResultURL & "?search=" & theName -- gerar link
							set theOccurrence to return & "> [" & theOccurrence & "](" & theURL & ")  " & return -- citação com o link
							set theListOfOccurrences to theListOfOccurrences & theOccurrence -- lista de CITAÇÕES pronta
							
							set theHeader to true
						end if
					end if
					
					set theAliases to my strToList(theAliases, ", ")
					repeat with theAlias in theAliases
						set theOccurrences to regex search theText search pattern "(\\s{0,1}.{0,40}" & theAlias & ".{0,50}\\s{1})" -- Padrão de busca
						
						if theOccurrences is not "" then -- se houver ocorrências...
							
							
							repeat with theOccurrence in theOccurrences -- para cada uma...
								if theOccurrence does not contain "|" then
									if theOccurrence does not contain "#" then
										set theOccurrence to my replaceText(theOccurrence, return, "")
										set theOccurrence to my replaceText(theOccurrence, theAlias, "<mark>" & theAlias & "</mark>") -- marcar
										set theURL to theResultURL & "?search=" & theAlias -- gerar link
										set theOccurrence to return & "> [" & theOccurrence & "](" & theURL & ")  " -- citação com o link
										set theListOfOccurrences to theListOfOccurrences & theOccurrence -- lista de CITAÇÕES pronta
										
										set theHeader to true
									end if
								end if
								
							end repeat
						end if
					end repeat
					
				end repeat
				if theHeader is true then
					set ListItem to return & "## " & theResultName & return & theListOfOccurrences & return
				else
					set ListItem to "* " & theResultName & return
				end if
				
			else
				
				set ListItem to "* " & theResultName & return
				
			end if
			
			set the end of theList to ListItem
			
			
		end repeat -- end of outer loop
		
		set theList to my sortlist(theList)
		--	set theList to my replaceText(theList, quote, "")
		
		--set theList to my replaceText(theList, "[" & return, "[")
		--set theList to my replaceText(theList, " ]" & return, "]")
		--		
		--set theList to my replaceText(theList, "\"" & return, "")
		set theList to regex search theList search pattern "[" & return replace template "["
		return theList as string
		
		
	end tell -- and everyone lived happily ever after
	
end run -- thank god, I was getting tired


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
on listToStr(theList, d)
	local thestr
	set {TID, text item delimiters} to {text item delimiters, d}
	set thestr to theList as text
	set text item delimiters to TID
	return thestr
end listToStr

-- set theList to my strToList(theString, ", ")
on strToList(thestr, d)
	local theList
	set {TID, text item delimiters} to {text item delimiters, d}
	set theList to every text item of thestr
	set text item delimiters to TID
	return theList
end strToList
```
