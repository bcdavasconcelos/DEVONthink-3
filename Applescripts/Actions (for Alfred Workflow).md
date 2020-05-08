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

on run
	
	set WinID to ((system attribute "WinID") as number)
	set TabID to ((system attribute "TabID") as number)
	
	set ActionCloseWin to ((system attribute "ActionCloseWin") as boolean)
	set ActionCloseTab to ((system attribute "ActionClose") as boolean)
	
	set ActionAttach to ((system attribute "ActionAttach") as number) -- contains the id of the target window
	set DetachAll to ((system attribute "DetachAll") as boolean)
	
	set ActionOpenNewDoc to ((system attribute "ActionOpenNewDoc") as boolean)
	set ActionOpenNewWin to ((system attribute "ActionOpenNewWin") as boolean)
	
	set ActionActivate to ((system attribute "ActionActivate") as boolean)
	set ActionOpenEx to ((system attribute "ActionOpenEx") as boolean)
	
	set ActionRename to ((system attribute "ActionRename") as text) -- contains the new name
	set ReturnLink to ((system attribute "ReturnLink") as text)
	
	-- Detach = ActionCloseTab + ActionOpenNewDoc
	-- Attach = ActionCloseTab + ActionAttach
	-- DetachAll = WinID + boolean
	-- New document window = ActionOpenNewDoc
	-- New viewer window = ActionOpenNewWin
	-- Open/activate = ActionActivate
	-- Open ReturnLink = No especial formula just open in desired way
	
	
	set WinID to 25811
	set TabID to 1
	
	tell application id "DNtp"
		try
		if ReturnLink is "" then
			set theRecord to content record of (tab id TabID) in (window id WinID)
		else
			set theRecord to get record with uuid theUUID
		end if
		
		set theURL to the reference URL of theRecord
		end try
		-- close window, close tab, attach tab to window, open new doc window, open tab, open externally (the order of the sequence is relevant)
		
		if ActionCloseWin then close (window id WinID)
		if ActionCloseTab then close theRecord
		if ActionAttach is not "" then open tab for record theRecord in (window id ActionAttach)
		if DetachAll then
			set theTabs to every tab in (window id WinID)
			repeat with theTab in theTabs
				set theRecord to content record of theTab
				close theTab
				open tab for record theRecord
			end repeat
		end if
		
		if ActionOpenNewDoc then open tab for record theRecord
		if ActionOpenNewWin then open window for record theRecord
		if ActionActivate then tell me to do shell script "open " & theURL
		if ActionOpenEx then
			set thePath to (path of theRecord as string)
			do shell script "open " & quoted form of thePath
		end if
		if ActionRename is not "" then set the name of theRecord to ActionRename
		
		
		
	end tell
end run


```
