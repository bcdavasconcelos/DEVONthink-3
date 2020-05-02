# Retrieve Bekker numbering

All of Aristotle's works are numbered according to the 1831 edition by german scholar Immanuel Bekker. This script will extract numbering matching the pattern `PAGE(1-1462) COLUMN(a|b) LINE (1-45)`, which translates into regex pattern `\d{1,4}[a|b]\d{1,2}`, and add them as aliases to the record. This is exceptionally useful if you are using automatic wiki links, as any occurrence of a reference will take you directly to the referenced work. For more information on this, see this thread [How I use Wiki-Links and Aliases (pt.2) ][1]. The smart-rule can be downloaded [here][2].

```applescript
use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions

on performSmartRule(theRecords)
	
	tell application id "DNtp"
		--set theRecords to the selection
		repeat with theRecord in theRecords
			
			set theText to plain text of theRecord
			set theResult to regex search theText search pattern "(\\d{1,4}[a|b]\\d{1,2})"
			set theString to my replaceText(theResult, return, ", ")
			set theString to theString & ", "
			set the aliases of theRecord to theString
			display notification "Bekker lines successfully added as aliases"
			
		end repeat
	end tell
	
end performSmartRule
-- Handlers section

on replaceText(theString, old, new)
	set {TID, text item delimiters} to {text item delimiters, old}
	set theStringItems to text items of theString
	set text item delimiters to new
	set theString to theStringItems as text
	set text item delimiters to TID
	return theString
end replaceText

```

[1]:	https://discourse.devontechnologies.com/t/how-i-use-wiki-links-and-aliases-pt-2/47873
[2]:	https://bcdav.blot.im/DT3-Smart-rules

#Applescript