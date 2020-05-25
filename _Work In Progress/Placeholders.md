# Placeholders


```applescript
-- Jim Neumann

on performSmartRule(theRecords)
	set ot to AppleScript's text item delimiters
	tell application id "DNtp"
		repeat with thisRecord in theRecords
			set recTags to tags of thisRecord
			if recTags ≠ {} then
				set AppleScript's text item delimiters to ", "
				set recTags to (text items of recTags) as string
				--display alert "" & recTags
				set docText to (plain text of thisRecord)
				if docText contains "%tags%" then
					set plain text of thisRecord to (do shell script "echo " & (quoted form of docText) & " | sed 's_%tags%_Tags: " & recTags & "_'")
				end if
			end if
		end repeat
		set AppleScript's text item delimiters to ", "
	end tell
end performSmartRule

```
