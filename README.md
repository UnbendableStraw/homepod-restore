How to restore the first generation A1639 Apple HomePod!

Download the latest IPSW here: https://nicsfix.com/ipsw/pod17.5.ipsw

Note: When 17.5 becomes unsigned, a new .ipsw will need to be rebuilt.


Prerequisites:
-you need a mac (tested on apple silicon)
-your homepod needs to show as recovery mode on your mac when powered on right-side up, not DFU mode. otherwise, it's likely a hw issue.
-homebrew <https://brew.sh/>

`brew tap d235j/ios-restore-tools`
`brew install --HEAD libimobiledevice-glue`
`brew install --HEAD d235j/ios-restore-tools/libimobiledevice`
`brew install --HEAD libirecovery`
`brew install --HEAD idevicerestore`
`brew install --HEAD ra1nsn0w`
`brew install --HEAD gaster`
`brew install --HEAD ldid-procursus`


-connect homepod via usb to pc, place homepod upside down, then power on 
-allow any accessory connect prompts (pay attention to ones while running restore process too)

`gaster pwn`
`gaster reset`
`idevicerestore -d -e PATH_TO_.ipsw`

Note:
-e is needed to erase. enter YES to confirm when prompted
-d is debug flag for extra info, will help troubleshoot if something goes wrong

If you get a failure at nitrogen firmware stage, try again from step 1 and it should work. If you consistently get `Possibly invalid iBec` error during restore, it's probably hardware failure (bga / nand)

Be patient. Takes a few minutes. Once you see `Restore Complete` continue waiting about a minute. Then unplug power / usb, flip rightside up, reconnect power and wait for setup chime


