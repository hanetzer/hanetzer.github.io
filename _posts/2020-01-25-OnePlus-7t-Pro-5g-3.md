---
layout: post
title: Hotdogg Wars - Revenge of the Nerds
---

Back again. We have a fix.

As mentioned in a footnote on my first post[^1], I was mistaken about a complete reset destroying the file needed, so all users who are not into custom roms and such stuff are more than likely safe. I'm still unsure if a user who has not unlocked their boot loader will be able to issue the fastboot commands that will destroy it.

We still had to either hope T-Mobile or OnePlus would release a full rom package in a timely manner (I have since recieved a replacement from T-Mobile, so props to them for that at least), or figure out some way to get the file ourself. As of this writing, there is no way to achieve root access on the device without performing an unlock, and as the ownership of `/data/reserve/reserve.img` is `root:root` with permissions `0600`, and selinux context of `u:object_r:apk_data_file:s0`, there is no way on the device to read the file. Infuriatingly, you actually can read the apk files inside it, but even if we were to copy those out, and recreate an ext4 image to replace it, the chances to recreate it 100% enough to pass the checks and successfully patch are astronomical.

Another user in our telegram group chat helped me set up a tool, edl[^2], which allows us to read (but not write) raw partitions from the phone over usb, which while very useful for complete backups of the phone's current firmware, didn't help us in this quest, as the userdata partition (220gb) is encrypted. This got us thinking.

The edl tool speaks a certain protocol to qualcomm devices (the same protocol the MSM tools speak), which is more than likely either the same, or similar, to what is used in manufacturing. So, if userdata is encrypted, and contains this image by default, does that mean that each device is flashed with a pre-encrypted 220gb userdata image? I should really hope not, as if the write speeds are anywhere near as bad as the read speeds (didn't time it, but it was a legitimately long time) it would take ages to manufacture all of the devices. The reserve.img, at least from other variants, was no bigger than 300mb. If userdata was not encrypted, they could simply 'place' the image in the right locale in manufacturing somehow, if its not encrypted. And with my previous mistake about loss on factory reset known, we took another tack. The idea was thrown about to pull the entire userdata, unlock the phone, and factory reset it from recovery, but that didn't seem to work with my replaced device, using the replacement as a userdata donor, but on a locked device it always survived.

Encouraged by this, we hatched upon a plan. It was simple in concept. Factory reset in recovery, immediatley power off the device, and pull userdata again via edl, and it would be easy to check if there's a chance of it working, without having to dump the entire image to check. After the first dozen or so mb, test the growing image with the `file` command. By this point it should be able to detect an ext4 filesystem, and if it returns as 'data' (means `file` doesn't know what it is, to be truthful), then we cancel the dump and try another approach. Unfortunately this would sacrifice all the actual userdata and customizations, but is otherwise non-destructive.

<script id="asciicast-YaLFHrLiy1dpqmrRSTdJMgC8X" src="https://asciinema.org/a/YaLFHrLiy1dpqmrRSTdJMgC8X.js" data-rows="10" async></script>
The moment of truth was here. I waited, and watched the percent counter tick off a few points. 3% should be enough, right?

<script id="asciicast-h0A79gbTap5Zx6T0V2wOuMEOK" src="https://asciinema.org/a/h0A79gbTap5Zx6T0V2wOuMEOK.js" data-rows="7" async></script>
Oh.. OH. It was working. After a long wait, I finally had a copy of my userdata partition, unencrypted and devoid of personal data, but the reserve.img was there for the taking. 260mb of salvation. The battle is over.

What now? The edl tool cannot write to the device, so we can't use it as part of the restoration, but all the devices which are OTA bricked have one thing in common: they have unlocked bootloaders. While its very unlikely we'll capture a reserve.img for versions 10.0.14 and 10.0.16 as new devices now update straight from 10.0.13 to 10.0.19, but what we can do is use edl to pull clean images from devices that were never unlocked, determine which ones were actually updated (some appear to never be touched), and package them up to be flashed on afflicted devices using fastboot. After everything else is on version 10.0.19, root and place reserve.img where it belongs. You'll still need to unroot to get further otas, but in theory it should no longer be impossible.

We won't know for sure that this will get us the next update until it comes out, but as far as I could tell from update failure logs this is the only real issue we'd have, as long as you do proper backups beforehand.

Full details on how to restore and links to downloads will be provided on [xda-forums](https://forum.xda-developers.com/7t-pro/how-to/hd1925-10-0-19-fastboot-rom-ota-fixer-t4040629) once prepared.

Big thanks to all the folks on xda and our telegram group chat, and may you have many happy updates!

[^1]: [note](/OnePlus-7t-Pro-5g/#fn:1)
[^2]: Not my tool, but don't want folks to bother the creator.

