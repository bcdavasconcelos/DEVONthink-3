# Parse text with delimiter

```applescript
-- Manipula delimitadores do applescript para separar texto em blocos e fazer uma lista #as

try -- parse variable
   set oldDelims to AppleScript's text item delimiters -- salvar o delimitador padrão
   set AppleScript's text item delimiters to {"{cursor}"} -- declarar delimitador novo

set theText to ""



set delimitedList to every text item of theText
set AppleScript's text item delimiters to oldDelims -- restaurar delimitador padrão
on error
   set AppleScript's text item delimiters to oldDelims -- restaurar delimitador padrão em caso de erro
end try
```


#Snippets