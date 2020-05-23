# BBedit
Comandos importantes do BBEdit

## Markdown as default

```applescript
defaults write com.barebones.bbedit DefaultLanguageNameForNewDocuments -string "Markdown" 
```

## Keyboard navigation in the Sidebar

```applescript
defaults write com.barebones.bbedit ProjectsListCanAcquireKeyboardFocus -bool YES
```

## Reveal project in sidebar

```applescript
defaults write com.barebones.bbedit AutoRevealSelectedDocumentInProjectList -bool YES
```


## Use iTerm

```applescript
defaults write com.barebones.bbedit TerminalBundleID -string "com.googlecode.iterm2"
```

## Warn about non-UT8

```applescript
defaults write com.barebones.bbedit WarnMalformedUTF8 -bool YES
```

## Mono-spaced font in gutter

```applescript
defaults write com.barebones.bbedit UseSystemFontForLineBar -bool YES
```

## Live Search bar

```applescript
defaults write com.barebones.bbedit Editor_AlwaysOpenQuickFind -bool YES
```

## Replace All confirmation sheet

```applescript
defaults write com.barebones.bbedit ReportReplaceAllResults -bool YES
```

## BBEdit's Replace All time

```applescript
defaults write com.barebones.bbedit ReplaceAllResultsIncludeTiming -bool YES
```

## Find and Replace History Size

```applescript
defaults write com.barebones.bbedit FindAndReplaceHistorySize -int 100
```

## Surf next/previous in display order

```applescript
defaults write com.barebones.bbedit SurfNextPreviousInDisplayOrder -bool YES
```

## Open file by name (modal on/off)

```applescript
defaults write com.barebones.bbedit CloseOFBNWindowAfterOpeningSelection -bool YES
```

## Spaces (move dialog to different spaces)

### Controls the Find window
```applescript
defaults write com.barebones.bbedit MoveModelessWindowsToActiveSpace_FindWindow -bool YES
```

### Controls the Multi-File Search window
```applescript
defaults write com.barebones.bbedit MoveModelessWindowsToActiveSpace_MultiFileFindWindow -bool YES
```

### Controls the Open File by Name window

```applescript
defaults write com.barebones.bbedit MoveModelessWindowsToActiveSpace_OpenFileByNameWindow -bool YES
```

### Sets the default behavior for any of the above window types for which a default is not explicitly specified

```applescript
defaults write com.barebones.bbedit MoveModelessWindowsToActiveSpace -bool YES
```


#BBEdit