# Get date

For more information, see [this thread][1].

```applescript
use AppleScript version "2.4" -- Yosemite (10.10) or later
use scripting additions

on getDateStr(theDate, theDateFormat, theOffset)
	--by ngan 2020.05.03
	
	-- CASE SENSITIVITIVE parameters for theDateFormat
	-- Comment: the parameters are identical to shell scipt of 'date' except for Q and q
	-- Q and q are customised to generate an array of 7 days
	
	-- Y  long year	 (e.g. 2020		
	-- y  short year (e.g. 20)
	-- m  month ( e.g. 01..12)			
	-- B  long month name ( e.g. January)		
	-- b  abbreviated month name ( e.g. Jan)
	-- d  day of moth (e.g. 01..31)		
	-- A  full weekday name (e.g. Monday)	 	
	-- a  abbreviated weekday name (e.g. Mon)
	-- R 24-hour time (e.g. 16:10)	 		
	-- r  12-hr clock time (e.g. 4:10 PM)
	-- H  hour (e.g. 00..23)		 	
	-- I  hour (e.g. 1..12)
	-- p  AM/PM	(e.g.  AM/PM)
	-- M  minute  (e.g. 00..59)			
	-- S  second (e.g. 00..60)
	-- V  ISO week number - Monday as 1st day of week ( e.g. 01..53)	 
	-- Q an array from Monday to Sunday of a given ISO week num ( e.g. {27.04.2020,...03.05.2020})
	-- q  an array of a given date and the following 6 days
	
	-- CASE SENSITIVITIVE parameters for theOffset
	-- y  +/-years	(e.g. "+2y" or "-2y")
	-- m  +/-months	(e.g. "+2m" or "-2m")
	-- w  +/-weeks	(e.g. "+2w" or "-2w")
	-- d  +/-days	(e.g. "+2d" or "-2d")
	-- H  +/-hours		(e.g. "+2H" or "-2H" )
	-- M  +/-minutes		(e.g. "+2M" or "-2M")
	-- S  +/-seconds		(e.g. "+2S" or "-2S") 
	
	
	-- Main
	set ltd to {{"y", 365 * days}, {"m", ""}, {"w", 7 * days}, {"d", days}, {"H", hours}, {"M", minutes}, {"S", 1}}
	set lol to {{"Y", ""}, {"y", ""}, {"m", ""}, {"B", ""}, {"b", ""}, {"d", ""}, {"A", ""}, {"a", ""}, {"R", ""}, {"r", ""}, {"H", ""}, {"I", ""}, {"p", ""}, {"M", ""}, {"S", ""}, {"V", ""}, {"Q", ""}, {"q", ""}}
	set lp to {"Y", "y", "m", "B", "b", "d", "A", "a", "R", "r", "H", "I", "p", "M", "S", "V"}
	
	set useShell to theDate = (current date)
	
	considering case
		--if theDate = (current date) then use shell script for all parameters in list lp
		if useShell then
			
			set v to ""
			if theOffset is not "" then set v to " -v "
			--convert theOffset in a format that is recognisable by shell script
			set theShellPara to characters of theDateFormat
			repeat with i from 1 to length of theShellPara
				if theShellPara's item i is in lp then
					set theShellPara's item i to "%" & theShellPara's item i
				end if
			end repeat
			set theShellPara to "+" & theShellPara
			
			-- get weeknum and adjusted date for computing Q and q
			set (lol's item 16)'s item 2 to do shell script "date " & v & theOffset & " '+%V'"
			set theDate to do shell script "date " & v & theOffset & " '+%d.%m.%Y %H:%M:%S'"
			set theDate to date theDate
			
			if "Q" is not in theDateFormat and "q" is not in theDateFormat then
				set ds to do shell script "date " & v & theOffset & " " & quoted form of theShellPara
				return ds
			end if
			
			--	theDate is not current date then use date string computation
		else
			
			
			if theOffset is not "" then
				-- obtain the adjusted date (theDate+theOffset) calculation before formatting
				set theOffset to (do shell script "[[ " & quoted form of theOffset & " =~ ([+-]+)([0-9]+)([A-Za-z]+) ]] && echo \"${BASH_REMATCH[1]},${BASH_REMATCH[2]},${BASH_REMATCH[3]}\"")
				
				set {tid, text item delimiters} to {text item delimiters, ","}
				set theOffset to every text item of theOffset
				set text item delimiters to tid
				
				set {offsetOperator, offsetMultiplier, offsetUnit} to theOffset
				-- offsetBase in seconds. E.g. theOffset="+?d",offsetBase = 24*3600   
				set offsetBase to (my lolLookup(offsetUnit, 1, 2, ltd))'s item 2
				
				if offsetUnit is in {"w", "d", "H", "M", "S"} then
					-- standard date operation in AppleScript 
					set theDate to theDate + ((offsetOperator as string) & (offsetMultiplier as integer) * offsetBase)
					
				else if offsetUnit is "y" then
					-- +/- years
					set theDate's year to (theDate's year) + ((offsetOperator as string) & (offsetMultiplier as integer) * 1)
					
					
				else if offsetUnit is "m" then
					-- tricky arithmatics
					set {y, m} to {theDate's year, theDate's month as integer}
					set yAdjustment to (m + ((offsetOperator as string) & (offsetMultiplier as integer) * 1)) div 12
					set mAdjustment to (m + ((offsetOperator as string) & (offsetMultiplier as integer) * 1)) mod 12
					
					if mAdjustment > 0 then
						set y to y + yAdjustment
						set m to mAdjustment
					else
						set y to y + yAdjustment - 1
						set m to mAdjustment + 12
					end if
					
					set {theDate's year, theDate's month} to {y, m}
					
				end if
			end if
			
			tell theDate to set {y, m, ms, d, dw, h, n, s} to {its year as string, its month as integer, its month as string, its day as string, its weekday as string} & every word of its time string
			
			-- get Y
			set (lol's item 1)'s item 2 to y
			-- get y
			set (lol's item 2)'s item 2 to (characters 3 thru 4 of y as string)
			-- get m
			if m < 10 then -- m
				set (lol's item 3)'s item 2 to "0" & m
			else
				set (lol's item 3)'s item 2 to m
			end if
			-- get B
			set (lol's item 4)'s item 2 to ms
			-- get b
			set (lol's item 5)'s item 2 to (characters 1 thru 3 of ms as string)
			-- get d
			set (lol's item 6)'s item 2 to d
			-- get A
			set (lol's item 7)'s item 2 to dw
			-- get a
			set (lol's item 8)'s item 2 to (characters 1 thru 3 of dw as string)
			-- get R
			set (lol's item 9)'s item 2 to h & ":" & n
			set {t1, t2} to {h div 12, h mod 12} -- t1,t2 are temporary variables
			if t1 ≥ 1 then
				-- get p
				set (lol's item 13)'s item 2 to "PM"
				-- get r
				set (lol's item 10)'s item 2 to ((h mod 12) & ":" & n & " PM")
			else
				-- get p
				set (lol's item 13)'s item 2 to "AM"
				-- get r
				set (lol's item 10)'s item 2 to (h & ":" & n & " AM")
			end if
			-- get H
			set (lol's item 11)'s item 2 to h
			-- get I
			set (lol's item 12)'s item 2 to (h mod 12)
			-- get M
			set (lol's item 14)'s item 2 to n
			-- get S
			set (lol's item 15)'s item 2 to s
			-- get V
			--credit Nigel Garvey from macscripter.net for the script getISOWeekNumber ()
			-- Comment from his script: ISO Standard: Weeks start on Mondays. A week that straddles a year boundarybelongs to the year in which it has more days. One consequence of this is that the "first week" of any year contains 4th January.
			set aMonday to date "Monday, 3 January 2000 at 00:00:00"
			set nextMonday to theDate - (theDate - aMonday) mod weeks + weeks
			set Jan4 to date "Tuesday, 4 January 2000 at 00:00:00"
			set Jan4's year to nextMonday's year
			if (Jan4 does not come before nextMonday) then set Jan4's year to (Jan4's year) - 1
			set baseMonday to Jan4 - (Jan4 - aMonday) mod weeks
			set (lol's item 16)'s item 2 to (nextMonday - baseMonday) div weeks
			-- end of Nigel's code for getISOWeekNumber()
			
			
			set ds to ""
			if "Q" is not in theDateFormat and "q" is not in theDateFormat then
				set theDateFormat to (every character of theDateFormat)
				repeat with n from 1 to length of theDateFormat
					if theDateFormat's item n is in lp then
						set ds to ds & (my lolLookup(theDateFormat's item n, 1, 2, lol))'s item 2
					else
						set ds to ds & theDateFormat's item n
					end if
				end repeat
				return ds
			end if
		end if
		
		--get Q
		--credit Nigel Garvey from macscripter.net for the script getdatesforweeknum()
		-- Comment from his script: Assuming the ISO standard that the first week of the year is the one containing 4th January although that might begin in the previous calendar year.
		tell theDate to set {y, m, d} to {its year, its month, its day}
		set weekNum to (lol's item 16)'s item 2
		set yearNum to y
		set preferredWeekStart to "Mon"
		
		set Jan4 to date "Tuesday, 4 January 2000 at 00:00:00" -- Or 4th January in any year after 1582.
		set Jan4's year to yearNum -- Make that 4th January in the given year.
		set baseMonday to date "Monday, 3 January 2000 at 00:00:00" -- Or any Monday in the past.
		set baseWeekday to baseMonday + ((offset of (text 1 thru 2 of (preferredWeekStart as text)) in "MoTuWeThFrSaSu") div 2) * days
		-- Derive the beginning of the week containing 4th January in the given year
		set week1Start to Jan4 - (Jan4 - baseWeekday) mod weeks
		-- and from that, the beginning of the weekNumth week.
		set weekNStart to week1Start + (weekNum - 1) * weeks
		set datesInWeek to {}
		repeat with i from 0 to 6 * days by days
			set {day:d, month:m, year:y} to weekNStart + i
			tell y * 10000 + m * 100 + d as text
				set end of datesInWeek to text 7 thru 8 & "." & text 5 thru 6 & "." & text 1 thru 4
			end tell
		end repeat
		set (lol's item 17)'s item 2 to datesInWeek
		-- end of Nigel's code for getdatesforweeknum()
		
		--get q
		set datesNextSevenDays to {}
		copy theDate to dte
		repeat with i from 0 to 6
			set {day:d, month:m, year:y} to dte + i * days
			tell y * 10000 + m * 100 + d as text
				set end of datesNextSevenDays to text 7 thru 8 & "." & text 5 thru 6 & "." & text 1 thru 4
			end tell
		end repeat
		set (lol's item 18)'s item 2 to datesNextSevenDays
		
		if theDateFormat is "Q" then
			return (lol's item 17)'s item 2
		else if theDateFormat is "q" then
			return (lol's item 18)'s item 2
		end if
		
	end considering
	
	
end getDateStr


on lolLookup(lookupVal, lookUpPos, getValPos, theList)
	--only for list of list with more than 1 items, and only return the first exact match
	local i, j, k
	set j to lookUpPos
	set k to getValPos
	repeat with i from 1 to length of theList
		-- return (1) the index of the matched list in lol, (2) the specific item in the matched list, (3) the matched list
		if (item j of item i of theList) is equal to lookupVal then return {i, item k of item i of theList, item i of theList}
	end repeat
	return {0, "", {}}
end lolLookup
```

[1]:	https://discourse.devontechnologies.com/t/a-flexible-handler-to-generate-date-format-for-system-and-plug-in-date-string-allow-time-offset-and-7-days-generator/55369

#Snippets #Handler