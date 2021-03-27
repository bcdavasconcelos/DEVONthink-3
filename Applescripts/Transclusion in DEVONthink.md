# Transclusion in DEVONthink

[Alternatives for achieving transclusion within DT3 (wiki post)][1]

```applescript


use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions

-- on performSmartRule(theRecords)
tell application id "DNtp"
	
	set theRecord to (content record of think window 1)
	set theDB to the database of theRecord
	set theMainText to the plain text of theRecord
	
	if theMainText contains "<!-- " then
		set theFiles to regex search theMainText search pattern "\\<!-- \\{\\{(.+)\\.md\\}\\} -->" -- look for commented out transclusion marks
		
		repeat with theFile in theFiles
			set theReplaceTemplate to my replaceText(theFile, "<!-- ", "") -- reverse to uncommented
			set theReplaceTemplate to my replaceText(theReplaceTemplate, " -->", "") -- reverse to uncommented
			set theSearchPattern to my replaceText(theFile, "{", "\\{") -- Prepare to look for the commented mark using regex
			set theSearchPattern to my replaceText(theSearchPattern, "}", "\\}") -- Prepare to look for the commented mark using regex
			set theSearchPattern to my replaceText(theSearchPattern, "|", "\\|") -- Prepare to look for the commented mark using regex
			set theSearchPattern to theSearchPattern & "(.|\\s)+?\\*\\*\\*" -- Search pattern including transclusion mark and transcluded text
			set theMainText to regex change theMainText search pattern theSearchPattern replace template theReplaceTemplate
			
		end repeat
	end if
	
	set theFiles to regex search theMainText search pattern "^\\{\\{(.+)\\.md\\}\\}"
	if theFiles is not {} then
		repeat with theFile in theFiles
			--set theFile to item 1 of theFiles
			set theSubName to ""
			set theSearch to ""
			set theSubText to ""
			set theSubRecord to ""
			set theReplacement to ""
			set theSection to ""
			
			
			set theSearchName to my replaceText(theFile, "{{", "")
			set theSearchName to my replaceText(theSearchName, ".md}}", "")
			
			if theSearchName contains "|" then
				
				
				try -- parse variable
					set oldDelims to AppleScript's text item delimiters -- salvar o delimitador padrão
					set AppleScript's text item delimiters to {"|"}
					set theText to theSearchName
					set delimitedList to every text item of theText
					set AppleScript's text item delimiters to oldDelims -- restaurar delimitador padrão
				on error
					set AppleScript's text item delimiters to oldDelims -- restaurar delimitador padrão em caso de erro
				end try
				
				set theSection to item 1 of delimitedList
				set theSearchName to item 2 of delimitedList
				
			end if
			
			set theSearch to search "name:\"" & theSearchName & "\"" in theDB
			if theSearch is not {} then
				set theSubRecord to the item 1 of theSearch
				set theSubText to the plain text of theSubRecord
				if theSection is not "" then
					
					set n to (count my decoupe(theSection, "#")) - 1
					
					set theSectionSearch to theSection & "((.|\\s)+)" & "(#{" & n & "}|\\*\\*\\*)"
					
					set theSectionText to regex search theSubText search pattern theSectionSearch replace template "$1"
					
					set theSectionText to my replaceText(theSectionText, "***", "")
					
					set theSubText to theSectionText
					
				end if
				set theSubText to regex change theSubText search pattern "(aliases: .+)" replace template ""
				set theSubText to regex change theSubText search pattern "(m: .+)" replace template ""
				set theSubText to regex change theSubText search pattern "(tags: .+)" replace template ""
				set theSubURL to the reference URL of theSubRecord
				set theSubURL to "<div style=\"text-align: center\"><a href=\"" & theSubURL & "?reveal=1\">[Edit section]" & "</a> | <a href=\"#top\">[Topo]</a></div>  "
				
				
				-- Randomize footnotes
				
				set theSubText to my replaceText(theSubText, "[^1]", "[^" & (random number 100) & "]")
				set theSubText to my replaceText(theSubText, "[^2]", "[^" & (random number 100) & "]")
				set theSubText to my replaceText(theSubText, "[^3]", "[^" & (random number 100) & "]")
				set theSubText to my replaceText(theSubText, "[^4]", "[^" & (random number 100) & "]")
				set theSubText to my replaceText(theSubText, "[^5]", "[^" & (random number 100) & "]")
				set theSubText to my replaceText(theSubText, "[^6]", "[^" & (random number 100) & "]")
				set theSubText to my replaceText(theSubText, "[^7]", "[^" & (random number 100) & "]")
				set theSubText to my replaceText(theSubText, "[^8]", "[^" & (random number 100) & "]")
				set theSubText to my replaceText(theSubText, "[^9]", "[^" & (random number 100) & "]")
				-- end randomize
				--				set theSign to "\\* \\* \\*"
				set theSign to ""
				--				set theSign to "```" & linefeed & theSearchName & linefeed & "```" & linefeed
				set theReplacement to "<!-- " & theFile & " -->  " & linefeed & theSubText & "  " & linefeed & theSubURL & linefeed & linefeed & "***"
				set theMainText to my replaceText(theMainText, theFile, theReplacement)
				
			end if
			
		end repeat
	end if
	
	
	set theFiles to regex search theMainText search pattern "^\\{\\{(.+)\\.md\\}\\}"
	if theFiles is not {} then
		repeat with theFile in theFiles
			--set theFile to item 1 of theFiles
			set theSubName to ""
			set theSearch to ""
			set theSubText to ""
			set theSubRecord to ""
			set theReplacement to ""
			set theSection to ""
			
			
			set theSearchName to my replaceText(theFile, "{{", "")
			set theSearchName to my replaceText(theSearchName, ".md}}", "")
			
			if theSearchName contains "|" then
				
				
				try -- parse variable
					set oldDelims to AppleScript's text item delimiters -- salvar o delimitador padrão
					set AppleScript's text item delimiters to {"|"}
					set theText to theSearchName
					set delimitedList to every text item of theText
					set AppleScript's text item delimiters to oldDelims -- restaurar delimitador padrão
				on error
					set AppleScript's text item delimiters to oldDelims -- restaurar delimitador padrão em caso de erro
				end try
				
				set theSection to item 1 of delimitedList
				set theSearchName to item 2 of delimitedList
				
			end if
			
			set theSearch to search "name:\"" & theSearchName & "\"" in theDB
			if theSearch is not {} then
				set theSubRecord to the item 1 of theSearch
				set theSubText to the plain text of theSubRecord
				if theSection is not "" then
					
					set n to (count my decoupe(theSection, "#")) - 1
					
					set theSectionSearch to theSection & "((.|\\s)+)" & "(#{" & n & "}|\\*\\*\\*)"
					
					
					set theSectionText to regex search theSubText search pattern theSectionSearch replace template "$1"
					
					set theSectionText to my replaceText(theSectionText, "***", "")
					
					set theSubText to theSectionText
					
				end if
				set theSubText to regex change theSubText search pattern "(aliases: .+)" replace template ""
				set theSubText to regex change theSubText search pattern "(m: .+)" replace template ""
				set theSubText to regex change theSubText search pattern "(tags: .+)" replace template ""
				set theSubURL to the reference URL of theSubRecord
				set theSubURL to "<sup>[\\[Edit subsection\\]](" & theSubURL & "?reveal=1)</sup>  "
				
				
				-- Randomize footnotes
				
				set theMainText to my replaceText(theMainText, "[^1]", "[^" & (random number 100) & "]")
				set theMainText to my replaceText(theMainText, "[^2]", "[^" & (random number 100) & "]")
				set theMainText to my replaceText(theMainText, "[^3]", "[^" & (random number 100) & "]")
				set theMainText to my replaceText(theMainText, "[^4]", "[^" & (random number 100) & "]")
				set theMainText to my replaceText(theMainText, "[^5]", "[^" & (random number 100) & "]")
				set theMainText to my replaceText(theMainText, "[^6]", "[^" & (random number 100) & "]")
				set theMainText to my replaceText(theMainText, "[^7]", "[^" & (random number 100) & "]")
				set theMainText to my replaceText(theMainText, "[^8]", "[^" & (random number 100) & "]")
				set theMainText to my replaceText(theMainText, "[^9]", "[^" & (random number 100) & "]")
				
				-- end randomize
				
				
				set theReplacement to "<!-- " & theFile & " -->" & linefeed & linefeed & theSubText & "  " & theSubURL & linefeed
				set theMainText to my replaceText(theMainText, theFile, theReplacement)
				
			end if
			
		end repeat
	end if
	
	set theMainText to my replaceText(theMainText, "-->
			
			---", "-->")
	
	--	set theMainText to my replaceText(theMainText, "***"
	
	set the plain text of theRecord to theMainText
	
end tell

-- end performSmartRule

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



````

[1]:	https://discourse.devontechnologies.com/t/alternatives-for-achieving-transclusion-within-dt3-wiki-post/55724 "Alternatives for achieving transclusion within DT3 (wiki post)"

#Applescript #DEVONthink