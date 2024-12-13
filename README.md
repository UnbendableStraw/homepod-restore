# How to restore the first generation A1639 Apple HomePod! 

## Disclaimer

READ THROUGH THE COMPLETE GUIDE FIRST BEFORE TOUCHING **_ANYTHING!_** Then, re-read it again before you attempt an actual restore, as steps / software may have changed.

You perform all of this at your own risk with no promises, guarantees, warranty, whatever. Mistakes made with software can be restored with software, but mistakes made with hardware may need repair. Approach with care. Only USB Restore as a last resort.

This guide was tested on Apple Silicon / Intel with MacOS. It _should_ work on Linux, but do not ask me for help here. Ask in the [discord server](https://discord.gg/track44). Feel free to contribute steps to getting other platforms working.

You can only restore with currently signed HomePod OS versions! You can download the latest currently signed version to restore with [here,](https://nicsfix.com/ipsw/18.2.ipsw) or for fun try building your own using the steps at the bottom of this guide. 

https://nicsfix.com/ipsw/18.2.ipsw

https://nicsfix.com/ipsw/18.1.ipsw

## How to restore your HomePod!
### Prerequisites

p.0. You need a usb connection to your HomePod! The port on the pod is under the rubber base. Remove the base by wedging your fingers between the base and mesh, and prying it straight off. No heat necessary. It will stick right back on.  
> [!TIP]
> I strongly suggest cleaning all the black adhesive off the base of your HomePod around the debug port to allow your adapter to sit flush and close. You can easily clean all the adhesive by rolling itself into a ball and peeling it off.

p.0.0. While you can solder a usb cable directly to the debug port on the HomePod, it's not recommended, the pads are fragile. You can [make your own adapter,](https://github.com/UnbendableStraw/homepwn-simple) or buy premade adapters [here!](https://nicsfix.com/shop)

p.1. You need Homebrew installed: <https://brew.sh/>
> [!WARNING]
> On some Macs, after you install HomeBrew in your terminal, you will see "Next Steps, Run these commands..." Run those commands!!

p.2. Now run these commands, one at a time. This taps David's repo, then installs the necessary restore tools. For now, we are also installing a specific version of idevicerestore to workaround a bug in the latest version:
```
brew tap d235j/ios-restore-tools
brew install --HEAD libimobiledevice-glue
brew install --HEAD d235j/ios-restore-tools/libimobiledevice
brew install --HEAD libirecovery
brew install --HEAD gaster
brew install --HEAD ldid-procursus
brew install libzip
git clone https://github.com/libimobiledevice/idevicerestore.git 
cd idevicerestore
git checkout d2e1c4f
./autogen.sh
make
sudo make install
```

Confirm idevicerestore works by running the command `idevicerestore -v` you should see:

`idevicerestore 1.0.0-215-gd2e1c4f (libirecovery 1.2.1, libtatsu 1.0.3)`. 

Confirm HomeBrew is working by running `brew doctor` you should see:

`Your system is ready to brew!`

You are now set up to restore HomePods! From here on, you just need to run the next steps anytime you want to restore a(nother) HomePod unless any major updates come out.

### Restore Steps

1. IN ORDER: Place and keep your HomePod upside down. Then, connect the adapter to the HomePod. Then, plug the usb cable into computer. Only then, can you plug power into HomePod. 

  * * It is important you do this in order. With the adapter attached to your HomePod, keep pod upside down. USB to PC first. Then power to pod. 

  * * Some macs will have "Allow Accessory to Connect" prompts along the way, Allow them! Pay attention to more prompts while running restore process, too.

2. Run the following commands, one at a time. Replace YOUR.ipsw with the location of your .ipsw file. You can use my latest signed .ipsw [here](https://nicsfix.com/ipsw/18.2.ipsw)

> [!TIP]
> Drag your .ipsw file from a Finder window, into your Terminal window, to populate it's path!

```
gaster pwn
gaster reset
idevicerestore -d -e YOUR.ipsw
```

3. Be patient! It can take up to 10-15 minutes. Once you see `Restore Complete`, it will still appear to be bootlooping / blinking volume buttons, thats expected! 

Once you see `Restore Complete`, you can unplug power from your HomePod, then unplug the usb cable, then remove adapter from HomePod. Flip the HomePod rightside up, reconnect power and wait for it to reboot a few times and initialize set-up! 


> [!CAUTION]
> Do not disturb your dongle, HomePod, or any part of the USB cable while restoring. The pads are close together, with voltage next to data lines, and you risk failing to restore, or worse, shorting and damaging something. When you remove the adapter from the HomePod, be sure the USB cable is unplugged first, and power isn't plugged into the HomePod!

## Troubleshooting

If the restore is unsuccessful, try again from Restore Step 1. Usually though, errors are caused by faulty hardware, bad connection to the HomePod, or you didn't do something right. See if you recognize any of the errors below with what you got...

* If you get an ERROR: about `Unable to get SHSH blobs for this device` or `This device isn't eligible` or `Unable to send iBEC to device`, you are likely using the wrong version of idevicerestore, or an unsigned .ipsw. Try redownloading or rebuilding your .ipsw, and try running `brew uninstall d235j/ios-restore-tools/idevicerestore` to install the specific version of idevicerestore needed from Prerequisite Step 4.

* If you get an error running `make`  during Prerequisite Step 4, or while running idevicerestore, saying `error: call to undeclared function 'irecv_init';`, you will need to work around this by modifying a file;
* * Go to where your idevicerestore files checked out (usually "/Users/$USER/idevicerestore/src"), edit the file "dfu.c", and delete line 87
`irecv_init();` <- Delete this!
* * Now you can proceed through the rest of the guide...
* * `./autogen.sh`
* * `make`
* * `sudo make install`



* You may see many repeating timeout messages like so:
>
> ```
> ReverseProxy[Conn]: Received Ping command, replying with Pong
> ReverseProxy[Conn]: Connection closed
> ReverseProxy[Conn]: (status=2) Terminated
> No data to read (timeout)
> No data to read (timeout)
> No data to read (timeout)
> No data to read (timeout)
> No data to read (timeout)
>```
>
This is normal and may take up to 10–15 minutes.

Other Tips:

* Is your HomePod upside down while attempting all of this?
* Did you connect the HomePod's USB first, then connect power to HomePod?
* Did you run `gaster pwn`, then `gaster reset`, then `idevicerestore -d -e YOUR.ipsw` all while your HomePod was connected, on, and upside down?
* Restart your computer and try again from Restore Step 1.
* If you get an `Unable to restore device` error because of `failure when attempting to flash the nitrogen firmware`, try again from step 1 and it should work.
* If you consistently get `Possibly invalid iBec` error, or `Waiting on NAND` during restore, it's probably hardware failure (nand / bga)
* If you are not able to `gaster pwn` your HomePod, it's likely a hardware failure or bad USB connection.
* Basically, it _should_ restore successfully if it's purely a software brick, and your connection to the HomePod is good.



## How to build your own .ipsw

You don't need to do this, only if you want to try building an .ipsw yourself. Accomplish the prerequisites first. May get easier in the future.

1. Download the latest ./makeipsw.sh from [tihmstar's repo](https://github.com/tihmstar/homepodstuff)
2. Download the desired signed, full OTA .zip for AudioAccessory1,1 (from ipsw.me). You need the one with the full ramdisk (usually the largest one with no prerequisites)
3. Download the same version of the signed full .ipsw for AppleTV5,3 (from ipsw.me)
* example: [this](https://ipsw.me/download/ota/AudioAccessory1,1/21M71?prerequisite=) for the ota.zip and [this](https://ipsw.me/download/AppleTV5,3/21M71) for the .ipsw file
4. Download the `firmware_keys.zip` from this repo. Currently working for 17.5, 17.6, and 18.0. (new / self key generation coming soon to makeipsw.sh script)
5. `chmod +x makeipsw.sh`
6. `brew reinstall ra1nsn0w` (to ensure you have the latest version)
7. `./makeipsw.sh PATH_TO_HOMEPOD_OTA.zip PATH_TO_APPLETV.ipsw PATH_TO_OUTPUT.ipsw PATH_TO_firmware_keys.zip`
* Point the first path to the latest signed, FULL ota .zip for AudioAccessory1,1 file you downloaded
* Point the second path to the latest signed, FULL .ipsw for AppleTV5,3 file you downloaded
* Point the third path to where you want your .ipsw to be output, along with a name (like ~\Desktop\nic\homepod.ipsw)
* Point the fourth path to where you downloaded the keys.zip file from this repo


## Credit
Huge thanks to thimstar, and David Ryskalczyk, for making all of this happen. Consider donating to them if you found this helpful! I do the easy work, making the how-to's, testing, support, and attempting to keep things up to date. 

* https://github.com/tihmstar
* https://www.patreon.com/tihmstar


### Looking for more HomePod repairs? Visit https://nicsfix.com or join my discord server for more help at https://discord.gg/track44
