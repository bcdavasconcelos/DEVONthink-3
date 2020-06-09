# DT find DOI/ISBN and to Bookends and import metadata

```applescript

use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions

property thePattern : "ISBN (.+)"
property thePattern2 : "DOI (.+)"
property theMDField : "is?n"
property theMDField2 : "doi"

on performSmartRule(theRecords)
	tell application id "DNtp"
		repeat with theRecord in theRecords
			--	set theRecord to (content record of think window 1)
			
			set theName to the name of theRecord
			set theText to the plain text of theRecord
			
			set theISBN to regex search once theText search pattern thePattern replace template "$1"
			set theDOI to regex search once theText search pattern thePattern2 replace template "$1"
			
			add custom meta data theISBN for theMDField to theRecord
			add custom meta data theDOI for theMDField2 to theRecord
			
			
			tell application "Bookends"
				tell front library window
					if theDOI is not missing value then set theRef to quick add {theDOI}
					
					if theDOI is missing value then
						if theISBN is not missing value then set theRef to quick add {theISBN}
					end if
					
					delay 2
					
					-- Get selected publication 
					set theRefs to selected publication items
					set theRefsNo to count of theRefs
					set theRef to first item of theRefs
					-- Get properties of selected reference
					
					set theType to (type of theRef) as text
					
					set theID to id of theRef
					set theAbstract to abstract of theRef
					set theAuthors to authors of theRef
					set theEditor to editors of theRef
					set theTitle to title of theRef
					set theAbstract to abstract of theRef
					set thePublisher to publisher of theRef
					set theLocation to location of theRef
					set theVolume to volume of theRef
					set theKeywords to keyword names of theRef
					set theKeywords2 to keywords of theRef
					set theDate2 to publication date string of theRef
					set theURL2 to url of theRef
					set theISBN to isbn of theRef
					set theDOI to doi of theRef
					set theTranslator to user3 of theRef
					set theBibkey to user1 of theRef
					set thePages to pages of theRef
					set theIssue to volume of theRef
					set theNotes to notes of theRef
					set theLanguage to language of theRef
					set theAttachments to attachments of theRef
					set theJournal to journal of theRef
					set theShortTitle to short title of theRef
					set theFormattedReference to format theRef using "ABNT.fmt"
					set theNameRec to theAuthors & " " & theDate2 & " " & theTitle
				end tell
			end tell
			
			
			try
				
				set theRecord to the content record of think window 1
				if theRecord is {} then error "Nothing selected in DEVONThink Pro"
				set theURL2 to ("bookends://sonnysoftware.com/" & theID) as text
				set the URL of theRecord to theURL2
				set the name of theRecord to theShortTitle
				set the aliases of theRecord to theBibkey
				
				try
					if theAuthors does not contain {"Arisóteles" or "Platão"} then
						set the name of theRecord to theNameRec
					end if
				end try
				--if the tags of theRecord is not "" then
				try
					set {od, AppleScript's text item delimiters} to {AppleScript's text item delimiters, ASCII character 10}
					set BEKeywords to tags of theRecord
					set BEKeywords to (BEKeywords as string)
				end try
				--else if the tags of theRecord is "" then
				--	set the tags of theRecord to theKeywords
				--end if
				
				
				add custom meta data ("bookends://sonnysoftware.com/" & theID) for "link" to theRecord
				add custom meta data theAuthors for "authors" to theRecord
				add custom meta data theEditor for "editor" to theRecord
				add custom meta data theTranslator for "translator" to theRecord
				add custom meta data theTitle for "title" to theRecord
				add custom meta data thePublisher for "publisher" to theRecord
				add custom meta data theLocation for "place" to theRecord
				add custom meta data theVolume for "volume" to theRecord
				add custom meta data theDate2 for "date" to theRecord
				add custom meta data theURL2 for "link" to theRecord
				add custom meta data thePages for "page" to theRecord
				add custom meta data theAbstract for "abstract" to theRecord
				add custom meta data theBibkey for "bibkey" to theRecord
				add custom meta data theDOI for "doi" to theRecord
				add custom meta data theISBN for "is?n" to theRecord
				add custom meta data theJournal for "sertitle" to theRecord
				add custom meta data theJournal for "journal" to theRecord
				add custom meta data theIssue for "issue" to theRecord
				add custom meta data theNotes for "notas" to theRecord
				add custom meta data theFormattedReference for "Reference" to theRecord
				
				--add custom meta data theKeywords2 for "keywords" to theRecord
				if theType is "1" then set theType to "Artwork"
				if theType is "2" then set theType to "Book"
				if theType is "3" then set theType to "Book Chapter"
				if theType is "4" then set theType to "Conference Proceedings"
				if theType is "5" then set theType to "Dissertation"
				if theType is "6" then set theType to "Edited Book"
				if theType is "7" then set theType to "Editorial"
				if theType is "8" then set theType to "In Press"
				if theType is "9" then set theType to "Journal Article"
				if theType is "10" then set theType to "Letter"
				if theType is "11" then set theType to "Map"
				if theType is "12" then set theType to "Newspaper Article"
				if theType is "13" then set theType to "Patent"
				if theType is "14" then set theType to "Personal communication"
				if theType is "15" then set theType to "Review"
				if theType is "16" then set theType to "Internet"
				
				
				set theURL to ""
				set theURL to reference URL of theRecord
			end try
			
			-- Set URL to DEVONThink in Bookends
			
			tell application "Bookends"
				tell front library window
					set url of publication item id theID to theURL
					--if the keywords of theRef is "" then 
					set the keywords of theRef to BEKeywords as string
					
				end tell
			end tell
			
		end repeat
	end tell
end performSmartRule


```
