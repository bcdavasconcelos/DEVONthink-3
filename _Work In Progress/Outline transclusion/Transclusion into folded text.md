# Transclusion into folded text
**Status:** *Finished*
- 2020-05-24-13-21-21: The `clean_trans_marks` routine still needs working.
- 2020-05-28-17-50-34: Finished. 

```applescript

use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions

property ToClean : true
property ToTransclude : true

property IncludeBacklinks : true
property Backlinks_Field : "v0"

property PartialTransclusionAllowed : true
property Clean_MMD_header : true
property DepthOfTranclusion : 4

property Last_Modification_Date : true
property Add_toTop : false

property RandomizeFootnotes : true
property NameBetweenBrackets : true


-- on performSmartRule(theRecords)
tell application id "DNtp" -- to get the ball rolling
	
	set theTurn to 0
	set theSource to (content record of think window 1) -- get open record
	set theDB to the database of theSource -- get database
	set theMainText to the plain text of theSource -- get text	
	
	if ToClean then
		if theMainText contains "<!-- " then -- if sign of comments are present, clean it first
			set theMainText to my clean_trans_marks(theMainText)
		end if -- end conditional part related to cleaning the text	
	end if
	
	if ToTransclude then
		
		set thePlaceHolders to regex search theMainText search pattern "^\\{\\{(.+)\\.md\\}\\}"
		
		repeat until thePlaceHolders is {}
			set theTurn to theTurn + 1
			set theMainText to my transclude(theMainText, theDB, theTurn)
			
			set thePlaceHolders to regex search theMainText search pattern "^\\{\\{(.+)\\.md\\}\\}"
			
		end repeat
		
	end if
	
	set theMainText to my replaceText(theMainText, linefeed & linefeed & linefeed, linefeed & linefeed)
	
	set the plain text of theSource to theMainText
	
	--	return theMainText
	
end tell
-- end performSmartRule




------------ Handlers ------------


on clean_trans_marks(theMainText)
	set theOccurrences to regex search theMainText search pattern "^\\<!-- \\{\\{(.+)\\.md\\}\\} -->" -- get comments
	
	repeat with theOccurrence in theOccurrences -- for each record reference with comment, clean it
		
		-- first, remove comment tags from <!-- {{record.md}} --> so that we get {{record.md}} to use as replace template below
		set theReplaceTemplate to my replaceText(theOccurrence, "<!-- ", "")
		set theReplaceTemplate to my replaceText(theReplaceTemplate, " -->", "")
		
		-- prepare regex patter to search for <!-- {{record.md}} --> so that we can exclude it from the text by replacing it
		set theSearchPattern to my replaceText(theOccurrence, "{", "\\{") -- the { has to be escaped
		set theSearchPattern to my replaceText(theSearchPattern, "}", "\\}")
		set theSearchPattern to my replaceText(theSearchPattern, "|", "\\|")
		
		-- search pattern needs to include <!-- {{record.md}} --> plus everything until "&nbsp;"
		set theSearchPattern to theSearchPattern & "(.|\\s)+?&nbsp;" & linefeed & "</details>"
		
		-- erase old transclusion and add back the original placeholder
		set theMainText to regex change theMainText search pattern theSearchPattern replace template theReplaceTemplate
		
	end repeat
end clean_trans_marks



on transclude(theMainText, theDB, theTurn)
	tell application id "DNtp"
		set thePlaceHolders to regex search theMainText search pattern "^\\{\\{(.+)\\.md\\}\\}"
		
		if thePlaceHolders is not {} then
			repeat with thePlaceHolder in thePlaceHolders
				
				-- clean  the variables
				set {theName, theSearch, theText, theSubRecord, theReplacement, theSection, theSectionName} to {"", "", "", "", "", "", ""}
				
				-- get name
				set theRecordName to my replaceText(thePlaceHolder, "{{", "")
				set theRecordName to my replaceText(theRecordName, ".md}}", "")
				
				if PartialTransclusionAllowed then
					-- if there is a pipe in the name, we are transcluding part of the text
					try
						if theRecordName contains "|" then
							
							try
								set oldDelims to AppleScript's text item delimiters
								set AppleScript's text item delimiters to {"|"}
								set theText to theRecordName
								set delimitedList to every text item of theText
								set AppleScript's text item delimiters to oldDelims
							on error
								set AppleScript's text item delimiters to oldDelims
							end try
							
							set theSectionName to item 1 of delimitedList -- section name first
							set theRecordName to item 2 of delimitedList -- record name next
							
						end if
					end try
				end if
				
				-- search for record using the name
				set theRecords to search "name:\"" & theRecordName & "\"" in theDB
				
				if theRecords is not {} then
					
					set theRecord to the item 1 of theRecords -- our record will be the first item of the search
					
					set theText to the plain text of theRecord -- get text
					
					
					if PartialTransclusionAllowed then
						if theSectionName is not "" then -- if section name, then get section
							set theText to my get_section(theText, theSectionName)
						end if -- end theSectionName
					end if
					
					
					if Clean_MMD_header then set theText to my clean_mmd(theText) -- erase MMD metadata header that will not be transcluded 
					
					set theURL to the reference URL of theRecord
					
					set myDateString to ""
					if Last_Modification_Date then
						set d1 to the modification date of theRecord
						set myDateString to ((month of d1) & " " & (day of d1) & ", " & (year of d1) & " - " & (time string of d1)) as string
						set myDateString to "Last modified: " & myDateString & "<br>"
					end if
					
					set toTop to ""
					if Add_toTop then set toTop to "<a href=\"#top\">[To top]</a> | "
					
					set theURL to "<small><div style=\"text-align: right\">" & myDateString & toTop & "<a href=\"" & theURL & "?reveal=1\">[Edit section]" & "</a></div></small>  " & linefeed
					
					if RandomizeFootnotes then set theText to my randomize_FN(theText)
					
					set theBL to ""
					if IncludeBacklinks then
						try
							set theBL to get custom meta data for Backlinks_Field from theRecord default value ""
						end try
					end if
					
					if theBL is not "" then
						set theBL to my clean_mmd(theBL)
						set theBL to "<details class=\"backlinks\">" & linefeed & "<summary class=\"backlinks\">" & "Backlinks" & "</summary>" & linefeed & (theBL as text) & " " & linefeed & "</details>" & linefeed & linefeed
					end if
					
					-- the text to be added via replace
					set theTaggedPlaceHolder to "<!-- " & thePlaceHolder & " -->  " & linefeed
					
					if NameBetweenBrackets then set theRecordName to "[" & theRecordName & "]"
					
					set theRecordName to "<summary>" & theRecordName & "</summary>"
					
					set theText to theText & "  " & linefeed
					
					if theTurn = 1 then set theEndofString to "&nbsp;" & linefeed & "</details>"
					if theTurn > 1 then set theEndofString to linefeed & "</details>"
					
					set theReplacement to theTaggedPlaceHolder & linefeed & "<details>" & linefeed & theRecordName & linefeed & theText & linefeed & theURL & linefeed & theBL & theEndofString
					
					set theMainText to my replaceText(theMainText, thePlaceHolder, theReplacement)
					
				end if -- end "if theRecords are not {}"
				
			end repeat -- end "repeat with thePlaceHolder in thePlaceHolders"
			
		end if -- end if thePlaceHolders is not {}
		
		
		return theMainText
		
	end tell
end transclude



on get_section(theText, theSectionName)
	set n to (count my decoupe(theSectionName, "#")) - 1 -- section level
	set theSectionSearch to theSectionName & "((.|\\s)+)" & "(#{" & n & "}|&nbsp;|#{" & n - 1 & "})" -- section level search string
	set theSectionText to regex search theText search pattern theSectionSearch replace template "$1" -- use regex to get it
	set theText to theSectionText -- if section name, text will be just this section
	return theText
end get_section



on clean_mmd(theText)
	set theText to regex change theText search pattern "(title: .+)" replace template ""
	set theText to regex change theText search pattern "(aliases: .+)" replace template ""
	set theText to regex change theText search pattern "(tags: .+)" replace template ""
	set theText to regex change theText search pattern "(.+)\\|\\|" replace template "" -- this one is for my own use case
	return theText
end clean_mmd



on randomize_FN(theText)
	if theText contains "[^1]" then set theText to my replaceText(theText, "[^1]", "[^" & (random number 100) & "]")
	if theText contains "[^2]" then set theText to my replaceText(theText, "[^2]", "[^" & (random number 100) & "]")
	if theText contains "[^3]" then set theText to my replaceText(theText, "[^3]", "[^" & (random number 100) & "]")
	if theText contains "[^4]" then set theText to my replaceText(theText, "[^4]", "[^" & (random number 100) & "]")
	if theText contains "[^5]" then set theText to my replaceText(theText, "[^5]", "[^" & (random number 100) & "]")
	if theText contains "[^6]" then set theText to my replaceText(theText, "[^6]", "[^" & (random number 100) & "]")
	if theText contains "[^7]" then set theText to my replaceText(theText, "[^7]", "[^" & (random number 100) & "]")
	if theText contains "[^8]" then set theText to my replaceText(theText, "[^8]", "[^" & (random number 100) & "]")
	if theText contains "[^9]" then set theText to my replaceText(theText, "[^9]", "[^" & (random number 100) & "]")
	return theText
end randomize_FN



on replaceText(theString, old, new)
	set {TID, text item delimiters} to {text item delimiters, old}
	set theStringItems to text items of theString
	set text item delimiters to new
	set theString to theStringItems as text
	set text item delimiters to TID
	return theString
end replaceText



on decoupe(t, d)
	local oTIDs, l
	set {oTIDs, AppleScript's text item delimiters} to {AppleScript's text item delimiters, d}
	set l to text items of t
	set AppleScript's text item delimiters to oTIDs
	return l
end decoupe


```


#Applescript #DEVONthink