# Actions (for Alfred Workflow)

## Variáveis
```applescript

WinID id
TabID id
ActionCloseWin boolean
ActionCloseTab boolean
ActionAttach id
DetachAll boolean
ActionOpenNewDoc boolean
ActionOpenNewWin boolean
ActionActivate boolean
ActionOpenEx boolean
ActionRename text
ReturnLink text

```

```applescript
WinID
TabID
ActionCloseWin
ActionCloseTab
ActionAttach
DetachAll
ActionOpenNewDoc
ActionOpenNewWin
ActionActivate
ActionOpenEx
ActionRename
ReturnLink
```


```applescript


-- 2020-05-08-19-07-05 Conditional to prevent accidental triggering of theUUID path
-- 2020-05-08-19-58-13 Added try before actions to prevent the script from stopping; avoided encoding error in renaming action by using the clipboard.


on run
	
	-- Identify the record
	try -- via tab info	
		set WinID to ((system attribute "WinID") as number)
	end try
	try
		set TabID to ((system attribute "TabID") as number)
	end try
	
	
	try -- via UUID
		set theUUID to ((system attribute "theUUID") as text)
		set theNr to count (theUUID)
		if theNr is not 36 then set theUUID to ""
	end try
	
	
	-- Actions	
	try -- close think window
		set ActionCloseWin to ((system attribute "ActionCloseWin") as number)
	end try
	
	
	try -- close selected tab
		set ActionCloseTab to ((system attribute "ActionCloseTab") as number)
	end try
	
	
	try -- attach record to selected think window
		set ActionAttach to ((system attribute "ActionAttach") as number) -- contains the id of the target window
	end try
	
	
	try -- detach all tabs from selected think window
		set DetachAll to ((system attribute "DetachAll") as number)
	end try
	
	
	
	try -- open record in new document window
		set ActionOpenNewDoc to ((system attribute "ActionOpenNewDoc") as number)
	end try
	
	
	try -- open record in new viewer window
		set ActionOpenNewWin to ((system attribute "ActionOpenNewWin") as number)
	end try
	
	
	try -- activate the record
		set ActionActivate to ((system attribute "ActionActivate") as number)
	end try
	
	
	
	try -- open record externally
		set ActionOpenEx to ((system attribute "ActionOpenEx") as number)
	end try
	
	
	-- Metadata manipulation
	try -- rename the record
		set ActionRename to (system attribute "ActionRename") -- contains the new name
		
	end try
	try -- change the aliases
		set ActionAliases to ((system attribute "ActionAliases") as text) -- contains the new name
	end try
	
	-- Detach = ActionCloseTab + ActionOpenNewDoc
	-- Attach = ActionCloseTab + ActionAttach
	-- DetachAll = WinID + number
	-- New document window = ActionOpenNewDoc
	-- New viewer window = ActionOpenNewWin
	-- Open/activate = ActionActivate
	-- Open ReturnLink = No especial formula just open in desired way
	
	tell application id "DNtp"
		
		
		-- identify the record
		try
			if theUUID is not "" then
				set theRecord to get record with uuid theUUID
			else
				set theWin to window id WinID
				set theTab to tab id TabID in theWin
				set theRecord to content record of theTab
			end if
			-- get record URL
			set theURL to the reference URL of theRecord
		end try
		
		
		
		
		
		-- close window
		try
			if ActionCloseWin is not 0 then close (window id WinID)
		end try
		-- close tab
		
		try
			if ActionCloseTab is not 0 then close theTab
		end try
		
		-- attach tab to window
		try
			if ActionAttach is not 0 then open tab for record theRecord in (window id ActionAttach)
		end try
		
		-- detach all tabs
		if DetachAll is not 0 then
			try
				set theTabs to every tab in (window id WinID)
				repeat with theTab in theTabs
					set theRecord to content record of theTab
					close theTab
					open tab for record theRecord
				end repeat
			end try
		end if
		
		-- open new document window
		try
			if ActionOpenNewDoc is not 0 then open tab for record theRecord
		end try
		
		-- open new viewer window
		try
			if ActionOpenNewWin is not 0 then open window for record theRecord
		end try
		
		-- activate record
		try
			if ActionActivate is not 0 then tell me to do shell script "open " & theURL
		end try
		
		-- open record externally
		if ActionOpenEx is not 0 then
			try
				set thePath to (path of theRecord as string)
				tell me to do shell script "open " & quoted form of thePath
			end try
		end if
		
		-- data manipulation
		-- rename record
		if ActionRename is not "" then
			try
				set theName to do shell script "pbpaste"
				set the name of theRecord to theName
			end try
		end if
		if ActionAliases is not "" then
			try
				set theAliases to do shell script "pbpaste"
				set the aliases of theRecord to theAliases
			end try
		end if
		
		
	end tell
end run


```


#Applescript #DEVONthink #Alfred