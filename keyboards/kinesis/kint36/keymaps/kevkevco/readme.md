# kevkevco's Kinesis Advantage2 keymap
Tested with a Kinesis Advantage2, kinT (stapelberg) keyboard controller built
with a Teensy 3.6 microcontroller and a USA system layout.

# Approach
This keymap is created to address my tendonitis/tennis elbow symptoms as well as enhance the ease and convenience of coding and generally using my MacBook. As such, I want to provide a bit of the setup that I've created for myself beyond my QMK keymap that has helped. Feel free to reach out to me for more details.
# My Setup
## Keyboard
- Working with upgradekeyboards.com, they changed the switches in my board to Kailh box white switches for ease of use and clear clicky feedback.
- They also installed a new KinT PCB with a Teensy 3.6 controller
- They also added about a pound of weight to the board to make it stay put on the desk, feel more substantial and stable, and perhaps improve the acoustics.
## Keycaps
- I shuffled several of the keycaps around to my ergonomic preferences.
- I used a blank keycap set purchased from Kinesis in order to have the extra keycaps necessary for this.
## Furniture
- Standing desk by Uplift (as recommended by Wirecutter)
- Keyboard tray by Humanscale
- Humanscale Leap v2 Chair

## Technology
- Kensington Slimblade Trackball
- Kensington Expert Trackball Wireless
- Evoluent Right Handed V4MDLW wireless large vertical mouse
- Evoluent Left Handed wired vertical mouse
- Monitors that can adjust to the right positioning
- Logitech G915TKL Wireless Clicky Keyboard for variation
## BT500
Sometimes it is more convenient for me to use my keyboard wirelessly, for example in my lap. For that purpose I used industrial strength velcro to temporarily attach a slim portable battery ~10,000mAh to the back of the keyboard. Mostly for aesthetics I had the native USB cord replaced with a shorter cord (that avoids having extra slack) that plugs into the BT500 which then plugs into the portable battery. There is a few inches of give and take in the cord to slide through the hole in the back of the board that was plugged using a rubber piece that upgradekeyboards.com found.
Note: The Apple Fn key doesn't seem to work using BT500 Bluetooth adapter.
## Other Ergonomic Considerations
- Hand exercises/strengthening seem positive
- Elbow compression sleeve seems to help

