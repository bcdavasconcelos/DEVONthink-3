# Transclusion into folded text

**Status:** *Finished*
- 2020-05-24-13-21-21: The `clean_trans_marks` routine still needs working.
- 2020-05-28-17-50-34: Finished. 

```applescript

use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions

-- @bcdav - 2020-06-04-21-27-41

--  WARNING: USE ONLY ON DUMMY FILES UNTIL YOU UNDERSTAND HOW IT WORKS

-- property IncludeBacklinks : false -- are you kidding?
-- property Backlinks_Field : "v0" -- where are the backlinks stored?

property PartialTransclusionAllowed : true
property Clean_MMD_header : true -- if you use MMD metadata, it might me a good ideia to remove it. take a look at the handler in the very bottom of the script to add or remove fields.
property Recursive : true -- false will cause it to run a single time, thus not transcluding placeholders found in the trancluded notes. It won't another level deeper, so to speak.

property Last_Modification_Date : true -- optional information about the transcluded note
property Add_toTop : false -- optional shortcut to the top of the page

property RandomizeFootnotes : true -- to avoid footnotes with the same ID after joining the notes.
property NameBetweenBrackets : true -- with auto wiki links the name of the note will be a link. This causes a glitch in the <summmary> element. With brackets we prevent it from being a link.

-- in case someone want to use it partially
property ToClean : true -- erase comment tags <!-- {{note.md}} --> and transcluded text
property ToTransclude : true -- change the placeholders for the text


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
		
		repeat until thePlaceHolders is {} -- this will make sure that we keep transcluding until there are no more placeholders left
			set theTurn to theTurn + 1 -- so that we don't accidentaly add the delimiter in the wrong place. it should always be at the bottom of the first transcluded note, but not at the end of the subnotes.
			
			set theMainText to my transclude(theMainText, theDB, theTurn)
			
			-- now let us check if there are placeholders left that could have appeared after the transclusion above
			set thePlaceHolders to regex search theMainText search pattern "^\\{\\{(.+)\\.md\\}\\}"
			
			if Recursive is false then set thePlaceHolders to {} -- no fun in this
			
		end repeat
		
	end if
	
	set theMainText to my replaceText(theMainText, linefeed & linefeed & linefeed, linefeed & linefeed) -- This is cosmetic. I really don't like three linefeeds in sequence. I always find that two are enought to keep things organized. If change this, just bear in mind that the commented out placeholder cannot stay only one linefeed away from  text. 
	
	set the plain text of theSource to theMainText
	--	return theMainText
	
	log message "Transclusion successful" info "It was nothig short of a miracle that it didn't failed"
	
end tell
-- end performSmartRule




------------ Handlers ------------ 
-- a.k.a. where things go wrong


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
		
		-- search pattern needs to include <!-- {{record.md}} --> plus everything until the delimiter
		set theSearchPattern to theSearchPattern & "(.|\\s)+?&nbsp;" & linefeed & "</details>"
		
		-- erase old transclusion and add back the original placeholder
		set theMainText to regex change theMainText search pattern theSearchPattern replace template theReplaceTemplate
		
	end repeat
end clean_trans_marks



on transclude(theMainText, theDB, theTurn)
	tell application id "DNtp"
		
		-- let us find all the placeholders
		set thePlaceHolders to regex search theMainText search pattern "^\\{\\{(.+)\\.md\\}\\}"
		
		-- if we find something, let us transclude it
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
					set theText to ""
					set theText to the plain text of theRecord -- get text
					
					
					if PartialTransclusionAllowed then
						if theSectionName is not "" then -- if section name, then get section
							set theText to my get_section(theText, theSectionName) -- I made a special handler for getting just the section. It is still being tested. I hope it holds.
						end if -- end theSectionName
					end if
					
					
					if Clean_MMD_header then set theText to my clean_mmd(theText) -- erase MMD metadata header that will not be transcluded 
					
					set theURL to the reference URL of theRecord -- we will add the url to the EDIT link in the bottom
					
					set myDateString to "" -- let us get the modification date
					if Last_Modification_Date then
						set d1 to the modification date of theRecord
						set myDateString to ((month of d1) & " " & (day of d1) & ", " & (year of d1) & " - " & (time string of d1)) as string
						set myDateString to "Last modified: " & myDateString & "<br>"
					end if
					
					set toTop to "" -- a handy link to navigate long files
					if Add_toTop then set toTop to "<a href=\"#top\">[To top]</a> | "
					
					set theURL to "<small><div style=\"text-align: right\">" & myDateString & toTop & "<a href=\"" & theURL & "?reveal=1\">[Edit section]" & "</a></div></small>  " & linefeed -- these are the URLs on the bottom of the note
					
					if RandomizeFootnotes then set theText to my randomize_FN(theText)
					
					set theBL to "" -- this will work with my other script for getting occurrences of words and backlinks just make sure to input the correct metadata field name
					--if IncludeBacklinks then
					--	try
					--		set theBL to get custom meta data for Backlinks_Field from theRecord default value ""
					--	end try
					--end if
					--					
					--if theBL is not "" then -- the backlinks will appear folded at the bottom
					--	set theBL to my clean_mmd(theBL)
					--	set theBL to "<details class=\"backlinks\">" & linefeed & "<summary class=\"backlinks\">" & "Backlinks" & "</summary>" & linefeed & (theBL as text) & " " & linefeed & "</details>" & linefeed & linefeed -- I made a special class so that the appearance of this particular item can be customised in the css
					--end if
					
					-- now we will prepare the text to be added via replace
					set theTaggedPlaceHolder to "<!-- " & thePlaceHolder & " -->  " & linefeed
					
					if NameBetweenBrackets then set theRecordName to "[" & theRecordName & "]"
					
					set theRecordName to "<summary>" & theRecordName & "</summary>"
					
					set theText to theText & "  " & linefeed
					
					----- ATTENTION HERE: These are the delimiters: change only if you know what you are doing 
					if theTurn = 1 then set theEndofString to "&nbsp;" & linefeed & "</details>"
					if theTurn > 1 then set theEndofString to linefeed & "</details>"
					
					----- END -----
					
					-- this is the final product with the content of the note
					set theReplacement to theTaggedPlaceHolder & linefeed & "<details>" & linefeed & theRecordName & linefeed & theText & linefeed & theURL & linefeed & theBL & theEndofString
					
					-- now we will replace the main text with it
					set theMainText to my replaceText(theMainText, thePlaceHolder, theReplacement)
					
				end if -- end if statement: "if theRecords are not {}"
				
			end repeat -- end repeat statement: "repeat with thePlaceHolder in thePlaceHolders"
			
		end if -- end if statement: thePlaceHolders is not {}
		
		
		return theMainText
		
	end tell
end transclude



on get_section(theText, theSectionName)
	set n to (count my decoupe(theSectionName, "#")) - 1 -- section level
	set m to (n - 1)
	set theSectionSearch to theSectionName & "((.|\\s)+?)" & "(#{" & n & "}|&nbsp;|#{" & m & "})" -- section level search string
	set theSectionText to regex search theText search pattern theSectionSearch replace template "$1" -- use regex to get it
	set theText to theSectionText -- if section name, text will be just this section
	return theText
end get_section


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


-- ADJUST TO YOUR NEEDS
on clean_mmd(theText)
	set theText to regex change theText search pattern "(title: .+)" replace template ""
	set theText to regex change theText search pattern "(aliases: .+)" replace template ""
	set theText to regex change theText search pattern "(tags: .+)" replace template ""
	set theText to regex change theText search pattern "^(.+)\\|(.){3,25}" & linefeed replace template "" -- this one is for my own use case	return theText
end clean_mmd





```


#Applescript #DEVONthink