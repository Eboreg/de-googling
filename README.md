# My de-googling experience

Like so many of us, I was sick and tired of being constantly tracked and monitored by Big Tech. But it was very much [Rob Braxman's videos](https://odysee.com/@RobBraxmanTech:6) that got me started on actually doing something about it, so big thanks to him for that.

Midway through, I decided to document the project in this manner, both for myself and for anyone who might find it useful in any way.

## Before

I use a Samsung Galaxy S9 (codename `starlte`). My most used Google apps on it were probably Keep, Youtube, and Drive. I host my own email since many years back, but I used the Gmail app to access it. I used Google Calendar, albeit with another app as frontend. And yes, I also synced my phone's photos with Google Photos. :-/

When backing up my computers, I also used Drive for storage; however, the files I sent were already encrypted by [Duplicati](https://www.duplicati.com/), so at least I had _some_ sense. And of course I use Firefox for browsing, and [Duckduckgo](https://duckduckgo.com/?q=rick+astley+never+gonna+give+you+up&iax=videos&ia=videos) for searching (only downsides: Google's image search is admittedly better, and I can't seem to get rid of all those search results in Chinese).

## Choosing a distribution

My first choice was [LineageOS](https://lineageos.org/). However, I soon learned I would need [MicroG](https://microg.org/) in order to receive push notifications and such stuff ... I think. My understanding of this is a bit patchy. Anyway, vanilla LineageOS does not include MicroG, the [LineageOS for MicroG](https://lineage.microg.org/) project doesn't have any builds for my phone (also, their site was down at the time), and I didn't want to make the installation more of a hassle than necessary (I don't really have a backup phone should I brick my current one). 

Solution: I went for the impossibly named LineageOS fork [/e/](https://e.foundation/).

## Installing the OS

### Easy install

I found that the /e/ "easy installer" software actually worked like a charm with my device; check [here](https://doc.e.foundation/devices) to find out if it's available for yours. Only problem is it insists on encrypting your Data partition, which (as I understand it) makes it impossible for you to root your phone. But that can be fixed afterwards; more on that later.

### Less easy install

I also played around quite a bit with manual installation. For this, I mostly followed the instructions [here](https://doc.e.foundation/devices/starlte/install) (with some assistance from [here](https://www.getdroidtips.com/lineage-os-18-1-samsung-galaxy-s9/)). I will now detail the process I ultimately landed upon:

Install [Heimdall](https://doc.e.foundation/support-topics/install-heimdall) (specifically for Samsung phones) and [adb](https://developer.android.com/studio/command-line/adb).

Download:

* Custom recovery [TWRP 3.2.3 for starlte](https://images.ecloud.global/stable/twrp/starlte/twrp-3.2.3-0-starlte.img) - find a variant for your device [here](https://twrp.me/Devices/). The reason I didn't go for a later version is it resulted in the error "failed to mount /odm" later on, but your mileage may vary.
* Ramdisk modification stuff to remove forceful encryption and other things: [Disable_Dm-Verity_ForceEncrypt_11.02.2020.zip](https://zackptg5.com/downloads/archive/Disable_Dm-Verity_ForceEncrypt_11.02.2020.zip)
* Vendor stuff: [VENDOR-27_ARI9.zip](https://images.ecloud.global/stable/vendors/VENDOR-27_ARI9.zip) (specific to my phone model)
* [Latest /e/ image ZIP archive](https://images.ecloud.global/stable/starlte/e-latest-starlte.zip) for my device; find yours [here](https://doc.e.foundation/devices)
* [Magisk APK](https://github.com/topjohnwu/Magisk/releases/)

Rename `Disable_Dm-Verity_ForceEncrypt_11.02.2020.zip` to `Disable_Dm-Verity_ForceEncrypt_quota_11.02.2020.zip`, because the filename holds significance and this disables disk quota. I honestly don't remember why I decided on this and if it's necessary in any way, but you can read more about it [here](https://forum.xda-developers.com/t/deprecated-universal-dm-verity-forceencrypt-disk-quota-disabler-11-2-2020.3817389/).

(Also, the "official" guide instructs you to use [no-verity-opt-encrypt-samsung-1.0.zip](https://images.ecloud.global/stable/patch/no-verity-opt-encrypt-samsung-1.0.zip) instead. I think this also worked for me, at least in combination with TWRP 3.2.3 (i.e. not the latest version), but that's just not the combination I ended up with.)

Rename `Magisk-v24.1.apk` to `Magisk-v24.1.zip`.

Boot to download mode (hold down `Volume down` + `Bixby` + `Power`). Run:

```shell
heimdall flash --RECOVERY twrp-3.2.3-0-starlte.img --no-reboot
```

Do not detach the USB cable before rebooting now, regardless of that the instructions say. I had some trouble getting it to reboot to recovery at this point; it would insist on rebooting normally, which would overwrite TWRP and render the whole exercise pointless. After some attempts, I discovered it actually worked with the USB cable still attached for some reason.

Power off device (`Volume down` + `Power`).

Immediately when the screen turns black: boot to recovery (`Volume up` + `Bixby` + `Power`).

**IMPORTANT: This is the point of no return. The actions hereafter will wipe all data from your phone.**

Now we're in TWRP. Do `Wipe > Format Data > type "yes"`. Back to main menu, reboot to recovery again.

Run:

```shell
adb shell "twrp mount system"
adb shell "twrp wipe system"
adb shell "twrp wipe cache"
adb push Disable_Dm-Verity_ForceEncrypt_quota_11.02.2020.zip /sdcard
adb shell twrp install /sdcard/Disable_Dm-Verity_ForceEncrypt_quota_11.02.2020.zip
adb push VENDOR-27_ARI9.zip /sdcard
adb shell twrp install /sdcard/VENDOR-27_ARI9.zip
adb push e-0.20-o-20220118158074-stable-starlte.zip /sdcard
adb shell twrp install /sdcard/e-0.20-o-20220118158074-stable-starlte.zip
adb push Magisk-v24.1.zip /sdcard
adb shell twrp install /sdcard/Magisk-v24.1.zip
```

Then, in TWRP: `Wipe > Advanced wipe > Data > Repair or change file system > Resize file system`.

Still in TWRP, choose to reboot normally. It will prompt you to install the TWRP app; however, every time I have agreed to do this, the system has refused to start afterwards, so I don't let it do that. But again, your mileage may vary.

Reboot, and the new OS plus Magisk should now be installed!

## Updating the OS

/e/ sometimes prompts you to install an OS update, which you of course should. However, this also automatically encrypts the Data partition, making you lose your sweet root privileges. So, whenever it wants to update, do this:

1. Boot to recovery (TWRP)
2. Do a backup of the Data partition, preferably to external SD card
3. Reboot and let it upgrade and encrypt
4. Boot to TWRP again
5. Wipe Data partition, possibly you need to reboot once more
6. Re-install Magisk and `Disable_Dm-Verity_ForceEncrypt_quota_11.02.2020.zip` (in that order) as per above (`adb push`, `adb shell twrp install`)
7. Restore backup of Data
8. Reboot
9. You're now updated and rooted! \o/

## Replacing Google

### The simple stuff

* Gmail -> [K-9 Mail](https://k9mail.app/) (simple because I really don't use my Gmail address for other than junk, and don't need to sync it on my phone; there probably is a way to do this, but I haven't bothered looking into it)
* Youtube -> [Newpipe](https://newpipe.net/) (with migration of my subscriptions etc via Google Takeout)
* Maps -> [Osmand](https://osmand.net/) (I use the the map source "OsmAnd (online tiles)", which I think looks nicest)

### Play Store

First choice for us [RMS](https://stallman.org/) wannabes is of course [F-Droid](https://f-droid.org/).

For non-FOSS apps, I just use the conveniently titled ["Apps"](https://gitlab.e.foundation/e/apps/apps) app that came preinstalled; only for some special cases, like my Swedish bank's application (yes, it works, and so does Swish and BankID!), have I needed to consult places like [APKPure](https://apkpure.com/) or [Aptoide](https://en.aptoide.com/).

### Calendar

I started out by installing [a basic CalDAV server](https://radicale.org/v3.html) on my trusty Raspberry Pi. But then I realised I wanted to try out that [Nextcloud](https://nextcloud.com/) thing everybody is raving about, so I installed that too on my poor Pi (with much help from [this page](https://docs.nextcloud.com/server/latest/admin_manual/installation/nginx.html), as I was already running Nginx). Not surprisingly, it runs a bit sluggish, and large imports tend to require some retries and also increasing the server timeout limits. But for my humble needs, it will probably suffice. Importing the `.ics` file exported from Google Calendar was a breeze IIRC. On the phone, I just use [/e/'s preinstalled fork of the Etar calendar app](https://gitlab.e.foundation/e/apps/calendar). 

### Keep

I love lists; nay, I _need_ lists. For all kinds of lists and short notes, I have been using Google Keep. I tried out a bunch of open source alternatives, but the one I will probably use is [Carnet](https://www.getcarnet.app/). I haven't tried it out extensively yet, but it looks like a more beautiful Keep and seems to have all the features I want. Also, I can sync it with the Nextcloud I already installed, as well as import my old Keep items (via Google Takeout again) using [their Nextcloud web app](https://apps.nextcloud.com/apps/carnet).

### Drive & Photos

The downside of Nextcloud is of course that you have to host your files yourself, or rely on there existing a Nextcloud integration with your cloud storage provider of choice (and the supply of such integrations honestly leaves a lot to be desired). So, as I neither want to invest in a NAS (plus the fact that backups should really be hosted off-site), nor want to settle for Dropbox or Onedrive, I will probably have to outsource the "file storage" part of my cloud solution to [Mega](https://mega.io/), who offer end-to-end encryption as well as native Linux (including Raspbian!) clients. Perhaps there is some convoluted way of integrating this into Nextcloud, but it's not a big deal. I've had good experiences with Mega in the past and will probably upgrade to a paid subscription.

### Software keyboard

This is probably where it makes the most sense to use open source software, since this app literally sees everything you type. Here is also where I ended up sinning, as Swiftkey is simply too damn good for me to switch, and none of the FOSS keyboards were to my satisfaction. But I did try!

[Openboard](https://f-droid.org/en/packages/org.dslul.openboard.inputmethod.latin/) looks to me like the best alternative, although [Florisboard](https://f-droid.org/en/packages/dev.patrickgold.florisboard/) shows great promise.

The Openboard versions in the app stores don't have a Swedish wordlist, though. To get that, you either need to build the app from [source](https://github.com/dslul/openboard) or trust one of the [user built packages](https://github.com/dslul/openboard/issues/454) (which you probably shouldn't).

### Chromecast

I regularily used my Chromecast device for listening to music and watching video. This is obviously out of the question now, as casting to Chromecast requires proprietary Google services (with the exception of VLC, which somehow manages to do it anyway?!). I replaced it with a Raspberry Pi, on which I installed [XBian](https://xbian.org/), which allows me to painlessly stream Netflix, Youtube, and local videos with [Kodi](https://kodi.tv/), while at the same time offering the freedom and familiarity of a Debian installation. I control it from my phone using [Kore](https://f-droid.org/en/packages/org.xbmc.kore/).

In order to run Spotify on this Raspberry Pi, use [Raspotify](https://dtcooper.github.io/raspotify/), which is a thin wrapper over [Librespot](https://github.com/librespot-org/librespot):

```shell
sudo apt install curl apt-transport-https git python
curl -sL https://dtcooper.github.io/raspotify/install.sh | sh
```

Now edit `/etc/raspotify/conf` with your account credentials and other settings to your liking. Then, to make librespot run as a daemon on startup, copy this to `/etc/init/raspotify.conf`:

```
description "Daemonized librespot"

start on net-device-up
stop on runlevel [!2345]

respawn

script
	set -a
	. /etc/raspotify/conf
	exec /usr/bin/librespot
end script
```

Your Raspberry Pi should now pop up as a device in your Spotify clients. Works like a charm for me:

![image](https://user-images.githubusercontent.com/1786886/156905263-df935a4c-4f17-4439-a27c-d2dcaca02fcc.png)

### The Bixby button

My phone is one of those equipped with an extra button, originally hardcoded to invoke Samsung's stupid Bixby assistant. I used to reassign it however I wanted (specifically, short press: play/pause media, long press: "do not disturb" on/off, double press: flashlight on/off) with the brilliant [BxActions](https://apkpure.com/bixbi-button-remapper-bxactions/com.jamworks.bxactions), but apparently, this requires the Bixby software to be installed, which it's not now (nor would I want it to be). I ended up manually re-mapping the button to toggle playing/pausing media, which is what I mostly wanted it to do. Here is a little shell script I wrote to accomplish this:

```shell
#!/bin/sh

# N.B: DON'T USE THIS VERBATIM ON YOUR DEVICE! ONLY FOR INSPIRATION.

adb root
adb remount
adb shell "cd /system/usr/keylayout && cp gpio_keys.kl gpio_keys.kl.backup"
adb pull /system/usr/keylayout/gpio_keys.kl ./
grep -q '^key 703' gpio_keys.kl
if [ $? -eq 0 ]; then
    # key found
    sed -i 's/^key 703.*$/key 703 MEDIA_PLAY_PAUSE/' gpio_keys.kl
else
    echo 'key 703 MEDIA_PLAY_PAUSE' >>gpio_keys.kl 
fi
adb push gpio_keys.kl /system/usr/keylayout/
# Should not be needed, but just for safety:
adb shell "cd /system/usr/keylayout && chown root:root gpio_keys.kl && chmod 644 gpio_keys.kl"
# Now reboot the phone.
```

Basically, it's just re-mapping key 703 (which is the Bixby button) to trigger the `MEDIA_PLAY_PAUSE` action. Nothing complicated.

## Non-phone de-googling

For those instances where you need to use Google services on your computer, I can recommend the add-ons [Firefox Multi-Account Containers](https://addons.mozilla.org/firefox/addon/multi-account-containers/) and [Google Container](https://addons.mozilla.org/firefox/addon/google-container/), which will at least keep your Google identity from leaking all over the place.

If Google requires two-factor authentication, you can select the option "Get a verification code from the **Google Authenticator** app", but use an open source TOTP app instead, like the excellent [AndOTP](https://f-droid.org/en/packages/org.shadowice.flocke.andotp/).
