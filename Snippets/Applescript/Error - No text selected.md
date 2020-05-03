# Error - No text selected

```applescript
try

  set invalidName to false
  set theName to selected text of think window 1

if theName is "" then set invalidName to true

  on error

set invalidName to true
end try

if invalidName then error "No text selected."
```


#Snippets #DEVONthink