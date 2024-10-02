# How to restore the first generation A1639 Apple HomePod! 
## Disclaimer

READ THROUGH THE COMPLETE GUIDE FIRST BEFORE TOUCHING **_ANYTHING_**

You perform all of this at your own risk with no promises, guarantees, warranty, whatever. Mistakes made with software can be restored with software, but mistakes made with hardware will need repair. Approach with care.

##
Video Guide: https://www.youtube.com/watch?v=VCm-ac8EmaE

## How to restore your HomePod
### Prerequisites

p.0. You need a usb connection to your HomePod. the port on the pod is under the rubber base. remove the base by wedging your fingers between the base and mesh, pry it straight off. no heat necessary. it will stick back on with no issues.

p.0.0. While you can solder directly to the debug port on the pod, it's not recommended. the pads are fragile. You can [make your own adapter](https://github.com/UnbendableStraw/homepwn-simple) (based on [tihmstar's design](https://github.com/tihmstar/homepwn)) you only need the four usb 2.0 wires for restoring. You can buy premade adapters [here!](https://nicsfix.com/shop)

p.0.0.0. Do not disturb your dongle, HomePod, or any part of the USB cable during restore. the pads are close together with voltage next to data lines and you risk shorting and damaging something if mistakes are made. when unplugging things, it's best to disconnect power from pod first, then unplug dongle usb, then remove dongle from pod.

p.1. This guide was tested on Apple Silicon with MacOS. It _should_ work on Intel / Windows / Linux, but do not ask me for help, ask in the [discord server](https://discord.gg/track44). Feel free to contribute steps to getting other platforms working and submit a pull request.

p.2. A currently signed .ipsw file. Download mine at https://nicsfix.com/ipsw/18.0.ipsw or build your own. 

p.3. Homebrew <https://brew.sh/>

**Note: _On some Macs, after you install homebrew for the first time in your terminal, you will see "Next Steps, Run these two commands..." Run those two commands!!_**

p.4. Now run these commands, one at a time. This taps David's repo, then installs the necessary restore tools. For now, we are also installing a specific version of idevicerestore to workaround a bug in the latest version:
```
brew tap d235j/ios-restore-tools
brew install --HEAD libimobiledevice-glue
brew install --HEAD d235j/ios-restore-tools/libimobiledevice
brew install --HEAD libirecovery
brew install --HEAD gaster
brew install --HEAD ldid-procursus
git clone https://github.com/libimobiledevice/idevicerestore.git 
cd idevicerestore
git checkout d2e1c4f
./autogen.sh
make
sudo make install
```

If you get no errors (warnings are OK) then you are ready to restore HomePods!

### Restore Steps

1. IN ORDER: Place and keep your HomePod upside down. Then, connect the adapter to the HomePod. Then, plug the usb cable into computer. Only then, can you plug power into HomePod. 

  * * It is important you do this in order. Keep pod upside down. USB to PC first. Then power to pod. 

  * * Some macs will have "Allow Accessory to Connect" prompts along the way, Allow them! Pay attention to more prompts while running restore process, too.

2. Run the following commands, one at a time. Replace YOUR.ipsw with the location of your .ipsw file. You can drag your .ipsw file from Finder into your Terminal window to populate it's path. 

```
gaster pwn
gaster reset
idevicerestore -d -e YOUR.ipsw
```
* * Do not disturb HomePod or cable during restore process 

3. Be patient! It can take up to 10-15 minutes. Once you see `Restore Complete` continue waiting about a minute, then you'll see it reconnect in DFU mode again. You can now unplug power, then usb, then remove adapter from HomePod, flip rightside up, reconnect power and wait for it to reboot a few times to initialize set-up!

Now you can set it up and use like any other HomePod. This does not break over the air updates or any features / functionality.

## Troubleshooting

If the restore is unsuccessful, try again from Restore Step 1. Usually though, errors are caused by faulty hardware, bad connection to the HomePod, or you didn't do something right.

* If you get an error about "Unable to get SHSH blobs for this device" or "This device isn't eligible", you are likely using an incompatible version of idevicerestore. If you intsalled it with `brew install --HEAD idevicerestore`, then uninstall it with `brew uninstall d235j/ios-restore-tools/idevicerestore` and install the specific version in Prerequisite Step 4.
* Is your HomePod upside down while attempting all of this?
* Did you connect the HomePod's USB first, then connect power to HomePod?
* Did you run `gaster pwn`, then `gaster reset`, then `idevicerestore -d -e YOUR.ipsw` all while your HomePod was connected, on, and upside down?
* Restart your computer and try again from Restore Step 1.
* If you get an `Unable to restore device` error because of `failure when attempting to flash the nitrogen firmware`, try again from step 1 and it should work.
* If you consistently get `Possibly invalid iBec` error, or `Waiting on NAND` during restore, it's probably hardware failure (bga / nand)
* Basically, it _should_ restore successfully if it's purely a software brick, and your connection and cables to the HomePod are good enough.
* If you arent able to get a USB commection to your HomePod, look for any balled-up bits of the black adhesive keeping your adapter from sitting flush. You may need to clean all the adhesive off the bottom of your HomePod, to allow the adapter to make closer contact.

* You may see many repeating timeout messages like so:
>
> ```
> ReverseProxy[Ctrl]: (status=3) Connect Request
> ReverseProxy[Conn]: Got device identifier [redacted]
> ReverseProxy[Conn]: (status=1) Ready
> ReverseProxy[Conn]: Received Ping command, replying with Pong
> ReverseProxy[Conn]: Connection closed
> ReverseProxy[Conn]: (status=2) Terminated
> No data to read (timeout)
> No data to read (timeout)
> No data to read (timeout)
> ```
>
This is normal and may take up to 10–15 minutes.


## How to build your own .ipsw

You don't need to do this, only if you want to try building an .ipsw yourself. Accomplish the prerequisites first. This is actively being worked on to be made easier. 

1. Download the latest ./makeipsw.sh from [tihmstar's repo](https://github.com/tihmstar/homepodstuff)
2. Download the desired signed, full OTA .zip for AudioAccessory1,1 (from ipsw.me). You need the one with the full ramdisk (usually the largest one with no prerequisites)
3. Download the same version of the signed full .ipsw for AppleTV5,3 (from ipsw.me)
* example: [this](https://ipsw.me/download/ota/AudioAccessory1,1/21M71?prerequisite=) for the ota.zip and [this](https://ipsw.me/download/AppleTV5,3/21M71) for the .ipsw file
4. Download the `firmware_keys.zip` from this repo. Currently working for 17.5 and 17.6 (new / self key generation coming soon to makeipsw.sh script)
5. `chmod +x makeipsw.sh`
6. `brew reinstall ra1nsn0w` (to ensure you have the latest version)
7. `./makeipsw.sh PATH_TO_homepod_OTA.zip PATH_TO_APPLETV.ipsw PATH_TO_OUTPUT.ipsw PATH_TO_firmware_keys.zip`
* Point the first path to the latest signed, FULL ota .zip for AudioAccessory1,1 file you downloaded
* Point the second path to the latest signed, FULL .ipsw for AppleTV5,3 file you downloaded
* Point the third path to where you want your .ipsw to be output, along with a name (like ~\Desktop\nic\homepod.ipsw)
* Point the fourth path to where you downloaded the keys.zip file from this repo


## Credit
Huge thanks to thimstar, and David Ryskalczyk, for making all of this happen. All I've done is test and put guides together! Consider donating to them if you found this helpful.

* https://github.com/tihmstar
* https://www.patreon.com/tihmstar


### Looking for more HomePod repairs? Visit https://nicsfix.com or join my discord server for more help at https://discord.gg/track44
