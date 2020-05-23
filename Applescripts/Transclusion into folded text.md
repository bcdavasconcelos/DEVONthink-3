# Transclusion into folded text

```applescript

use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions

-- on performSmartRule(theRecords)
tell application id "DNtp" -- to get the ball rolling
	
	set theSource to (content record of think window 1) -- get open record
	set theDB to the database of theSource -- get database
	set theMainText to the plain text of theSource -- get text	
	
	if theMainText contains "<!-- " then -- if sign of comments are present, clean it first
		set theMainText to my clean_trans_marks(theMainText)
	end if -- end conditional part related to cleaning the text	
	
	set theMainText to my transclude(theMainText, theDB)
	set thePlaceHolders to regex search theMainText search pattern "^\\{\\{(.+)\\.md\\}\\}"
	if thePlaceHolders is not {} then set theMainText to my transclude(theMainText, theDB)
	
	--	set theMainText to my replaceText(theMainText, "-->
	
	---", "-->")
	
	set theMainText to my replaceText(theMainText, linefeed & linefeed & linefeed, linefeed & linefeed)
	
	set the plain text of theSource to theMainText
	
end tell
--- end performSmartRule





-- handlers section
on randomize_fn(theText)
	set theText to my replaceText(theText, "[^1]", "[^" & (random number 100) & "]")
	set theText to my replaceText(theText, "[^2]", "[^" & (random number 100) & "]")
	set theText to my replaceText(theText, "[^3]", "[^" & (random number 100) & "]")
	set theText to my replaceText(theText, "[^4]", "[^" & (random number 100) & "]")
	set theText to my replaceText(theText, "[^5]", "[^" & (random number 100) & "]")
	set theText to my replaceText(theText, "[^6]", "[^" & (random number 100) & "]")
	set theText to my replaceText(theText, "[^7]", "[^" & (random number 100) & "]")
	set theText to my replaceText(theText, "[^8]", "[^" & (random number 100) & "]")
	set theText to my replaceText(theText, "[^9]", "[^" & (random number 100) & "]")
end randomize_fn

on clean_trans_marks(theMainText)
	set theRecords to regex search theMainText search pattern "\\<!-- \\{\\{(.+)\\.md\\}\\} -->" -- get comments
	
	repeat with theRecord in theRecords -- for each record reference with comment, clean it
		
		-- replace template: remove tags <!-- {{record.md}} --> to {{record.md}}
		set theReplaceTemplate to my replaceText(theRecord, "<!-- ", "")
		set theReplaceTemplate to my replaceText(theRecord, " -->", "")
		
		-- get <!-- {{record.md}} --> and prepare for regex search
		set theSearchPattern to my replaceText(theRecord, "{", "\\{") -- the { has to be escaped
		set theSearchPattern to my replaceText(theSearchPattern, "}", "\\}")
		set theSearchPattern to my replaceText(theSearchPattern, "|", "\\|")
		
		-- search pattern needs to include <!-- {{record.md}} --> plus everything until "&nbsp;"
		set theSearchPattern to theSearchPattern & "(.|\\s)+?&nbsp;" & "</details>"
		
		-- erase old transclusion and add back the original placeholder
		set theMainText to regex change theMainText search pattern theSearchPattern replace template theReplaceTemplate
		
		return theMainText
	end repeat
	
end clean_trans_marks

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

on get_section(theText, theSectionName)
	set n to (count my decoupe(theSectionName, "#")) - 1 -- section level
	set theSectionSearch to theSectionName & "((.|\\s)+)" & "(#{" & n & "}|&nbsp;)" -- section level search string
	set theSectionText to regex search theText search pattern theSectionSearch replace template "$1" -- use regex to get it
	set theText to theSectionText -- if section name, text will be just this section
end get_section

on clean_mmd(theText)
	set theText to regex change theText search pattern "(title: .+)" replace template ""
	set theText to regex change theText search pattern "(aliases: .+)" replace template ""
	set theText to regex change theText search pattern "(tags: .+)" replace template ""
	set theText to regex change theText search pattern "(.+)\\|\\|" replace template "" -- this is for my own custom wiki header
	--				set theText to regex change theText search pattern "(level: .+)" replace template "" -- this was for using custom css classes, but I droped it
end clean_mmd

on transclude(theMainText)
	
	tell application id "DNtp"
		set thePlaceHolders to regex search theMainText search pattern "^\\{\\{(.+)\\.md\\}\\}"
		
		if thePlaceHolders is not {} then
			repeat with thePlaceHolder in thePlaceHolders
				
				-- clean  the variables
				set {theName, theSearch, theText, theSubRecord, theReplacement, theSection, theSectionName} to {"", "", "", "", "", "", ""}
				
				-- get name
				set theRecordName to my replaceText(thePlaceHolder, "{{", "")
				set theRecordName to my replaceText(theRecordName, ".md}}", "")
				
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
				-- search for record using the name
				set theRecords to search "name:\"" & theRecordName & "\"" in current database
				
				if theRecords is not {} then
					
					set theRecord to the item 1 of theRecords -- our record will be the first item of the search
					set theText to the plain text of theRecord -- get text
					
					if theSectionName is not "" then -- if section name, then get section
						set theText to my get_section(theText, theSectionName)
					end if -- end theSectionName
					
					
					-- erase MMD metadata header that will not be transcluded 
					set theText to my clean_mmd(theText)
					set theURL to the reference URL of theRecord
					set d1 to the modification date of theRecord
					set myDateString to ((month of d1) & " " & (day of d1) & ", " & (year of d1) & " - " & (time string of d1)) as string
					
					set theURL to "<small><div style=\"text-align: right\">Last modified: " & myDateString & "<br><a href=\"#top\">[To top]</a> | <a href=\"" & theURL & "?reveal=1\">[Edit section]" & "</a></div></small>  "
					
					set theText to my randomize_fn(theText)
					
					-- the text to be added via replace
					set theRecordName to "[" & theRecordName & "]"
					set theReplacement to "<!-- " & thePlaceHolder & " -->  " & linefeed & "<details class=\"l2\">
	 <summary class=\"l2\">" & theRecordName & "</summary>" & linefeed & theText & "  " & linefeed & theURL & linefeed & linefeed & "&nbsp;" & linefeed & "</details>"
					
					set theMainText to my replaceText(theMainText, thePlaceHolder, theReplacement)
					
				end if -- end "if theRecords are not {}"
				
			end repeat -- end "repeat with thePlaceHolder in thePlaceHolders"
			
		end if -- end if thePlaceHolders is not {}
		
		--	set thePlaceHolders to regex search theMainText search pattern "^\\{\\{(.+)\\.md\\}\\}"
		--		if thePlaceHolders is not {} then set theMainText to transclude(theMainText)
		
	end tell
	
end transclude

```


#Applescript #DEVONthink