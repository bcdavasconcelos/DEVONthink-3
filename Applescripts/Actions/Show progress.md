# Show progress

Posted by @halloleo in [this thread](https://discourse.devontechnologies.com/t/in-applescript-how-does-the-with-cancel-button-attribute-of-the-show-progress-command-work/56214/4).

```applescript

tell application id "DNtp"
	try
		activate
		show progress indicator "Working" steps 10 with cancel button
		repeat with a from 1 to 10
			step progress indicator "Step " & (a as string)
			delay 1
			--			if cancelled progress then exit repeat
			if cancelled progress then error -128
		end repeat
	on error errStr number errNum
		display dialog "error " & errStr
	end try
	hide progress indicator
end tell

```