# Software
## Steermouse
[Great MacOS software to customize the use of most mouses](http://plentycom.jp/en/steermouse/). You can import and export default profiles. I use this to swap the trackballs I use between my left and right hand but reflect the button assignments
## Karabiner
- I use this to launch applications using my keyboard
- [I've provided my complex modification file as a template](karabiner_example.json)
## Rectangle Pro
I use [this wonderful app](https://rectangleapp.com/pro) to manage my windows. I highly recommend it.
## Teensy Loader
[This application](https://www.pjrc.com/teensy/loader.html) is what I use to flash my keyboard.
# Features
## Apple Fn Key
### Please note: keycode KC_APFN is included in keymap but requires separate implementation.
Included in this keymap is keycode `KC_APFN`, which emulates the native Apple Fn key. It is created using this [Apple Fn patch.](https://gist.github.com/fauxpark/010dcf5d6377c3a71ac98ce37414c6c4) If you do not want to use this keycode, simply remove `KC_APFN` from the "keymap.c" file and remove `APPLE_FN_ENABLE = yes` from the "rules.mk" file.


**Please see the link and comment thread for more detailed instructions than the following summary:**

This patch adds support for the Apple Fn key, which unlike most keyboards with Fn keys, is actually sent over the wire. It works by repurposing the reserved byte in the keyboard report to represent [the `KeyboardFn` usage of the `AppleVendor Top Case` usage page](https://opensource.apple.com/source/IOHIDFamily/IOHIDFamily-1035.70.7/IOHIDFamily/AppleHIDUsageTables.h.auto.html). When the Fn key is pressed, the value of this byte becomes `1`.

To apply this patch, download the below file, `cd` to your `qmk_firmware` repository in your preferred terminal, and run `git apply /path/to/applefn.patch`. Then, add the `KC_APPLE_FN` (or `KC_APFN` for short) keycode to your keymap.

There are a couple of caveats to this implementation that are important to be aware of. Firstly, it is not compatible with NKRO, as QMK's NKRO report format has no reserved byte - it is part of the 6KRO report for compatibility with the HID [boot protocol](https://deskthority.net/wiki/USB#Keyboard_Boot_Protocol). Thus you must set `NKRO_ENABLE = no` in your keymap's `rules.mk`.
You will also need to redefine the USB Vendor and Product IDs in your keymap's `config.h` to that of a genuine Apple keyboard* in order for macOS to recognise the Fn key:
```c
#undef VENDOR_ID
#define VENDOR_ID 0x05AC
#undef PRODUCT_ID
#define PRODUCT_ID <pid>
```
Any IDs from this list should work: https://usb-ids.gowdy.us/read/UD/05ac

This is the primary reason this patch has not been integrated into upstream QMK - Apple would probably not be too happy about others using their vendor ID, and a feature that relies on the VID/PID pair being set to a specific value is not particularly ideal anyway.

See https://github.com/qmk/qmk_firmware/issues/2179 for a little more info and discussion.

<sup>\* It appears that the functionality of certain F keys can differ depending on the PID, likely because they have evolved over time on real Apple keyboards.</sup>

## Home Row Mods
Are inspired by [this article.](https://precondition.github.io/home-row-mods)
## SELECT WORD
Enable Select Word Macro from https://getreuer.info/posts/keyboards/select-word/index.html

## CAPSWORD and NUMWORD
**credit:** [replicaJunction](https://github.com/qmk/qmk_firmware/tree/906108fb486797ab2f3eb7c3a6f70e099c1199e6/users/replicaJunction#capsword-and-numword and Drashna

The concept here is simple: more often than you'd think, you need to type a single word in ALL CAPS. An easy example for me, as a programmer, is a constant value; in most programming languages, constants are typed in all caps by convention.

You typically have a few choices, but each one comes with a drawback. Here are the options I'm aware of:

* Use proper typing technique and alternate which hand holds Shift for each keypress
    * This can often end up requiring you to switch / re-press Shift again and again, making this a tedious process
* Hold a single Shift key down
    * This can lead to uncomfortable finger gymnastics
* Hit the Caps Lock key, then hit it again when you're done
    * Requires you to remember to hit it again, meaning a higher cognitive load
    * In some layouts for smaller keyboards, Caps Lock is not easily accessible (sometimes not mapped at all)
    
The solution to this problem is CAPSWORD. When enabled, it activates Caps Lock and begins running an additional callback on each keypress. If the keypress is an alphanumeric key or one of a specific few symbols (such as the underscore), nothing happens. Otherwise, before processing the keypress, Caps Lock is disabled again.

NUMWORD is a similar concept, but has a slightly more elaborate implementation. There's a bit of extra logic in the NUMWORD code that allows the keycode to act as a tap/hold key as well. Tapping enables NUMWORD while number keys are in use, while holding the key enables a number layer for the duration of the key hold and disables it again afterwards.

**Note:** The implementation of NUMWORD requires that the keyboard's layer definitions be accessible in a header file.
# Misc. Configuration Notes
## Repeating Hold Function
In order to get fast repeating hold function, as if holding down a key on a normal keyboard, on space, enter, delete, forward delete in default layout. Tap and release within tapping term and then immediately trigger same key with a hold right after. This works because I have opted not to use: https://docs.qmk.fm/#/tap_hold?id=tapping-force-hold

## Custom MacOS App Shortcuts
https://support.apple.com/guide/mac-help/create-keyboard-shortcuts-for-apps-mchlp2271/mac
I created two specifically for QMK: Program and Reboot in the Teensy program for quick flashing.