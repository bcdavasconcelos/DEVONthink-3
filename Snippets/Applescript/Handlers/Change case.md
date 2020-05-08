# Change case

```applescript

set theText to ""
set the theText to my change_case(theText, "lower")
set the theText to my change_case(theText, "upper")


-- AppleScript 'change_case' handler
on change_case(this_text, this_case)
	if this_case is "lower" then
		set the comparison_string to "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
		set the source_string to "abcdefghijklmnopqrstuvwxyz"
	else
		set the comparison_string to "abcdefghijklmnopqrstuvwxyz"
		set the source_string to "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
	end if
	set the new_text to ""
	repeat with thisChar in this_text
		set x to the offset of thisChar in the comparison_string
		if x is not 0 then
			set the new_text to (the new_text & character x of the source_string) as string
		else
			set the new_text to (the new_text & thisChar) as string
		end if
	end repeat
	return the new_text
end change_case

```


#Snippets #Applescript