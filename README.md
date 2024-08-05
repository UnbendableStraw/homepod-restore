# How to restore the first generation A1639 Apple HomePod!
## Work In Progress

17.6: https://nicsfix.com/ipsw/17.6.ipsw
17.5: https://nicsfix.com/ipsw/pod17.5.ipsw

When 17.5 / 17.6 becomes unsigned, a new .ipsw will need to be rebuilt. We will try to share new .ipsws here as old versions become unsigned, and everyone will be able to make their own soon!

### Prerequisites:

p.0. you need a usb connection to your homepod. the port on the pod is under the rubber base. remove the base by wedging your fingers between the base and mesh, pry it straight off. no heat necessary. 

p.0.0. while you can solder directly to the pins on the pod, it's not recommended, the pads are fragile. see [tihmstar's repo](https://github.com/tihmstar/homepwn/tree/main/homebreakout_3dprint) for info on crafting your own usb dongle! 

p.0.0.0 avoid unplugging the pod side of the dongle while the pod is powered, since the pads are so close together you risk shorting and frying something. it's best to disconnect all power from pod before you unplug your dongle!

p.1. you need a mac (tested on apple silicon, may work on intel)

p.2. your homepod needs to show as recovery mode on your mac when powered on oriented normally. If it never connects in Recovery Mode, or it only gives DFU mode, it's likely a hw issue. this is just for reference, you will actually restore in DFU mode upside down!

p.3. Homebrew <https://brew.sh/>

p.4.
```
brew tap d235j/ios-restore-tools
brew install --HEAD libimobiledevice-glue
brew install --HEAD d235j/ios-restore-tools/libimobiledevice
brew install --HEAD libirecovery
brew install --HEAD idevicerestore
brew install --HEAD gaster
brew install --HEAD ldid-procursus
```

### Now you can restore homepods!

1. with pod powered off, connect homepod via usb to pc, then place homepod upside down, then power on 

* allow any accessory connect prompts (pay attention to ones while running restore process too)

2. run the following, replacing YOUR.ipsw with the location of your .ipsw file

```
gaster pwn
gaster reset
idevicerestore -d -e YOUR.ipsw
```

* If you get an `Unable to restore devie` error because of `failure when attempting to flash the nitrogen firmware`, try again from step 1 and it should work. If you consistently get `Possibly invalid iBec` error during restore, it's probably hardware failure (bga / nand)

3. Be patient. Takes a few minutes. Once you see `Restore Complete` continue waiting about a minute. Then unplug power first, then usb, flip rightside up, reconnect power and wait for setup chime. At this point it can be set up and used like any other homepod. This does not break over the air updates or any features / functionality. 

## Building your own IPSW

If you want to try building an .ipsw yourself, accomplish the prerequisites first. This is actively being worked on to be made easier. 

1. Download the latest ./makeipsw.sh from [tihmstar's repo](https://github.com/tihmstar/homepodstuff)
2. Download the desired signed, full OTA .zip for AudioAccessory1,1 (from ipsw.me)
3. Download the same version of the signed full .ipsw for AppleTV5,3 (from ipsw.me)
* example: [this](https://ipsw.me/download/ota/AudioAccessory1,1/21L569?prerequisite=) for the ota.zip and [this](https://ipsw.me/download/AppleTV5,3/21L580) for the .ipsw file
4. Download the `_keys_17.5_and_17.6.zip` from this repo. Currently working for 17.5 and 17.6 (new / self key generation coming soon to makeipsw.sh script)
5. `chmod +x makeipsw.sh`
6. `brew reinstall ra1nsn0w` (to ensure you have the latest version)
7. `./makeipsw.sh PATH_TO_HOMEPOD_OTA.zip PATH_TO_APPLETV.ipsw PATH_TO_OUTPUT.ipsw PATH_TO_KEYS.zip`
* Point the first path to the latest signed, FULL ota .zip for AudioAccessory1,1 file you downloaded
* Point the second path to the latest signed, FULL .ipsw for AppleTV5,3 file you downloaded
* Point the third path to where you want your ipsw to be output, along with a name (like ~\Desktop\nic\homepod.ipsw)
* Point the fourth path to where you downloaded the keys.zip file from this repo


### Credit
Huge thanks to thimstar, and David Ryskalczyk, for making all of this happen. All I've done is test and put guides together! Consider donating to them if you found this helpful

* https://github.com/tihmstar
* https://www.patreon.com/tihmstar


### Looking for more HomePod repairs? Visit https://nicsfix.com or join my discord server for more help at https://discord.gg/track44
