# Find ISBN/DOI

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
		--		set theRecord to (content record of think window 1)
		repeat with theRecord in theRecords
			
			set theName to the name of theRecord
			set theText to the plain text of theRecord
			set theISBN to ""
			set theDOI to ""
			
			set theISBN to regex search once theText search pattern thePattern replace template "$1"
			set theDOI to regex search once theText search pattern thePattern2 replace template "$1"
			
			add custom meta data theISBN for theMDField to theRecord
			add custom meta data theDOI for theMDField2 to theRecord
			
		
			
		end repeat
	end tell
end performSmartRule



```
