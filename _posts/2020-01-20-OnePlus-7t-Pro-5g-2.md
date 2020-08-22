---
layout: post
title: Hotdogg Wars - Attack of the Support Drones
tags: hotdogg oneplus tmobile android
---

Further developments on this front. As you may have seen, the previous blog [post](/OnePlus-7t-Pro-5g/)
has been picked up by the AndroidPolice[^1], so hopefully some form of fix will be
forthcoming.

A friend of mine, who was in the same situation, managed to secure a replacement
device from T-Mobile. At my instruction, he used the built-in OnePlusLogKit app
to capture the OTA update url for the phone. Strangely enough, the update given
to him was a direct update from 10.0.13HD61CB to 10.0.19HD61CB, and this is very
big news for all of us.

Once again, it was a delta update, which means it cannot save those of us stuck
in update limbo, but it proves that someone, somewhere has full roms of each of
these versions. OTA updates are produced by a tool, ota_from_target_files[^2], which
can produce both delta and full updates. You would first produce say, the first
version (lets call it version 1) and then after you have enough fixes and updates
to warrant pushing a new update to your users, you produce the other version, lets
call it version 2. Now you could push the full update to users and just call it
done, but as the name implies, a full update is just that, full. It contains all
the files and partitions, even if most of them are the exact same as the previous
versions. To save on bandwidth and storage space, you can use both full OTAs to
create a delta OTA. The process is documented here[^3].

Again, this means someone (T-Mobile, OnePlus, some third party) has the full OTA
packages, and one of the parties in question is refusing to release them. OnePlus
is pointing the finger at T-Mobile[^4], and T-Mobile is pointing the finger at OnePlus[^5].
One of them is lying to their customers, who paid $900 for what is otherwise a
great phone. Further, I can quote[^6] one of the @TMobileHelp drones as saying "However,
once the bootloader is unlocked, even if the phone isn't rooted, the device will
then stop receiving the updates". I don't need to tell you how bullshit that is,
I think. There is literally no reason to do so, as non-carrier OnePlus devices
do not have this issue, switching to full OTAs after their update app detects you
have unlocked the bootloader (how they do that I'm unsure. Perhaps there is some
api it queries, or perhaps its as simple as it notices the crucial file `reserve.img`
is missing).

[^1]: [AndroidPolice](https://www.androidpolice.com/2020/01/19/unlocking-the-bootloader-on-the-t-mobile-oneplus-7t-pro-5g-mclaren-breaks-your-ability-to-install-updates/)
[^2]: [ota_from_target_files](https://android.googlesource.com/platform/build/+/master/tools/releasetools/ota_from_target_files.py)
[^3]: [OTA production process](https://source.android.com/devices/tech/ota/tools)
[^4]: [blame from OnePlus](/images/oneplus-bullshit-000.png)
[^5]: [blame from T-Mobile](/images/tmo-bullshit-000.png)
[^6]: [quote](/images/tmo-bullshit-001.png)
