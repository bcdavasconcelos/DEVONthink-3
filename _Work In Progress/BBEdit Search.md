# BBEdit Search

```applescript
tell application "BBEdit"
	
	set theRecords to get document "Markdown.bbprojectd"
	
	-- Search options
	set SearchPattern to ".{0,40} γένεσις .{0,60}\\s"
	-- set SearchPattern to "γένεσις"
	set SearchWithGrep to grep -- replace 'grep' with 'literal' in order to search without grep	
	set SearchCaseSensitive to false
	set MatchEntireWords to false
	set DisplayResultsWindow to false
	set ReturnResults to true
	
	set SearchOptions to {search mode:SearchWithGrep, case sensitive:SearchCaseSensitive, match words:MatchEntireWords, showing results:DisplayResultsWindow, returning results:ReturnResults}
	
	-- End of search options
	
	set theResults to ""
	set theResults to find SearchPattern searching in theRecords options SearchOptions
	set ResultList to found matches of theResults
	
	set theEntries to {}
	repeat with each in ResultList
		--	set each to get item 1 of ResultList
		set thePath to result_file of each
		
		tell application "Finder"
			set theFile to get file thePath
			set theName to name of theFile
		end tell
		
		set theEntry to "## " & theName & return & return & "* " & (message of each) & return
		set theEntries to theEntries & theEntry
		--	set thePath to (POSIX path of result_file of theRecord)
		--	set theRecord to get file (result_file of theRecord)
		-- result_file of item 1 of ResultList
		
		
	end repeat
	
	--	set theResult to replace ".md" searching in (theEntries as text) using ""
	set theResult to process duplicate lines (theEntries as text) ¬
		duplicates options {match mode:leaving_one} ¬
		output options {deleting duplicates:true}
	
	
	--	process duplicate lines theEntries duplicates options {match mode:leaving_one} output options {deleting duplicates:true}
	
end tell



```
