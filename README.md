# How to restore the first generation A1639 Apple HomePod!
### Work In Progress
## Disclaimer

You perform all of this at your own risk with no promises, guarantees, warranty, whatever. Mistakes made with software can be restored with software, but mistakes made with hardware will need repair. Approach with care

##
17.6: https://nicsfix.com/ipsw/17.6.ipsw

17.5 (no longer signed): https://nicsfix.com/ipsw/pod17.5.ipsw

Video Guide: https://www.youtube.com/watch?v=VCm-ac8EmaE

## How to restore your HomePod
### Prerequisites

p.0. you need a usb connection to your homepod. the port on the pod is under the rubber base. remove the base by wedging your fingers between the base and mesh, pry it straight off. no heat necessary. it will stick back on with no issues.

p.0.0. while you can solder directly to the pins on the pod, it's not recommended, the pads are fragile. You can [make your own adapter](https://github.com/UnbendableStraw/homepwn-simple) (based on [tihmstar's design](https://github.com/tihmstar/homepwn)) you only need the four usb 2.0 wires for restoring. for troubleshooting hardware issues, you will need UART. I am also selling premade adapters [here!](https://nicsfix.com/shop)

p.0.0.0. avoid removing / disturbing dongle while the pod is powered. the pads are close together with voltage next to data lines and you risk shorting and damaging something if mistakes are made. it's best to disconnect power from pod first, then unplug dongle usb, then remove dongle from pod

p.1. tested on apple silicon, may work on intel / linux with additional tweaks 

p.2. your homepod should show as Recovery Mode on a connected computer when powered on oriented normally. If it never connects in Recovery Mode, or it only gives DFU mode, it's likely a hw issue, but you can still attempt to restore. this is just for reference, you will actually restore in DFU mode upside down. 

p.3. Homebrew <https://brew.sh/>

p.3.0. after you install homebrew for the first time in your terminal, you will see "Next Steps, Run these two commands..." Run those two commands!! 

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

Now you can restore homepods!

### Restore Steps
#### Video guide soon!

1. with pod powered off, connect homepod via usb to computer. then place homepod upside down. then power on homepod

* allow any accessory connect prompts (pay attention to ones while running restore process too)

2. run the following, replacing YOUR.ipsw with the location of your .ipsw file

```
gaster pwn
gaster reset
idevicerestore -d -e YOUR.ipsw
```

* If the restore is unsuccessful, just try again! But...
* If you get an `Unable to restore device` error because of `failure when attempting to flash the nitrogen firmware`, try again from step 1 and it should work.
* If you consistently get `Possibly invalid iBec` error, or `Waiting on NAND` during restore, it's probably hardware failure (bga / nand)
* Basically, it _should_ restore successfully if it's purely a software brick, and your connection and cables to the homepod are good enough. 

3. Be patient. Takes a few minutes. Once you see `Restore Complete` continue waiting about a minute. Then unplug power first, then usb, flip rightside up, reconnect power and wait for setup chime. At this point it can be set up and used like any other homepod. This does not break over the air updates or any features / functionality. 

## How to build your own .ipsw

If you want to try building an .ipsw yourself, accomplish the prerequisites first. This is actively being worked on to be made easier. 

1. Download the latest ./makeipsw.sh from [tihmstar's repo](https://github.com/tihmstar/homepodstuff)
2. Download the desired signed, full OTA .zip for AudioAccessory1,1 (from ipsw.me). You need the one with the full ramdisk (usually the largest one with no prerequisites)
3. Download the same version of the signed full .ipsw for AppleTV5,3 (from ipsw.me)
* example: [this](https://ipsw.me/download/ota/AudioAccessory1,1/21M71?prerequisite=) for the ota.zip and [this](https://ipsw.me/download/AppleTV5,3/21M71) for the .ipsw file
4. Download the `_keys_17.5_and_17.6.zip` from this repo. Currently working for 17.5 and 17.6 (new / self key generation coming soon to makeipsw.sh script)
5. `chmod +x makeipsw.sh`
6. `brew reinstall ra1nsn0w` (to ensure you have the latest version)
7. `./makeipsw.sh PATH_TO_HOMEPOD_OTA.zip PATH_TO_APPLETV.ipsw PATH_TO_OUTPUT.ipsw PATH_TO_KEYS.zip`
* Point the first path to the latest signed, FULL ota .zip for AudioAccessory1,1 file you downloaded
* Point the second path to the latest signed, FULL .ipsw for AppleTV5,3 file you downloaded
* Point the third path to where you want your ipsw to be output, along with a name (like ~\Desktop\nic\homepod.ipsw)
* Point the fourth path to where you downloaded the keys.zip file from this repo


## Credit
Huge thanks to thimstar, and David Ryskalczyk, for making all of this happen. All I've done is test and put guides together! Consider donating to them if you found this helpful

* https://github.com/tihmstar
* https://www.patreon.com/tihmstar


### Looking for more HomePod repairs? Visit https://nicsfix.com or join my discord server for more help at https://discord.gg/track44
