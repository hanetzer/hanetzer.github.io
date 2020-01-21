---
layout: post
title: T-Mobile OnePlus 7t Pro 5g McLaren - Defective by design. Part 1
---

Never Settle. Emblazoned on the website, promotional materials, and the home screen
of the phone, this is what OnePlus and T-Mobile would like you to believe about the
new OnePlus 7t Pro 5g McLaren edition (as this is a long name, from here on out I
will refer to it by its device codename, hotdogg).

On the surface, this appears to be true. Every aspect of the phone screams luxury
and power. Whether its the 8-core Snapdragon 855+ processor, the generous 256gb of
storage space, or the 12gb of ram, on paper it reads like an incredible phone, and
in many ways it is. However, under the hood a perfect storm of brokenness awaits.

As a carrier branded phone, it comes both network locked to T-Mobile and bootloader
locked. After one obtains a network unlock from T-Mobile, which nominally requires
that the phone be both paid in full and have been on the network for 40 days(some
times you get lucky and get a rep who will do it early for you), you can then apply
for an 'unlock token' from the oneplus website, which takes the form of a 288 byte
binary blob. This is 'flashed' (unsure if it actually gets written to the device or
is just interpreted by fastboot) in this manner `fastboot flash cust-unlock unlock.bin`,
after which you may unlock your bootloader with `fastboot oem unlock`.

I would highly suggest not doing so, however. Unless of course you want a phone you
can no longer update.

To elaborate, we must first talk a bit about how updates on this phone work. Unlike
previous android versions, devices launching with android 10 use what is quite an
ingenious setup. Instead of having single flat partitions (typically system and
vendor on older phones; this also has product and odm partitions), these phones have
two copies of every boot-critical partition, called 'sides' or 'slots', 'a' and 'b'.
system, vendor, product, and odm also comes in pairs, however, they are sort of
subpartitions of what is known as the 'super' partition. Effectively its an lvm
(Logical Volume Manager) setup, with dm-verity. This ensures the integrity of the
filesystem and protects users from persistent rootkits. Updates take one of two forms.
Either you have a full update, or a delta update. Full updates simply replace the
updated partitions wholly, and delta updates work as patches to existing filesystems.
After updating the 'other' side, your phone reboots and boots the freshly updated
side, and further updates will be applied to the 'other' side again, in a perpetual
dance of updates.

Delta updates cannot be applied if the partitions have been altered in any way.
This can be something big, like adding or removing files (typically not possible,
as the partitions are created with a subset of ext4 which is permanently read-only)
or even mounting the filesystem (ext4 filesystems record the last time they were
mounted). This is not an issue for the system, vendor, odm, and product partitions,
as they are the kind you cannot alter. However, there is one 'partition' you can,
and quite likely will, alter either on purpose or accident.

Sitting inside your userdata partition, where all your music, installed apps, and
memes are held, there is an ext4 filesystem image, `/data/reserve/reserve.img`. This
is mounted at `/system/reserve` by an init script, and contains some OnePlus apps.
Your phone can boot and operate just fine without this image, but once its gone,
you will never be able to update the phone again, as it is updated by the delta OTAs.

The problem here is, this file can and will be erased if at any point you erase
your userdata partition. ~~Performing a 'master reset' will delete it.~~[^1] Using fastboot
to erase userdata via `fastboot -w` will erase it. And unlocking your bootloader
will also erase it (by design; if it were not, anyone who steals your phone could
unlock the bootloader and load a rom to read your personal files and data).

On a non-carrier branded 7t Pro, you could simply go to the OnePlus site, download
your phone's software, and perform a local update to fix it. Two problems arise here:
As of this writing, there is no software package on the OnePlus site for this phone,
over a month after release, and even if it was on their site, the T-Mobile variant
does not have a local update option. If you could back up the image, you more than
likely could simply place the image back where it is expected after unlocking or
wiping. The issue here is, the file is only readable by the root user, and the
only way to root the phone is, you guessed it, unlocking the bootloader, which will
destroy it.

I highly advise anyone considering purchasing the phone to wait until either T-Mobile
or OnePlus provides some form of fix/recovery option, as right now you will be stuck
on whatever build of android you were on when you unlocked or wiped, and be missing
a handful of apps. If any vulnerability or exploit is found for these old versions
you will never be able to patch your phone to protect yourself.

Paying $900 for a phone which gets time locked as soon as you unlock it or even wipe
it is Settling, and you should listen to OnePlus on this matter, and NeverSettle for
defective products.

[^1]: Further experimentation shows that performing full resets of the phone via the option in settings or via recovery mode does not kill a fake reserve.img I placed there. My mistake. I've never seen any android phone before this one that preserved any files in `/data/` after performing factory resets in either manner, so you can see why I would believe that.
