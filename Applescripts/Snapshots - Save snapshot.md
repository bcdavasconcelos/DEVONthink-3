# Snapshots - Save snapshot

```applescript

tell application id "DNtp"
	-- set theRecords to the selection
	-- repeat with theRecord in theRecords
	set theRecord to (content record of think window 1)
	
	set theText to the plain text of theRecord
	set theNewMod to the modification date of theRecord
	
	set theOldBackup to get custom meta data for "v9" from theRecord default value ""
	add custom meta data theOldBackup for "v10" to theRecord
	
	set theOldBackup to get custom meta data for "v8" from theRecord default value ""
	add custom meta data theOldBackup for "v9" to theRecord
	
	set theOldBackup to get custom meta data for "v7" from theRecord default value ""
	add custom meta data theOldBackup for "v8" to theRecord
	
	set theOldBackup to get custom meta data for "v6" from theRecord default value ""
	add custom meta data theOldBackup for "v7" to theRecord
	
	set theOldBackup to get custom meta data for "v5" from theRecord default value ""
	add custom meta data theOldBackup for "v6" to theRecord
	
	set theOldBackup to get custom meta data for "v4" from theRecord default value ""
	add custom meta data theOldBackup for "v5" to theRecord
	
	set theOldBackup to get custom meta data for "v3" from theRecord default value ""
	add custom meta data theOldBackup for "v4" to theRecord
	
	set theOldBackup to get custom meta data for "v2" from theRecord default value ""
	add custom meta data theOldBackup for "v3" to theRecord
	
	set theOldBackup to get custom meta data for "v1" from theRecord default value ""
	add custom meta data theOldBackup for "v2" to theRecord
	
	set theMod to get custom meta data for "v9" from theRecord default value ""
	add custom meta data theMod for "v10" to theRecord
	
	set theMod to get custom meta data for "d8" from theRecord default value ""
	add custom meta data theMod for "d9" to theRecord
	
	set theMod to get custom meta data for "d7" from theRecord default value ""
	add custom meta data theMod for "d8" to theRecord
	
	set theMod to get custom meta data for "d6" from theRecord default value ""
	add custom meta data theMod for "d7" to theRecord
	
	set theMod to get custom meta data for "d5" from theRecord default value ""
	add custom meta data theMod for "d6" to theRecord
	
	set theMod to get custom meta data for "d4" from theRecord default value ""
	add custom meta data theMod for "d5" to theRecord
	
	set theMod to get custom meta data for "d3" from theRecord default value ""
	add custom meta data theMod for "d4" to theRecord
	
	set theMod to get custom meta data for "d2" from theRecord default value ""
	add custom meta data theMod for "d3" to theRecord
	
	set theMod to get custom meta data for "d1" from theRecord default value ""
	add custom meta data theMod for "d2" to theRecord
	
	add custom meta data theNewMod for "d1" to theRecord
	add custom meta data theText for "v1" to theRecord
	
	log message "New version saved" info "Saved at " & ((current date) as string) --
	
end tell

```


#Applescript #DEVONthink