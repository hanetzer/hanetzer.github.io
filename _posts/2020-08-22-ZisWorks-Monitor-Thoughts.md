---
layout: post
title: ZisWorks Monitor Thoughts
tags: linux amdgpu zisworks hardware
---

I recently purchased a pair of [ZisWorks](http://www.zisworks.com) X28 kits for the Samsung U28H750 display (stock hardware for this gives you 4k@60Hz display), and thought to pen down my thoughts on the subject.

Payment was simple, handled via paypal, and shipping was fast enough. Packaging was a bit unusual, but as this is 'just a guy' I can't blame them for that. As I had two displays to modify, I figured I'd do one, and leave the second one stock until I'm sure that it would work out for me in the long run.

Disassembling the display is easy, until it isn't. The top and sides of the outer shell release easily enough using a spudger/etc, however the bottom side is a bit tricky to free. There are two mechanism to install the new kit. Either you can modify an internal metal bit which holds one of the OEM pcbs in place to hold the new power board, or, remove that entirely and mount the new power board on a piece of cardboard.

For the first monitor, I opted to modify the metal support, easy enough with a bit of power tool usage. After installing the new hardware (two boards) and hooking up the cables to my gpu at the time (Sapphire RX480 8gb), the first issue arose. The display was detected by the operating system, but the screen was black. I sent Zis a text, and much to my surprise and delight, they walked me through a very thorough debugging session and we determined the issue was with the backlight driver (installing the one from the other kit fixed it all). This being determined, Zis mailed me a replacement, which I didn't install for a while.

At the time the amdgpu kernel module did not have proper support for EDID TILE'd displays, so running it in 4k@120Hz was not really an option (the display's left side seemed correct, but the right hand side was jittery and shifted up/down about 1/4 of the height of the screen), but this is a problem of linux and not the display itself. This has been fixed in kernel 5.7.6 and higher (at least with my old sapphire gpu, more on this later) but at the time I had to patch the kernel manually (easy enough to do with gentoo).

So, for about a month I ran the two displays side by side, OEM hardware at 4k@60Hz and the modified display at 4k@120hz, and the difference in quality was vast. Gaming on the enhanced display was noticably smoother in the one 'modern' game I play (Minecraft with either OptiFine or Sodium), even more so when I ran the 1080@240Hz setup. BlurBuster's testufo worked wonderfully even if it displayed a vsync not supported message.

After the initial testing month was over, I went ahead and installed the second kit. This time I opted to use the cardboard, to see if there were any noticable differences (I had assumed the internal metal bits may function as a heat spreader but this was not the case). As my gpu at the time did not have enough DP ports to run both in 4k mode I opted to run them both in 1080p240Hz mode (one cable each). Anecdotally, it seems using the orignal metal housing causes faster & more frequent fpga overtemp warnings when I don't have the AC running in that room, so maybe keep that in mind if you end up getting one of these. I did have a few issues with the screens flipping to some sort of corrupted state requiring a power cycle, or getting the OSD stuck, but this happened far less often after a few kernel upgrades and exchanging for some higher quality DP cables.

A month or so ago I purchased and recieved my new gpu, an Asrock RX 5700 XT Taichi, which I bough specifically for its 4x DP output (it also has two hdmi ports but I don't use those), and ran into another kernel related issue[^1]. Apparently NAVI gpus don't do these kind of displays properly at all, not even the 'half-right' setup with the old gpu. The bug[^2] has been reported to freedesktop's issue tracker and I hope the issue is fixed soon enough. Even with these hiccups, I have to say these are the best displays I've 

[^1]: Additionally, there's a poor interaction between my motherboard (Asrock X370 Killer Sli/ac) bios and the display, in that in UEFI/grub the DP outputs are not initialized at all, until the linux kernel loads. I think this may not be fixable without Asrock releasing another bios update.
[^2]: [Navi DC bug](https://gitlab.freedesktop.org/drm/amd/-/issues/1257)
