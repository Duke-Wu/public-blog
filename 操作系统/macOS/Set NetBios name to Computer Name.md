https://discussions.apple.com/thread/2457573



Gs-MacBook-Pro:~ g$ cat /Library/Preferences/SystemConfiguration/com.apple.smb.server.plist

<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">

<plist version="1.0">

<dict>

​	<key>DOSCodePage</key>

​	<string>437</string>

​	<key>LocalKerberosRealm</key>

​	<string>LKDC:SHA1.A543A61542DCC2B6D15C090656FECD0C8DEDF96C</string>

​	<key>NetBIOSName</key>

​	<string>Gs-MacBook-Pro</string>

​	<key>ServerDescription</key>

​	<string>G’s MacBook Pro</string>

</dict>

</plist>

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ defaults read /Library/Preferences/SystemConfiguration/com.apple.smb.server NetBIOSName

Gs-MacBook-Pro

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ defaults read /Library/Preferences/SystemConfiguration/com.apple.smb.server NetBIOSName

Gs-MacBook-Pro

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ sudo defaults write /Library/Preferences/SystemConfiguration/com.apple.smb.server NetBIOSName $HOSTNAME

Password:

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ cat /Library/Preferences/SystemConfiguration/com.apple.smb.server.plist

bplist00_ServerDescription[NetBIOSName_LocalKerberosRealm[DOSCodePageoG s MacBook Pro^Gs-MacBook-Pro_2LKDC:SHA1.A543A61542DCC2B6D15C090656FECD0C8DEDF96CS43%1FRs??	?Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ cat /Library/Preferences/SystemConfiguration/com.apple.smb.server.plist

bplist00_ServerDescription[NetBIOSName_LocalKerberosRealm[DOSCodePageoG s MacBook Pro^Gs-MacBook-Pro_2LKDC:SHA1.A543A61542DCC2B6D15C090656FECD0C8DEDF96CS43%1FRs??	?Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ vim 

Gs-MacBook-Pro:~ g$ defaults read /Library/Preferences/SystemConfiguration/com.apple.smb.server NetBIOSName

Gs-MacBook-Pro

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ 

Gs-MacBook-Pro:~ g$ defaults read /Library/Preferences/SystemConfiguration/com.apple.smb.server NetBIOSName

Gs-MacBook-Pro

Gs-MacBook-Pro:~ g$ 