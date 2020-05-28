```applescript

on get_section(theText, theSectionName)
	set n to (count my decoupe(theSectionName, "#")) - 1 -- section level
	set theSectionSearch to theSectionName & "((.|\\s)+)" & "(#{" & n & "}|&nbsp;)" -- section level search string
	set theSectionText to regex search theText search pattern theSectionSearch replace template "$1" -- use regex to get it
	set theText to theSectionText -- if section name, text will be just this section
end get_section

```
