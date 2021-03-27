# Move tab to the left


```applescript

use AppleScript version "2.4" -- Yosemite (10.10) or later
use scripting additions

-- 2021-03-27-08-38 bcdav

tell application id "DNtp"
	
	set theWin to front window
	set n to count tabs in theWin
	set theTabsBefore to tabs of theWin
	set theTab to current tab of theWin
	set thePos to my getPositionOfItemInList(theTab, theTabsBefore)
	if thePos > 1 then
		set theMainRec to the uuid of content record of the current tab of theWin
		close theTab
		
		set theTabs to tabs of theWin
		set theL to {}
		repeat with i in theTabs
			set theRecord to content record of i
			set theID to (the uuid of theRecord)
			set theL to theL & theID
		end repeat
		
		set theL to my insertItemInList(theMainRec, theL, thePos - 1)
		close every tab of theWin
		
		repeat with i in theL
			set theRec to get record with uuid i
			open tab for record theRec in theWin
		end repeat
		set theRec to get record with uuid theMainRec
		set theLink to reference URL of theRec
		tell me to do shell script "open " & theLink
	else
		beep
	end if
end tell

on getPositionOfItemInList(theItem, theList)
	repeat with a from 1 to count of theList
		if item a of theList is theItem then return a
	end repeat
	return 0
end getPositionOfItemInList

on insertItemInList(theItem, theList, thePosition)
	set theListCount to length of theList
	if thePosition is 0 then
		return false
	else if thePosition is less than 0 then
		if (thePosition * -1) is greater than theListCount + 1 then return false
	else
		if thePosition is greater than theListCount + 1 then return false
	end if
	if thePosition is less than 0 then
		if (thePosition * -1) is theListCount + 1 then
			set beginning of theList to theItem
		else
			set theList to reverse of theList
			set thePosition to (thePosition * -1)
			if thePosition is 1 then
				set beginning of theList to theItem
			else if thePosition is (theListCount + 1) then
				set end of theList to theItem
			else
				set theList to (items 1 thru (thePosition - 1) of theList) & theItem & (items thePosition thru -1 of theList)
			end if
			set theList to reverse of theList
		end if
	else
		if thePosition is 1 then
			set beginning of theList to theItem
		else if thePosition is (theListCount + 1) then
			set end of theList to theItem
		else
			set theList to (items 1 thru (thePosition - 1) of theList) & theItem & (items thePosition thru -1 of theList)
		end if
	end if
	return theList
end insertItemInList
```