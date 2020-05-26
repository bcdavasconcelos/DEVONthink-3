# Highlight dictionary words in PDF file

## Dependencies:
* Python 3
* PyMuPdf `
	`
```bash
pip install PyMuPdf
```

```python
# Taken from https://github.com/isitan/PDF-dictionary-highlighter

import fitz
import re

# splits the words from special characters and checks if those subwords are in the dictionary.
def wordListProcessor(wordList, page):
    word = wordList[4]
    split_word = re.split(r'[`\-=~!@#$%^&*()_+\[\]{};\'\\:"|<,./<>?]', word)
    for subword in split_word:
        stripped = subword.strip()
        if(stripped in lookup_set or stripped[:-1] in lookup_set or stripped[:-2] in lookup_set):
            highlight = page.addHighlightAnnot(fitz.Rect(wordList[0], wordList[1], wordList[2], wordList[3]))

lookup_set = set()
# reads dictionary into set to accelarate the process
def setMaker(file_name):
    _file = open(file_name, "r")
    for entry in _file:
        lookup_set.add(entry[:-1].strip())

# dictionary files, expand the list with your dictionaries.
dictionary_file_names = ["./Words/List.txt"]

for dictionary_file in dictionary_file_names:
    setMaker(dictionary_file)

### READ IN PDF
doc = fitz.open("./PDF/File.pdf")

for page in doc:
    page_text = page.getText("words",flags=None)
    for wordList in page_text:
        #wordList is a list which first 4 elements are location and the 5th is the word itself
        wordListProcessor(wordList, page)

### OUTPUT
doc.save("./File.pdf", garbage=4, deflate=True, clean=True)


```
