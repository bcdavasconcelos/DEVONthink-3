# Open frontmost document in DEVONthink (@find author)

```applescript


tell application "MultiMarkdown Composer" to activate

set thePath to my path_to_front_document()

set theResults to my lookup_path(thePath)

tell application id "DNtp"
	try
		open window for record (item 1 of theResults)
		activate
		
	on error error_message number error_number
		if the error_number is not -128 then display alert "DEVONthink" message error_message as warning
		return
	end try
end tell


on lookup_path(thePath)
	tell application id "DNtp"
		try
			set theDatabases to databases
			set theResults to {}
			
			repeat with thisDatabase in theDatabases
				set thisDatabasesResults to lookup records with path thePath in thisDatabase
				set theResults to theResults & thisDatabasesResults
			end repeat
			
			if theResults = {} then
				display notification "Pfad nicht in offenen Datenbanken enthalten!" with title "Lookup DEVONthink Handler"
				return
			end if
			
			return theResults
			
		on error error_message number error_number
			if the error_number is not -128 then display alert "DEVONthink" message error_message as warning
		end try
	end tell
end lookup_path


-- Get the path of the front document (of nearly any application you've listed in thisHandlerCovers)
--
-- For a quick test:
-- 1) activate an app that is listed in thisHandlerCovers and make sure you've opened a saved file in this application
-- 2) uncomment the first two lines in this script and run the script
--
-- To add more apps to this handler use the "frontAppName" script:
-- 1) open the "frontAppName" script in Script Editor (or whatever you use to run scripts)
-- 2) activate the app you want to add
-- 3) go back to Script Editor and run the "frontAppName" script (this copies the process name)
-- 4) now you've got the process name in the clipboard with which you can start to discover how to get this apps / this documents path. (I'm sorry for all the edits, I use this since months and totally forgot to mention basic parts..)

# front_testing()
# set thePath to path_to_front_document()

on path_to_front_document()
	tell application "System Events"
		set frontProcess to name of first process whose frontmost is true
	end tell
	
	set thisHandlerCovers to {"BBEdit", "DEVONthink 3", "Finder", "HoudahSpot", "Marked 2", "MultiMarkdown Composer", "OmniOutliner", "TableFlip", "TextEdit", "Tinderbox 8", "Electron", "Preview", "QuickTime Player", "FoxTrot Professional Search", "Curio", "PhotoStickies", "Numbers", "Nisus Writer Pro"}
	
	if thisHandlerCovers does not contain frontProcess then
		tell application "SystemUIServer"
			activate
			display dialog "Dieses Skript unterstützt diese App (noch) nicht"
		end tell
		return
		
	else
		
		if frontProcess = "BBEdit" then -- BBEdit
			tell application "BBEdit"
				set myFile to get file of front document of window 1
			end tell
			set thePath to POSIX path of myFile
			return thePath
		end if
		
		if frontProcess = "Finder" then -- Finder
			tell application "Finder"
				set theSelection to selection
				set theItem to item 1 of theSelection
				if class of theItem = alias file then
					set theItem to POSIX path of (original item of theItem as text)
				else
					set theItem to item 1 of theSelection as text
				end if
				set thePath to POSIX path of theItem
			end tell
			return thePath
		end if
		
		
		if frontProcess = "HoudahSpot" then -- HoudahSpot
			tell application id "com.houdah.HoudahSpot4"
				tell document 1
					set theSelection to selection
					set thisResult to item 1 of theSelection
					set thePath to path of thisResult
				end tell
			end tell
			return thePath
		end if
		
		
		if frontProcess = "Marked 2" then -- Marked 2
			tell application "Marked 2"
				set thePath to path of document 1
				return thePath
			end tell
		end if
		
		
		if frontProcess = "MultiMarkdown Composer" then -- MultiMarkdown Composer
			tell application "MultiMarkdown Composer"
				set doc to document of window 1
				set theFile to file of doc
			end tell
			set thePath to POSIX path of theFile
			return thePath
		end if
		
		
		if frontProcess = "OmniOutliner" then -- OmniOutliner
			tell application "OmniOutliner"
				set theFile to file of document 1
				set thePath to POSIX path of theFile
			end tell
			return thePath
		end if
		
		
		if frontProcess = "TableFlip" then -- TableFlip
			tell application "System Events"
				tell process "TableFlip"
					set theLocation to value of attribute "AXDocument" of window 1
				end tell
			end tell
			set theLocation to text_item_delimiters(theLocation, "//")
			set theLocation to text item 2 of theLocation
			try
				set thePath to my decode_Text(theLocation)
				return thePath
			on error eMsg number eNum
				error "Can't urlDecode: " & eMsg number eNum
			end try
		end if
		
		
		if frontProcess = "TextEdit" then -- TextEdit
			tell application "TextEdit"
				set doc to document of window 1
				set thePath to path of doc
			end tell
			return thePath
		end if
		
		
		if frontProcess = "Tinderbox 8" then -- Tinderbox 8
			tell application "System Events"
				try
					tell application process "Tinderbox"
						tell window 1
							set theLocation to the value of attribute "AXDocument"
							set theName to the value of attribute "AXTitle"
						end tell
					end tell
				on error error_message number error_number
					if the error_number is not -128 then display alert "Upps, das hat nicht geklappt" message error_message as warning
					return
				end try
			end tell
			set theLocation to text_item_delimiters(theLocation, "//")
			set theLocation to text item 2 of theLocation
			set thePath to my decode_Text(theLocation)
			return thePath
		end if
		
		
		if frontProcess = "Electron" then -- Visual Studio Code 
			tell application "System Events"
				tell application process "Electron"
					tell window 1
						set theLocation to the value of attribute "AXDocument"
					end tell
				end tell
			end tell
			set theLocation to text_item_delimiters(theLocation, "//")
			set theLocation to text item 2 of theLocation
			try
				set thePath to decode_Text(theLocation)
				return thePath
			on error eMsg number eNum
				error "Can't urlDecode: " & eMsg number eNum
				return
			end try
		end if
		
		
		if frontProcess = "Preview" then -- Preview
			tell application id "com.apple.Preview"
				set thePath to path of document 1
			end tell
			return thePath
		end if
		
		
		if frontProcess = "QuickTime Player" then -- QuickTime Player
			tell application id "com.apple.QuickTimePlayerX"
				set thePath to POSIX path of (get file of document 1)
			end tell
			return thePath
		end if
		
		
		--if frontProcess = "FoxTrot Professional Search" then -- FoxTrot Professional Search
		--	tell application "System Events"
		--		tell process "FoxTrot Professional Search"
		--			if pop up button 3 of splitter group 1 of group 1 of window 1 exists then
		--			else
		--				tell application id "com.ctmdev.FoxTrotPro"
		--					set theName to name of window 1
		--					set TextItems to my text_item_delimiters(theName, "/")
		--					set pathBeginning to ""
		--					set thePathBeginningItems to items 2 thru -1 of TextItems
		--					repeat with thisPart in thePathBeginningItems
		--						set pathBeginning to pathBeginning & "/" & thisPart
		--					end repeat
		--					set pathEnding to item 1 of TextItems
		--					set cleanedPathEnding to my remove_From_String(pathEnding, "  —  ")
		--					set thePath to pathBeginning & "/" & cleanedPathEnding
		--				end tell
		--			end if
		--		end tell
		--	end tell
		--	return thePath
		--end if
		
		
		if frontProcess = "Curio" then -- Curio
			tell application "System Events"
				tell process "Curio"
					set theLocation to the value of attribute "AXDocument" of window 1
				end tell
				set theLocation to my text_item_delimiters(theLocation, "//")
				set theLocation to text item 2 of theLocation
				try
					set thePath to my decode_Text(theLocation)
				on error eMsg number eNum
					error "Can't urlDecode: " & eMsg number eNum
				end try
			end tell
			return thePath
		end if
		
		
		if frontProcess = "PhotoStickies" then -- PhotoStickies
			tell application "System Events"
				tell process "PhotoStickies"
					set theFileName to name of window 1
				end tell
			end tell
			tell application id "DNtp"
				try
					set theResults to my lookup_filename(theFileName)
					set thePath to path of item 1 of theResults
				on error error_message number error_number
					if the error_number is not -128 then display alert "DEVONthink" message error_message as warning
				end try
			end tell
			return thePath
		end if
		
		
		if frontProcess = "Numbers" then -- Numbers
			tell application "Numbers"
				set theFile to file of document 1
				set thePath to POSIX path of theFile
			end tell
			return thePath
		end if
		
		
		if frontProcess = "Nisus Writer Pro" then -- Nisus Writer Pro
			tell application id "com.nisus.NisusWriter"
				set thePath to path of document 1
			end tell
			return thePath
		end if
		
	end if
end path_to_front_document

on text_item_delimiters(theText, theDelimiter)
	set d to AppleScript's text item delimiters
	set AppleScript's text item delimiters to theDelimiter
	set TextItems to text items of theText
	set AppleScript's text item delimiters to d
	return TextItems
end text_item_delimiters

on decode_Text(theText)
	local str
	try
		return (do shell script "/bin/echo " & quoted form of theText & " | perl -MURI::Escape -lne 'print uri_unescape($_)'")
	on error eMsg number eNum
		error "Can't urlDecode: " & eMsg number eNum
	end try
end decode_Text

on remove_From_String(theText, CharOrString)
	local ASTID, theText, CharOrString, lst
	set ASTID to AppleScript's text item delimiters
	try
		considering case
			if theText does not contain CharOrString then ¬
				return theText
			set AppleScript's text item delimiters to CharOrString
			set lst to theText's text items
		end considering
		set AppleScript's text item delimiters to ASTID
		return lst as text
	on error eMsg number eNum
		set AppleScript's text item delimiters to ASTID
		error "Can't RemoveFromString: " & eMsg number eNum
	end try
end remove_From_String

on lookup_filename(theFileName)
	tell application id "DNtp"
		try
			set theDatabases to databases
			set theResults to {}
			repeat with thisDatabase in theDatabases
				set thisDatabasesResults to lookup records with file theFileName in thisDatabase
				set theResults to theResults & thisDatabasesResults
			end repeat
			if theResults = {} then display notification "Datei nicht in offenen Datenbanken enthalten!" with title "Lookup DEVONthink"
			return theResults
		on error error_message number error_number
			if the error_number is not -128 then display alert "DEVONthink" message error_message as warning
		end try
	end tell
end lookup_filename

on front_testing()
	tell application "System Events"
		set frontmostProcess to first process where it is frontmost
		set visible of frontmostProcess to false
		repeat while (frontmostProcess is frontmost)
			delay 0.2
		end repeat
		set secondFrontmost to first process where it is frontmost
	end tell
end front_testing


```


#Applescript #DEVONthink #Alfred