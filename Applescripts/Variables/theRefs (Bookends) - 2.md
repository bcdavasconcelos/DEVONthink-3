# theRefs (Bookends)

```applescript
tell application "Bookends"
	tell front library window
		
		
		-- Get selected publication 
		set theRefs to selected publication items
		set theRefsNo to count of theRefs
		set theRef to first item of theRefs
		
		-- Error messages	
		if theRefsNo is greater than 1 then error "Select only one item"
		if theRefs is {} then error "Nothing selected in Bookends"
		
		set {theID, theAbstract, theAuthors, theEditors, theTitle, theAbstract, thePublisher, theLocation, theVolume, theKeywordNames, theKeywords, theDate, theURL, theISBN, theDOI, theTranslator, theBibkey, thePages, theVolume, theNotes, theLanguage, theAttachments, theJournal, theShortTitle} to {id, abstract, authors, editors, title, abstract, publisher, location, volume, keyword names, keywords, publication date string, url, isbn, doi, user3, user1, pages, volume, notes, language, attachments, journal, short title} of theRef
		
		set theType to type of theRef as text
		if theType is "1" then set thePubType to "Artwork"
		if theType is "2" then set thePubType to "Book"
		if theType is "3" then set thePubType to "Book Chapter"
		if theType is "4" then set thePubType to "Conference Proceedings"
		if theType is "5" then set thePubType to "Dissertation"
		if theType is "6" then set thePubType to "Edited Book"
		if theType is "7" then set thePubType to "Editorial"
		if theType is "8" then set thePubType to "In Press"
		if theType is "9" then set thePubType to "Journal Article"
		if theType is "10" then set thePubType to "Letter"
		if theType is "11" then set thePubType to "Map"
		if theType is "12" then set thePubType to "Newspaper Article"
		if theType is "13" then set thePubType to "Patent"
		if theType is "14" then set thePubType to "Personal communication"
		if theType is "15" then set thePubType to "Review"
		if theType is "16" then set thePubType to "Internet"
		
		set theFormattedReference to format theRef using "ABNT.fmt"
		set theABNTName to theAuthors & " " & theDate & " " & theTitle
		set theRefURL to ("bookends://sonnysoftware.com/" & theID) as text
		
	end tell
	
end tell

```


#Snippets #Bookends