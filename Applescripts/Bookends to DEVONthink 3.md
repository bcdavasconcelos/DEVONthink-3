# Bookends to DEVONthink 3

Script for carrying over metadata from BE into DT.

```applescript

use AppleScript version "2.4" -- Yosemite (10.10) or later
use scripting additions

-- BCDAVASCONCELOS 2020-05-17-14-17-34

set theAnswer to ""
set theOptions to {"Import metadata", "Export metadata", "Cancel"}
set theAnswer to the button returned of (display dialog "What is your wish, master?" buttons theOptions default button 1)

if theAnswer is "Import metadata" then
	
	tell application "Bookends"
		tell front library window
			
			-- Get selected publication 
			set theRefs to selected publication items
			set theRefsNo to count of theRefs
			set theRef to first item of theRefs
			
			-- Error messages	
			if theRefsNo is greater than 1 then error "Select only one item"
			if theRefs is {} then error "Nothing selected in Bookends"
			
			-- Get properties of selected reference
			set theType to type of theRef as text
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
	
	tell application id "DNtp"
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
	end tell
	
	-- Set URL to DEVONThink in Bookends
	
	tell application "Bookends"
		tell front library window
			set url of publication item id theID to theURL
			--if the keywords of theRef is "" then 
			set the keywords of theRef to BEKeywords as string
			
		end tell
		
	end tell
	
else if theAnswer is "Export metadata" then
	
	----------- DEVONthink 3 -----------
	
	tell application id "DNtp"
		
		set theRecord to the content record of think window 1
		if theRecord is {} then error "Nothing selected in DEVONThink Pro"
		
		set thePriority to get custom meta data for "Priority" from theRecord default value ""
		set theType to get custom meta data for "Type" from theRecord default value ""
		set theAuthors to get custom meta data for "Authors" from theRecord default value ""
		set theEditor to get custom meta data for "Editor" from theRecord default value ""
		set theTranslator to get custom meta data for "Translator" from theRecord default value ""
		set theTitle to get custom meta data for "Title" from theRecord default value ""
		set theSerTitle to get custom meta data for "SerTitle" from theRecord default value ""
		set theVolume to get custom meta data for "Volume" from theRecord default value ""
		set thePlace to get custom meta data for "Place" from theRecord default value ""
		set thePublisher to get custom meta data for "Publisher" from theRecord default value ""
		set theDate to get custom meta data for "Date" from theRecord default value ""
		set theJournal to get custom meta data for "Journal" from theRecord default value ""
		set theIssue to get custom meta data for "Issue" from theRecord default value ""
		set thePage to get custom meta data for "Page" from theRecord default value ""
		set theBibkey to get custom meta data for "Bibkey" from theRecord default value ""
		set theKeywords to get custom meta data for "Keywords" from theRecord default value ""
		set theReference to get custom meta data for "Reference" from theRecord default value ""
		set theDOI to get custom meta data for "DOI" from theRecord default value ""
		set theISBN to get custom meta data for "Is?n" from theRecord default value ""
		set theNotas to get custom meta data for "Notas" from theRecord default value ""
		set theAbstract to get custom meta data for "Abstract" from theRecord default value ""
		set theRating to get custom meta data for "Rating" from theRecord default value ""
		
		set theFilename to filename of theRecord
		set theDT3id to the uuid of theRecord
		set theShortName to the name of theRecord
		
		
		-- Tags do record → Keywords do Bookends
		try
			set {od, AppleScript's text item delimiters} to {AppleScript's text item delimiters, ASCII character 10}
			set BEKeywords to tags of theRecord
			set BEKeywords to (BEKeywords as string)
		end try
		
		
		-- Tradução do tipo na numeração correta para o Bookends
		try
			if theType is "Article" then set BEType to "9"
			if theType is "Book" then set BEType to "2"
			if theType is "Book Chapter" then set BEType to "3"
			if theType is "Edited Book" then set BEType to "6"
			if theType is "Dissertation" then set BEType to "5"
			if theType is "Review" then set BEType to "15"
			if theType is "Internet" then set BEType to "16"
			if theType is "Conference Proceedings" then set BEType to "4"
		end try
		
	end tell
	
	------------------------------------------- Bookends -------------------------------------------
	
	tell application "Bookends"
		tell front library window
			make new publication item with properties {type:BEType, title:theTitle, authors:theAuthors, publication date string:theDate, attachments:theFilename, citekey:theBibkey, editors:theEditor, doi:theDOI, user3:theTranslator, location:thePlace, publisher:thePublisher, journal:theJournal, volume:theVolume, pages:thePage, abstract:theAbstract, isbn:theISBN, url:("x-devonthink-item://" & theDT3id), short title:theShortName, keywords:BEKeywords}
			set theID to id of last publication item
			set theFormattedReference to format of last publication item using "ABNT.fmt"
			--set thePath to the path of attachment item 1 of last publication item
			set theAttachments to attachments of last publication item
			if theAttachments is equal to "" then error "There is no file attached to the Bookends reference"
			if theAttachments is not equal to "" then
				set theFile to the first attachment item of last publication item
				set thePath to the path of theFile
				if theFile is "" then error "The file is missing from the Bookends reference"
				
				-- Set the metadata fields for PDF. Need to install exiftool (https://www.sno.phy.queensu.ca/~phil/exiftool/install.html)
				do shell script "/usr/local/bin/exiftool -title=" & quoted form of theTitle & " -author=" & quoted form of theAuthors & " -subject=" & quoted form of theJournal & " -overwrite_original " & quoted form of thePath
			end if
		end tell
	end tell
	
	
	
	
	---------------- DEVONthink 3 -------------------
	tell application id "DNtp"
		
		set BElink to ("bookends://sonnysoftware.com/" & theID) as text
		set the URL of theRecord to BElink
		add custom meta data BElink for "link" to theRecord
		add custom meta data theFormattedReference for "Reference" to theRecord
		
	end tell
	
end if


```


#Applescript #DEVONthink #Bookends