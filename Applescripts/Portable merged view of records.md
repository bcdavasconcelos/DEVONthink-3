# Portable merged view of records
For more information, see [this thread][1].

```applescript
use AppleScript version "2.4" -- Yosemite (10.10) or later
use scripting additions
use script "Dialog Toolkit Plus" version "1.1.0"

-- by ngan 2020.04.28
-- v1b9 add progress indicator
-- v1b8 add an internal option to use [[...]] or reference url for the name of each file section
-- v1b7 use a different getAllChildren() handler that can include only a specific set of file formats
-- v1b6 add option to include pdf in MV in the form of link
-- v1b5 use QsortRecsByName,QsortRecsByMod,QsortRecsByCreate for speed
-- v1b4 add metadata at the heading of MV file
-- v1b3 working version: add selection dialog box and refresable merged view

property MVGpLocation : "/MergeView"
property MVNameFormat : "YMDHNS" -- yr mon day hr min sec
property sectionLinkFormat : "U" -- "W" for [[...]] style link, "U" for DT-URL link 
property showLastRefresh : true -- if true then show the last refeshed time at the header, if false hide the time in metadata

-- don't change these properties
property rtfHeader : "<!DOCTYPE HTML PUBLIC \"-//W3C//DTD HTML 4.0 Transitional//EN\" \"http://www.w3.org/TR/REC-html40/loose.dtd\">"
property sortByOptLabel : {"Default", "Modification Date", "Creation Date", "Name"}

global useSelOrGp, theGps, theGpsUUID, exSubGp, sortBy, sortOrder, addTags, addName, addSeperator, includePDF
global theRecords, theRecordsCount, theMVContent, theRecordName, eachRecordTags, theSeparator
global newMV, theMV, theMVName, theMVContent, theRTFSource
global rtfCount, mdCount, pdfCount
global gpPopupList, gpPopupLabel

-- Main --
tell application id "DNtp"
	-- check whether the script is to create a new MV or refresh MV
	
	
	if class of think window 1 is viewer window then
		show progress indicator "Creating MergeView ..."
		if (count of selection) is not 0 then
			-- call dialog box to get parameters
			set newMV to true
			set theParameters to my getMVOpt()
			if theParameters is "Cancel" then
				hide progress indicator
				return
			end if
		else
			
			display alert "Select at least an item or a group"
			hide progress indicator
			return
		end if
	else if class of think window 1 is document window then
		show progress indicator "Refreshing MergeView ..."
		set {theMV} to item 1 of {selection}
		set theMVcomment to comment of theMV
		
		if (theMVcomment is "") or (texts 1 thru 2 of theMVcomment is not "MV") then
			display alert "This is not a merged file" giving up after 2
			return
		else
			set newMV to false
			set theParameters to my strToList(theMVcomment, ",")
			-- get praameters from comment of the file
			set useSelOrGp to "S"
			set sortBy to theParameters's item 2
			set sortOrder to theParameters's item 3
			if theParameters's item 4 is "True" then
				set addTags to true
			else
				set addTags to false
			end if
			if theParameters's item 5 is "True" then
				set addName to true
			else
				set addName to false
			end if
			if theParameters's item 6 is "True" then
				set addSeperator to true
			else
				set addSeperator to false
			end if
			if theParameters's item 7 is "True" then
				set includePDF to true
			else
				set includePDF to false
			end if
			set theGpsUUID to items 8 thru -1 of theParameters
			set theGps to {}
			repeat with each in theGpsUUID
				set end of theGps to get record with uuid each
			end repeat
		end if
	end if
	
	
	-- prepare records for merged view	for selection w/o pdf
	
	if includePDF then
		set theRecords to my getAllChildren(theGps, {rtfd, rtf, markdown, PDF document})
	else
		set theRecords to my getAllChildren(theGps, {rtfd, rtf, markdown})
	end if
	set {rtfCount, mdCount, pdfCount} to {0, 0, 0}
	set theRecordsCount to length of theRecords
	if sortBy is "N" then -- sort by name option is checked
		my QsortRecsByName(theRecords, 1, theRecordsCount)
		if sortOrder is "D" then set theRecords to reverse of theRecords
		-- set theRecords to my sortRecsByName(theRecords, sortOrder)
	else if sortBy is "M" then -- sort by modification date option is checked
		my QsortRecsByMod(theRecords, 1, theRecordsCount)
		if sortOrder is "D" then set theRecords to reverse of theRecords
		--set theRecords to my sortRecsByMod(theRecords, sortOrder)
	else if sortBy is "C" then -- sort by creation date option is checked
		my QsortRecsByCreate(theRecords, 1, theRecordsCount)
		if sortOrder is "D" then set theRecords to reverse of theRecords
		--set theRecords to my sortRecsByCreate(theRecords, sortOrder)
	end if
	
	
	-- prepare contents
	set theMVContent to ""
	
	set stepCount to 1
	repeat with each in theRecords
		step progress indicator "Merging file ..." & stepCount & "/" & theRecordsCount
		-- Set name at section's beginning
		set theRecordName to ""
		if addName then
			if sectionLinkFormat is "W" then
				-- use [[..]] style wiki link
				if sortBy is "N" or sortBy is "D" then
					set theRecordName to "###### [[" & each's name & "]]"
				else if sortBy is "M" then
					set theRecordName to "###### [[" & each's name & "]]" & "    Modified: " & my getDateString(each's modification date, "YMD", ".")
				else if sortBy is "C" then
					set theRecordName to "###### [[" & each's name & "]]" & "    Created: " & my getDateString(each's creation date, "YMD", ".")
				end if
			else
				-- use reference URL link and set to page 1
				if sortBy is "N" or sortBy is "D" then
					set theRecordName to "###### [" & each's name & "](" & each's reference URL & "?page=0 )"
				else if sortBy is "M" then
					set theRecordName to "###### [" & each's name & "    Modified: " & my getDateString(each's modification date, "YMD", ".") & "](" & each's reference URL & "?page=0 )"
				else if sortBy is "C" then
					set theRecordName to "###### [" & each's name & "    Created: " & my getDateString(each's creation date, "YMD", ".") & "](" & each's reference URL & "?page=o)"
				end if
				
			end if
		end if
		
		-- Set tags at section's ending	
		set eachRecordTags to ""
		
		if addTags then
			if sectionLinkFormat is "W" then
				--get the tags of each record and convert them into a string of wikilink in [[]] format
				set eachRecordTags to name of (parents of each whose tag type is ordinary tag)
				repeat with i from 1 to length of eachRecordTags
					set eachRecordTags's item i to "[[" & (eachRecordTags's item i) & "]]  "
				end repeat
				set eachRecordTags to my listToStr(my sortlist(eachRecordTags), "  ")
				
			else
				-- get reference url and convert them into md link
				set eachRecordTags to (parents of each whose tag type is ordinary tag)
				set ll to {}
				repeat with i from 1 to length of eachRecordTags
					set end of ll to "[" & name of (eachRecordTags's item i) & "](" & reference URL of (eachRecordTags's item i) & ")"
				end repeat
				set eachRecordTags to "**#:** " & my listToStr(my sortlist(ll), "  ")
				
			end if
		end if
		
		--Set separator
		if addSeperator then
			set theSeparator to "---"
		else
			set theSeparator to ""
		end if
		
		--prepare the content of theMV
		if type of each is in {rtf, rtfd} then
			set theRTFSource to my findAndReplaceInText(source of each, rtfHeader, "")
			set theMVContent to theMVContent & theRecordName & return & theRTFSource & return & return & eachRecordTags & return & return & theSeparator & return & return
			set rtfCount to rtfCount + 1
			set stepCount to stepCount + 1
		else if type of each is markdown then
			set theMVContent to theMVContent & theRecordName & return & return & plain text of each & return & return & return & eachRecordTags & return & return & theSeparator & return & return & return
			set mdCount to mdCount + 1
			set stepCount to stepCount + 1
		else if type of each is PDF document and includePDF then
			set pdfLink to ""
			if addName is false then set pdfLink to "[" & each's name & "](" & each's reference URL & ")"
			set theMVContent to theMVContent & theRecordName & return & return & pdfLink & return & return & return & eachRecordTags & return & return & theSeparator & return & return & return
			set pdfCount to pdfCount + 1
			set stepCount to stepCount + 1
		else
			set theRecordsCount to theRecordsCount - 1
			set stepCount to stepCount + 1
		end if
		
	end repeat
	
	--add metadate to the heading of theMV
	if showLastRefresh then
		set theMVMetadata to "Total records: " & theRecordsCount & return & "RTF/RTFD files: " & rtfCount & return & "Markdown files: " & mdCount & return & "PDF files: " & pdfCount & return & return & "Last refreshed: " & (my getDateString(current date, MVNameFormat, ".")) & return & return & theSeparator
	else
		set theMVMetadata to "Last refreshed: " & (my getDateString(current date, MVNameFormat, ".")) & return & "Total records: " & theRecordsCount & return & "RTF/RTFD files: " & rtfCount & return & "Markdown files: " & mdCount & return & "PDF files: " & pdfCount
	end if
	
	set theMVContent to theMVMetadata & return & return & theMVContent
	
	if newMV then
		-- name and create theMV
		set theMVName to "MV - " & (my getDateString(current date, MVNameFormat, "."))
		set theMV to create record with {name:theMVName, source:theMVContent, type:markdown} in (get record at MVGpLocation)
		-- save parameters to comment of MV	
		set comment of theMV to my listToStr({"MV", sortBy, sortOrder, addTags, addName, addSeperator, includePDF, theGpsUUID}, ",")
		open tab for record theMV
		hide progress indicator
	else
		-- refresh theMV
		set the plain text of theMV to theMVContent
		display alert "The MV file is refreshed, check metadate for info" giving up after 1
		hide progress indicator
	end if
	
end tell
-- end of main program 


--script specific handlers
on getMVOpt()
	local addNameOptLabel, addTagOptLabel
	set {gpPopupList, gpPopupLabel} to my prepareGpsChoice()
	if sectionLinkFormat is "W" then
		set addNameOptLabel to "Add [[Name]]"
		set addTagOptLabel to "Add [[Tags]]"
	else
		set addNameOptLabel to "Add Name's Link"
		set addTagOptLabel to "Add Tag's Link"
	end if
	
	
	-- "Dialog Toolkit Plus" coding
	set accViewWidth to 300
	set theTop to 8
	set {theButtons, minWidth} to create buttons {"Cancel", "OK"} default button 2 given «class btns»:2
	if minWidth > accViewWidth then set accViewWidth to minWidth
	
	set {theRule3, theTop} to create rule (theTop - 8) rule width accViewWidth
	set {addSeperatorOpt, theTop, newWidth} to create checkbox "Add seperator" bottom (theTop + 8) max width accViewWidth / 3 - 8 with initial state -- comment out "with initial state" if default==not checked
	
	set {addNameOpt, theTop, newWidth} to create checkbox addNameOptLabel bottom (theTop + 8) max width accViewWidth / 3 - 8 with initial state -- comment out "with initial state" if default==not checked
	set {addTagOpt, theTop, newWidth} to create checkbox addTagOptLabel bottom (theTop + 8) max width accViewWidth / 3 - 8 with initial state -- comment out "with initial state" if default==not checked
	
	set {theRule2, theTop} to create rule (theTop + 12) rule width accViewWidth
	set {sortOrderOpt, theTop, newWidth} to create checkbox "Descending (Uncheck = Ascending)" bottom (theTop + 8) max width accViewWidth / 3 - 8 with initial state -- comment out "with initial state" if default==not checked
	
	set {sortByOpt, theTop} to create matrix sortByOptLabel bottom (theTop + 8) max width accViewWidth initial choice 1
	set {theRule1, theTop} to create rule (theTop + 12) rule width accViewWidth
	set {gpPopup, theTop} to create popup gpPopupLabel bottom (theTop + 8) popup width accViewWidth initial choice 1
	set {selOpt, theTop} to create matrix {"Use selection", "Use parent group or smart group"} bottom (theTop + 8) max width accViewWidth initial choice 1
	set {theRule0, theTop} to create rule (theTop + 12) rule width accViewWidth
	set {addPDFLinkOpt, theTop, newWidth} to create checkbox "Include PDF files as link in MV" bottom (theTop + 8) max width accViewWidth / 3 - 8 --with initial state 	
	set {boldLabel, theTop} to create label "Merge Markdown, RTF, RTFD files" bottom theTop + 8 max width accViewWidth control size large size
	set allControls to {theRule3, addSeperatorOpt, addNameOpt, addTagOpt, theRule2, sortOrderOpt, sortByOpt, theRule1, gpPopup, selOpt, theRule0, addPDFLinkOpt, boldLabel}
	set {buttonName, controlsResults} to display enhanced window "MergeView" acc view width accViewWidth acc view height theTop acc view controls allControls buttons theButtons with align cancel button
	
	
	--  end of "Dialog Toolkit Plus" coding
	if buttonName is not "Cancel" then
		tell application id "DNtp"
			if controlsResults's item 10 is "Use parent group or smart group" then
				-- get the selected gps
				set useSelOrGp to "G"
				set theGps to item (my indexOfOneItem(controlsResults's item 9, gpPopupLabel)) of gpPopupList
			else
				-- get the selection
				set useSelOrGp to "S"
				set theGps to selection as list
				
			end if
			
			set theGpsUUID to {}
			repeat with each in theGps
				set end of theGpsUUID to (get uuid of each)
			end repeat
			
			set sortBy to character 1 of (controlsResults's item 7)
			if controlsResults's item 6 then
				set sortOrder to "D"
			else
				set sortOrder to "A"
			end if
			set addTags to controlsResults's item 4
			set addName to controlsResults's item 3
			set addSeperator to controlsResults's item 2
			set includePDF to controlsResults's item 12
		end tell
		
		return {useSelOrGp, theGps, theGpsUUID, sortBy, sortOrder, addTags, addName, addSeperator, includePDF}
	else
		return "Cancel"
	end if
end getMVOpt
on prepareGpsChoice()
	local lg, lgn, a, b
	set lgn to {}
	tell application id "DNtp"
		set lg to every smart group of current database
		-- set lg to my sortRecsByName(lg, "A")
		set lgre to length of lg
		if lg is not {} then my QsortRecsByName(lg, 1, lgre)
		if name of current group is not equal to name of current database then set the beginning of lg to current group
		if lg ≠ {} then
			repeat with each in lg
				set end of lgn to name of each & "      «" & location of each & "»"
			end repeat
			return {lg, lgn}
		else
			return {{}, {"Parent is database and no smart group"}}
		end if
	end tell
end prepareGpsChoice
on getAllChildren(theseGps, theformat)
	local l
	set l to {}
	tell application id "DNtp"
		repeat with each in theseGps
			if (each's type as string) is in {"group", "smart group"} then
				set l to l & my getAllChildren(children of each, theformat)
			else
				if each's type is in theformat then set end of l to each's item 1
			end if
		end repeat
	end tell
	return l
end getAllChildren
on QsortRecsByMod(array, leftEnd, rightEnd)
	-- based on Hoare's QuickSort Algorithm
	-- modified by ngan for records sorting in DT
	tell application id "DNtp"
		script a
			property l : array
		end script
		set {i, j} to {leftEnd, rightEnd}
		set v to modification date of item ((leftEnd + rightEnd) div 2) of a's l -- pivot in the middle
		repeat while (j > i)
			repeat while (modification date of (item i of a's l) < v)
				set i to i + 1
			end repeat
			repeat while (modification date of (item j of a's l) > v)
				set j to j - 1
			end repeat
			if (not i > j) then
				tell a's l to set {item i, item j} to {item j, item i} -- swap
				set {i, j} to {i + 1, j - 1}
			end if
		end repeat
		if (leftEnd < j) then my QsortRecsByMod(a's l, leftEnd, j)
		if (rightEnd > i) then my QsortRecsByMod(a's l, i, rightEnd)
	end tell
end QsortRecsByMod
on QsortRecsByName(array, leftEnd, rightEnd)
	-- based on Hoare's QuickSort Algorithm
	-- modified by ngan for records sorting in DT
	tell application id "DNtp"
		script a
			property l : array
		end script
		set {i, j} to {leftEnd, rightEnd}
		set v to name of item ((leftEnd + rightEnd) div 2) of a's l -- pivot in the middle
		repeat while (j > i)
			repeat while (name of (item i of a's l) < v)
				set i to i + 1
			end repeat
			repeat while (name of (item j of a's l) > v)
				set j to j - 1
			end repeat
			if (not i > j) then
				tell a's l to set {item i, item j} to {item j, item i} -- swap
				set {i, j} to {i + 1, j - 1}
			end if
		end repeat
		if (leftEnd < j) then my QsortRecsByName(a's l, leftEnd, j)
		if (rightEnd > i) then my QsortRecsByName(a's l, i, rightEnd)
	end tell
end QsortRecsByName
on QsortRecsByCreate(array, leftEnd, rightEnd)
	-- based on Hoare's QuickSort Algorithm
	-- modified by ngan for records sorting in DT
	tell application id "DNtp"
		script a
			property l : array
		end script
		set {i, j} to {leftEnd, rightEnd}
		set v to creation date of item ((leftEnd + rightEnd) div 2) of a's l -- pivot in the middle
		repeat while (j > i)
			repeat while (creation date of (item i of a's l) < v)
				set i to i + 1
			end repeat
			repeat while (creation date of (item j of a's l) > v)
				set j to j - 1
			end repeat
			if (not i > j) then
				tell a's l to set {item i, item j} to {item j, item i} -- swap
				set {i, j} to {i + 1, j - 1}
			end if
		end repeat
		if (leftEnd < j) then my QsortRecsByCreate(a's l, leftEnd, j)
		if (rightEnd > i) then my QsortRecsByCreate(a's l, i, rightEnd)
	end tell
end QsortRecsByCreate

-- utility handlers
on indexOfOneItem(theItem, theList)
	-- credits Emmanuel Levy
	set {oTIDs, AppleScript's text item delimiters} to {AppleScript's text item delimiters, return}
	set theList to return & theList & return
	set AppleScript's text item delimiters to oTIDs
	try
		
		-1 + (count (paragraphs of (text 1 thru (offset of (return & theItem & return) in theList) of theList)))
	on error
		0
	end try
end indexOfOneItem
on getDateString(theDate, theDateFormat, theSeperator)
	tell application id "DNtp"
		local y, m, d, h, n, s, T
		local lol, ds
		set lol to {{"y", ""}, {"m", ""}, {"d", ""}, {"h", ""}, {"n", ""}, {"s", ""}}
		
		set (lol's item 1)'s item 2 to get year of theDate
		set (lol's item 2)'s item 2 to my padNum((get month of theDate as integer) as string, 2)
		set (lol's item 3)'s item 2 to my padNum((get day of theDate) as string, 2)
		set T to every word of (get time string of theDate)
		set (lol's item 4)'s item 2 to T's item 1
		set (lol's item 5)'s item 2 to T's item 2
		set (lol's item 6)'s item 2 to T's item 3
	end tell
	
	set ds to {}
	set theDateFormat to (every character of theDateFormat)
	repeat with each in theDateFormat
		set ds to ds & (my lolLookup(each as string, 1, 2, lol))'s item 2
	end repeat
	
	return my listToStr(ds, theSeperator)
	
end getDateString
on padNum(lngNum, lngDigits)
	-- Credit houthakker
	set strNum to lngNum as string
	set lngGap to (lngDigits - (length of strNum))
	repeat while lngGap > 0
		set strNum to "0" & strNum
		set lngGap to lngGap - 1
	end repeat
	strNum
end padNum
on lolLookup(lookupVal, lookUpPos, getValPos, theList)
	--only for list of list with more than 1 items
	local i, j, k
	set j to lookUpPos
	set k to getValPos
	repeat with i from 1 to length of theList
		if (item j of item i of theList) is equal to lookupVal then return {i, item k of item i of theList, item i of theList}
	end repeat
	return {0, {}, {}}
end lolLookup
on findAndReplaceInText(theText, theSearchString, theReplacementString)
	set AppleScript's text item delimiters to theSearchString
	set theTextItems to every text item of theText
	set AppleScript's text item delimiters to theReplacementString
	set theText to theTextItems as string
	set AppleScript's text item delimiters to ""
	return theText
end findAndReplaceInText
on listToStr(theList, d)
	local thestr
	set {tid, text item delimiters} to {text item delimiters, d}
	set thestr to theList as text
	set text item delimiters to tid
	return thestr
end listToStr
on strToList(thestr, d)
	local theList
	set {tid, text item delimiters} to {text item delimiters, d}
	set theList to every text item of thestr
	set text item delimiters to tid
	return theList
end strToList
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

[1]:	https://discourse.devontechnologies.com/t/script-refreshable-portable-merged-view-of-files-in-mixed-formats-direct-almost-editing-of-source-files-content-dynamically-linked-to-groups-tags/55241/5

#Applescript #DEVONthink