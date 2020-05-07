# String to list, list to string

```applescript

-- set theString to my listToStr(theList, ", ")
on listToStr(theList, d)
	local thestr
	set {tid, text item delimiters} to {text item delimiters, d}
	set thestr to theList as text
	set text item delimiters to tid
	return thestr
end listToStr

-- set theList to my strToList(theString, ", ")
on strToList(thestr, d)
	local theList
	set {tid, text item delimiters} to {text item delimiters, d}
	set theList to every text item of thestr
	set text item delimiters to tid
	return theList
end strToList
```


#Snippets #Applescript