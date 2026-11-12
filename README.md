# HomePod Restore

How to USB-restore a first-generation Apple HomePod (A1639).

> [!TIP]
> **Pre-built, signed .ipsw (18.3):** https://nicsfix.com/ipsw/18.3.ipsw

- [Before you start](#before-you-start)
- [Will this fix my HomePod?](#will-this-fix-my-homepod)
- [What you need](#what-you-need)
- [One-time setup](#one-time-setup)
- [Restoring a HomePod](#restoring-a-homepod)
- [Troubleshooting](#troubleshooting)
- [Building your own .ipsw](#building-your-own-ipsw)
- [Credits](#credits)

---

## Before you start

> [!IMPORTANT]
> Read this **entire** guide before touching anything. Then read it again right before you restore, something may have changed!

You do all of this at your own risk. No promises, guarantees or warranty. Software mistakes can be fixed with software, but hardware mistakes may need a hardware repair. Try this if nothing else works. 

## Will this fix my HomePod?

It might, if your HomePod has symptoms like:

- Blinking volume buttons
- Boot looping, or booting forever
- Failing to set up, configure or update (especially if it's on OS 11, 12, 13, and even some versions of 14! These have NO known upgrade path on their own!)

Some of these come from software, some from hardware.  The only way to tell is to attempt a USB restore. A HomePod with **no power** almost always has hardware issues this won't fix...check my Repair Guides to learn more about that.

**Limits:**

- You can only restore currently signed HomePod OS versions, using the tools in this guide. Clicking Restore in Finder won't work, and neither will older versions. Check back here now and then for the latest .ipsw.
- This guide is tested on macOS (Apple silicon and Intel). It *should* work on Linux, but I can't help you much better than AI can there. You can also get help in my [Discord server](https://discord.gg/nicsfix). 

> [!WARNING]
> **Restore with 18.3.** Some firmware versions (most recently 26.0.1) end up in a boot loop with briefly flashing volume buttons after a USB restore and reset. For whatever reason, Apple still signs 18.3, and 18.3 doesn't do this. Restore with 18.3, then let the HomePod set up and update as usual from the Home app.

## What you need

- A first-gen HomePod (A1639)
- A Mac
- **A USB adapter for the HomePod's debug port.** You can [buy a premade adapter](https://nicsfix.com/shop) or [try to make your own](https://github.com/UnbendableStraw/homepwn-simple). You *can* solder a USB cable straight to the debug port, but I don't recommend it. The pads are very fragile, and IF you manage to not mangle it, leftover solder can still inhibit a proper usb adapter's connection.
- [Homebrew](https://brew.sh/)
- A signed .ipsw. [Download mine (18.3)](https://nicsfix.com/ipsw/18.3.ipsw) or [build your own](#building-your-own-ipsw).

### Getting to the debug port

The port is under the HomePod's rubber base. Wedge your fingers between the base and the mesh and pry it straight off. You don't need heat, and the base sticks right back on afterwards. 

> [!TIP]
> You might break a few of the little plastic tabs on your rubber base...snap off any that aren't still standing straight up or it will keep your base from sitting flat again! These are not critical.

> [!WARNING]
> Clean all the black adhesive off the bottom of the HomePod around the debug port so your adapter can sit flush and be removed later without excessive force, risking damage to your adapter. The adhesive rolls into a ball and peels off easily. 

## First-time setup

**1. Install [Homebrew](https://brew.sh/).**

> [!WARNING]
> On some Macs the Homebrew installer finishes with **"Next steps: Run these commands..."** Run those commands!

**2. Install the restore tools.** Run these commands one at a time. They tap David's repo and install the restore tools, and builds a specific version of `idevicerestore` (`d2e1c4f`) to work around a bug in later versions.

```sh
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

If `make` fails with `error: call to undeclared function 'irecv_init'`, see [Troubleshooting](#make-fails-with-irecv_init)!

**3. Check that everything works;**

| Command | Expected output |
| --- | --- |
| `idevicerestore -v` | `idevicerestore 1.0.0-215-gd2e1c4f (libirecovery 1.2.1, libtatsu 1.0.3)` |
| `brew doctor` | `Your system is ready to brew!` |

You're now set up to restore HomePods! From here on, you only need the steps below each time you restore a HomePod.

## Restoring a HomePod

**1. Connect everything, in this exact order:**

1. Flip the HomePod upside down, and keep it upside down the whole time.
2. Attach the adapter to the HomePod.
3. Plug the USB cable into your Mac.
4. **Only then** plug power into the HomePod.

> [!TIP]
> Some Macs show **"Allow accessory to connect?"** prompts. Allow them, and keep an eye out for more prompts while the restore runs.

**2. Run these commands one at a time**, replacing `YOUR.ipsw` with the path to your .ipsw:

```sh
gaster pwn
gaster reset
idevicerestore -d -e YOUR.ipsw
```

> [!TIP]
> Drag your .ipsw from Finder into the Terminal window to fill in its path and skip typos.

**3. Be patient.** The restore can take 10–15 minutes. Lots of repeating `No data to read (timeout)` messages are normal. If / when you see `Restore Complete`, the HomePod will still look like it's boot looping with blinking volume buttons. That's expected! There may also be a few extra log messages after this. 

> [!CAUTION]
> Don't bump the adapter, the HomePod or the USB cable while it's restoring. The pads are close together, with voltage right next to the data lines, so a bump can fail the restore or, worse, short and damage something. Before you remove the adapter, make sure the USB cable is unplugged and the HomePod has no power.

**4. Disconnect, in this order:**

1. Unplug power from the HomePod.
2. Unplug the USB cable.
3. Remove the adapter.

Flip the HomePod right side up, plug power back in, and wait. It will reboot a few times and then go into setup.


## Troubleshooting

If a restore fails, start over from [step 1 of the restore](#restoring-a-homepod). Most failures come from faulty hardware, a bad connection to the HomePod, or a missed step. If it's purely a software brick and your connection is good, it *should* restore.

**Quick checks:**

- Is the HomePod upside down?
- Did you plug in USB first, and *then* power to the HomePod?
- Did you run `gaster pwn`, `gaster reset` and `idevicerestore -d -e YOUR.ipsw` while the HomePod was connected, powered on and upside down?
- Try restarting your Mac and starting again from step 1.

### `make` fails with `irecv_init`

If you get `error: call to undeclared function 'irecv_init'` while running `make` during setup, or while running `idevicerestore`:

1. Open `dfu.c` in the `src` folder of your idevicerestore clone (usually `~/idevicerestore/src/dfu.c`) with your preferred text / code editor
2. Delete line 87, which reads `irecv_init();` and save your changes!
3. Rebuild from the `idevicerestore` folder:
   ```sh
   ./autogen.sh
   make
   sudo make install
   ```

### Common errors

| You see | Likely cause | What to do |
| --- | --- | --- |
| `Unable to get SHSH blobs for this device`, `This device isn't eligible` or `Unable to send iBEC to device` | Wrong version of idevicerestore, or an unsigned .ipsw | Re-download or rebuild your .ipsw. If you have the Homebrew version of idevicerestore installed, remove it with `brew uninstall d235j/ios-restore-tools/idevicerestore` so the pinned version from [setup](#one-time-setup) is used. |
| Many repeating `No data to read (timeout)` / `ReverseProxy` messages | Normal | Wait. It can take 10–15 minutes. |
| `Unable to restore device` with `failure when attempting to flash the nitrogen firmware` | Usually a one-off | Try again from step 1. It usually works. |
| `gaster pwn` won't run | Hardware failure or a bad USB connection | Check that the adapter is seated flush and the base is clean. |
| `Waiting on NAND`, then `storage device did not become available` and `Timeout waiting for NVRAM Service` | Failed NAND or NAND BGA | Hardware repair |
| `Possibly invalid iBec` or `Device failed to enter restore mode. Please make sure that usbmuxd is running.` | Failed A8 SoC or SoC BGA | Hardware repair |

The normal timeout messages look like this:

```
ReverseProxy[Conn]: Received Ping command, replying with Pong
ReverseProxy[Conn]: Connection closed
ReverseProxy[Conn]: (status=2) Terminated
No data to read (timeout)
No data to read (timeout)
No data to read (timeout)
```

I'm keeping a [spreadsheet of known errors and behaviors](https://docs.google.com/spreadsheets/d/1Fvoa8zhKQzLCrZxgvBHMtxuQuXDyvi9EKi9KwdjbxGo/edit?usp=sharing).

## Building your own .ipsw

This is optional. Only do it if you want to build an .ipsw yourself, and finish the [one-time setup](#one-time-setup) first. It may get easier in the future.

> [!NOTE]
> See the [warning above](#will-this-fix-my-homepod). Versions newer than 18.3 may restore fine but boot loop after a reset.

**You'll need:**

1. The latest `makeipsw.sh` from [tihmstar's homepodstuff repo](https://github.com/tihmstar/homepodstuff).
2. The signed, **full** OTA .zip for `AudioAccessory1,1` from [ipsw.me](https://ipsw.me). Use the one with the full ramdisk, which is usually the largest one with no prerequisites.
3. The signed, full .ipsw for `AppleTV5,3` of the **same version**, also from [ipsw.me](https://ipsw.me).
   - Example for 21M71: [HomePod OTA .zip](https://ipsw.me/download/ota/AudioAccessory1,1/21M71?prerequisite=) and [Apple TV .ipsw](https://ipsw.me/download/AppleTV5,3/21M71)
4. [`firmware_keys.zip`](firmware_keys.zip) from this repo. It has keys for builds `21L569`, `21M71`, `22J357`, `22J580`, `22K155`, `22K557`, `22L255`, `22L261`, `22L572`, `22M84`, `23J353` and `23J362` (17.5 through 26.0.1). Self-generated keys are planned for `makeipsw.sh`.

**Then run:**

```sh
chmod +x makeipsw.sh
brew reinstall ra1nsn0w   # make sure you have the latest version
./makeipsw.sh PATH_TO_HOMEPOD_OTA.zip PATH_TO_APPLETV.ipsw PATH_TO_OUTPUT.ipsw PATH_TO_firmware_keys.zip
```

| Argument | Points to |
| --- | --- |
| `PATH_TO_HOMEPOD_OTA.zip` | The full `AudioAccessory1,1` OTA .zip you downloaded |
| `PATH_TO_APPLETV.ipsw` | The full `AppleTV5,3` .ipsw you downloaded |
| `PATH_TO_OUTPUT.ipsw` | Where to save the new .ipsw, including its name (for example `~/Desktop/homepod.ipsw`) |
| `PATH_TO_firmware_keys.zip` | The `firmware_keys.zip` from this repo |

## Credits

Huge thanks to **tihmstar** and **David Ryskalczyk** for making all of this possible. If this helped you, consider supporting them! I do the easy part: the how-tos, testing, support and keeping things up to date. Shout out to Claude for rewriting some of the guide, making it a little easier for everyone to read.


- tihmstar: [GitHub](https://github.com/tihmstar) · [Patreon](https://www.patreon.com/tihmstar)
- David Ryskalczyk: [GitHub](https://github.com/d235j)

---

**Need more HomePod repairs?** Visit [nicsfix.com](https://nicsfix.com) or join the [Discord](https://discord.gg/nicsfix) for help.
