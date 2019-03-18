That’s something that has always bothered me. Unfortunately there is no way to do it via a user interface. But it can be easily achieved running the following commands in the Terminal:

```
defaults write com.apple.screencapture location ~/Desktop/Screenshots/
killall SystemUIServer
```

Change the `~/Desktop/Screenshots/` with the desired path you want to save the screen shots. It is important to note that you need to create the target folder before changing the path.