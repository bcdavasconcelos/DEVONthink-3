# DT find ISBN and add to Bookends

```applescript


use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions

-- property thePattern : "(\\d{1,3} (U\\.S\\.|S\\. ?Ct\\.|F\\.[23]?d?|F\\. ?App\\'?x|F\\. ?Supp\\. ?2?d?) \\d{1,3} \\(\\d{4}\\))|[1290]\\d{2} (WL|LEXIS) \\d{5,6}"
property thePattern : "ISBN (.+)"
property theMDField : "is?n"

tell application id "DNtp"
	-- set theRecords to the selection
	-- repeat with theRecord in theRecords
	
	set theRecord to (content record of think window 1)
	set theText to the plain text of theRecord
	set theISBN to regex search once theText search pattern thePattern replace template "$1"
	add custom meta data theISBN for theMDField to theRecord
	
	
	tell application "Bookends"
		set pubList to quick add {theISBN}
	end tell
	
end tell



```
