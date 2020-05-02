# Extract PDF table of contents
It is often useful to keep the table of contents of a book (with the links to each section) along with the bibliographic reference and annotations. This script with extract the PDF table of contents along with other custom metadata from the record into a markdown file. See [this thread][1] for more information.  

**Additional dependency:**  
* [MuPDF][2]

```applescript
use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions

tell application id "DNtp"
	set theRecords to the selection
	repeat with theRecord in theRecords
		
		-- Propriedades
		set {theName, thePath, theRefURL, theURL} to {the name, the path, the reference URL, the URL} of theRecord
		
		set theScript to "/usr/local/bin/mutool show " & quoted form of thePath & " outline"
		tell me to set theToc to do shell script theScript
		
		set theToc to my replaceText(theToc, "\\n", " ")
		set theToc to regex change theToc search pattern "	#(.*?),.+" replace template " (page $1)"
		set theToc to regex change theToc search pattern "\" \\(" replace template "]("
		set theToc to regex change theToc search pattern "\"" replace template "["
		set theToc to regex change theToc search pattern "[\\||\\+|-]" replace template ""
		set theToc to regex change theToc search pattern "				" replace template "4 "
		set theToc to regex change theToc search pattern "			" replace template "3 "
		set theToc to regex change theToc search pattern "		" replace template "2 "
		set theToc to regex change theToc search pattern "	" replace template "* "
		set theToc to regex change theToc search pattern "^2" replace template "    *"
		set theToc to regex change theToc search pattern "^3" replace template "        *"
		set theToc to regex change theToc search pattern "^4" replace template "            *"
		
		try
			set oldDelims to AppleScript's text item delimiters
			set AppleScript's text item delimiters to {return}
			set theLines to every text item of theToc
			set AppleScript's text item delimiters to oldDelims
		on error
			set AppleScript's text item delimiters to oldDelims
		end try
		
		set theTableofContents to {}
		repeat with theLine in theLines
			set thePage to regex search theLine search pattern "\\(page ([0-9]{1,4})\\)" replace template "$1"
			set thePage to item 1 of thePage as text
			set thePage to thePage - 1
			set thePage to "(" & theRefURL & "?page=" & thePage & ")"
			set theRef to regex change theLine search pattern "(\\(page [0-9]{1,4}\\))" replace template "page"
			set theRef to my replaceText(theRef, "page", thePage)
			set theTableofContents to theTableofContents & theRef & return
		end repeat
		
		

		
		set theRecName to theName & " - Table of Contents"
		set theReference to get custom meta data for "Reference" from theRecord default value ""
		set theBibkey to get custom meta data for "Bibkey" from theRecord default value ""
		if theBibkey is "" then set theBibkey to the aliases of theRecord
		set theReference to "`[#" & theBibkey & "]: " & theReference & "`"
		set theMD to "# " & theName & return & return & theReference & return & return & "## Table of Contents" & return & return & theTableofContents
		set theResult to create record with {name:theRecName, type:markdown, content:theMD}
		set the tags of theResult to "_\\Bib Toc"
		
		set theReferenceURL to the reference URL of theResult
		set the URL of theResult to theURL
		add custom meta data theReferenceURL for "Link2" to theRecord
		open tab for record theResult
		
		
	end repeat
end tell


on replaceText(theString, old, new)
	set {TID, text item delimiters} to {text item delimiters, old}
	set theStringItems to text items of theString
	set text item delimiters to new
	set theString to theStringItems as text
	set text item delimiters to TID
	return theString
end replaceText

```

[1]:	https://discourse.devontechnologies.com/t/applescript-to-extract-the-pdfs-toc-into-a-markdown-file/55078?u=bernardo_v
[2]:	https://weibeld.net/pdf/extract_pdf_toc.html

#Applescript