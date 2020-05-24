# Replace text

```applescript
set theWord to my replaceText(theWord, " ", "%20")

on replaceText(theString, old, new)
        set {TID, text item delimiters} to {text item delimiters, old}
        set theStringItems to text items of theString
        set text item delimiters to new
        set theString to theStringItems as text
        set text item delimiters to TID
        return theString
end replaceText

```


#Snippets #Handler