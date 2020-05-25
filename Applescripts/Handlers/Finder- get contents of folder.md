# Finder: get contents of folder

```applescript
property kFileList : {}

tell application "Finder"
	set source_folder to choose folder with prompt "Please select directory."
	my createList(source_folder)
end tell

return kFileList

on createList(mSource_folder)
	set item_list to ""
	
	tell application "System Events"
		set item_list to get the name of every disk item of mSource_folder
	end tell
	
	set item_count to (get count of items in item_list)
	
	repeat with i from 1 to item_count
		set the_properties to ""
		
		set the_item to item i of the item_list
		set the_item to ((mSource_folder & the_item) as string) as alias
		
		tell application "System Events"
			set file_info to get info for the_item
		end tell
		
		if visible of file_info is true then
			set file_name to displayed name of file_info
			set end of kFileList to file_name
			if folder of file_info is true then
				my createList(the_item)
			end if
		end if
		
	end repeat
end createList
```
