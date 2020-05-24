# All records in database


```applescript

use AppleScript version "2.4" -- Yosemite (10.10) or later
use script "RegexAndStuffLib"
use scripting additions


tell application id "DNtp"
	-- set theRecords to the selection
	-- repeat with theRecord in theRecords
	
	set theRecord to (content record of think window 1)
	set theDB to database of theRecord
	
	set theTagEntries to {}
	set theTags to get name of tag group of theDB
	set theTags to my sortlist(theTags)
	
	repeat with theTag in theTags
		--		set theTag to item 36 of theTags
		--	set theRecords to search "tag: " & theTag in theDB
		set theRecords to lookup records with tags {theTag} in theDB
		
		set theEntries to {}
		repeat with theRecord in theRecords
			--		set theRecord to item 2 of theRecords
			set theName to the name of theRecord
			set theComment to get custom meta data for "log" from theRecord default value ""
			set theEntry to "* " & theName
			
			if theComment is not "" then set theEntry to theEntry & " - " & theComment & linefeed
			if theComment is "" then set theEntry to theEntry & linefeed
			
			
			set theEntries to theEntries & theEntry
			
		end repeat
		
		set theEntries to my sortlist(theEntries)
		set theTagEntry to "## " & theTag & linefeed & linefeed & theEntries & linefeed & linefeed
		set theTagEntries to theTagEntries & theTagEntry
		
	end repeat
	-- 
	-- set theRecords to search "kind:markdown item:!tagged scope:Corpora"
	--	
	--	set theEntries to 
	----repeat with theRecord in theRecords
	----		-- set theRecord to item 2 of theRecords
	----		set theName to the name of theRecord
	----		set theComment to get custom meta data for "log" from theRecord default value ""
	----		set theEntry to "* " & theName
	----		
	----		if theComment is not "" then set theEntry to theEntry & " - " & theComment & linefeed
	----		if theComment is "" then set theEntry to theEntry & linefeed
	----	end repeat
	--	
	--	set theEntries to my sortlist(theEntries)
	--)
	--	set theTagEntry to "## " & "Not tagged" & linefeed & linefeed & theEntries & linefeed & linefeed --d
	--	set theTagEntries to theTagEntries & theTagEntry
	
	set theMDText to "tags: #Index" & linefeed & linefeed & "# Database index by tags" & linefeed & linefeed & theTagEntries
	
	set theResult to create record with {name:"Index", type:markdown, content:theMDText} in theDB
	
	open tab for record theResult in think window 1
	
end tell

on sortlist(theList)
	set theIndexList to {}
	set theSortedList to {}
	repeat (length of theList) times
		set theLowItem to ""
		repeat with a from 1 to (length of theList)
			if a is not in theIndexList then
				set theCurrentItem to item a of theList as text
				if theLowItem is "" then
					set theLowItem to theCurrentItem
					set theLowItemIndex to a
				else if theCurrentItem comes before theLowItem then
					set theLowItem to theCurrentItem
					set theLowItemIndex to a
				end if
			end if
		end repeat
		set end of theSortedList to theLowItem
		set end of theIndexList to theLowItemIndex
	end repeat
	return theSortedList
end sortlist

```
