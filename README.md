# How to restore the first generation A1639 Apple HomePod!
## Work In Progress

Download the latest IPSW here: https://nicsfix.com/ipsw/pod17.5.ipsw

Note: When 17.5 becomes unsigned, a new .ipsw will need to be rebuilt. We will try to share new .ipsws here as old versions become unsigned. 



### Prerequisites:

p.0. you need a usb connection to your homepod. the port on the pod is under the rubber base. remove the base by wedging your fingers between the base and mesh, pry it straight off. no heat necessary. 

p.0.0. see tihmstar's repo for info on crafting your own usb dongle: https://github.com/tihmstar/homepwn/tree/main/homebreakout_3dprint

p.0.1 image of basic USB pinout https://nicsfix.com/media/debug.jpg

p.1. you need a mac (tested on apple silicon)

p.2. your homepod needs to show as recovery mode on your mac when powered on oriented normally. If it never connects in Recovery Mode, or it only gives DFU mode, it's likely a hw issue.

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

2. run the following

```
gaster pwn
gaster reset
idevicerestore -d -e PATH_TO_.ipsw
```

* If you get an `Unable to restore devie` error because of `failure when attempting to flash the nitrogen firmware`, try again from step 1 and it should work. If you consistently get `Possibly invalid iBec` error during restore, it's probably hardware failure (bga / nand)

3. Be patient. Takes a few minutes. Once you see `Restore Complete` continue waiting about a minute. Then unplug power / usb, flip rightside up, reconnect power and wait for setup chime

## Building your own IPSW

If you want to try building an .ipsw yourself, accomplish the prerequisites first. This may be easier in the future. For now:

1. `mkdir -p _httpserver/firmware/AudioAccessory1,1/0x7000`
2. Generate keys (details to come). This repo has key "21L569" for 17.5 attached for you to try. Put your key inside the 0x7000 folder
3. `cd _httpserver`
4. `python3 -m http.server 8888`
5. leave the python http server running, and open a new terminal
6. Download ./makeipsw.sh from tihmstar's repo: https://github.com/tihmstar/homepodstuff
7. `chmod +x makeipsw.sh`
8. `git checkout homepod` 
9. `./makeipsw.sh PATH_TO_HOMEPOD_OTA.zip PATH_TO_APPLETV.ipsw PATH_TO_OUTPUT.ipsw`
* Point the first path to the latest signed, FULL ota for AudioAccessory1,1 
* Point the second path to the latest signed, FULL ipsw for AppleTV5,3 
* Point the last path to where you want your ipsw to be output, along with a name (like ~\Desktop\nic\homepod.ipsw)


### Credit
Huge thanks to thimstar, and David Ryskalczyk, for making all of this happen. All I've done is test and put guides together! Consider donating to them if you found this helpful

* https://github.com/tihmstar
* https://www.patreon.com/tihmstar

### Looking for more HomePod repairs? Visit https://nicsfix.com!
