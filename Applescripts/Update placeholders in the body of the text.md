# Update placeholders in the body of the text

**Warning:** *This will change the UUID of the record and by extension the reference URL.*

```applescript
-- Update placeholders in the record
-- by bcdavasconcelos
-- 2020-05-26-17-43-08
-- Warning: This will change the UUID of the record!

tell application id "DNtp"
	
	set theSource to (content record of think window 1)
	set theGroup to parent 1 of theSource
	set thePath to the (path of theSource)
	
	tell application "Finder"
		
		set theSourceFile to get POSIX file thePath as text
		set theSourceFile to theSourceFile as alias
		set HomePath to home as text
		set theNewFile to duplicate file theSourceFile to HomePath with replacing
		set theNewFile to theNewFile as alias
		
	end tell
	
	
	set theUpdatedRecord to import theNewFile to theGroup placeholders {|%example%|:theExample, |%citationTranslator%|:theTranslator}
	
	delete record theSource
	
end tell

tell application "Finder" to delete theNewFile



```


#Applescript #DEVONthink