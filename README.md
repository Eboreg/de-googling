# My de-googling experience

Like so many of us, I was sick and tired of being constantly tracked and monitored by Big Tech. But it was very much [Rob Braxman's videos](https://odysee.com/@RobBraxmanTech:6) that got me started on actually doing something about it, so big thanks to him for that.

Midway through, I decided to document the project in this manner, both for myself and for anyone who might find it useful in any way. Some of it is very specific to my particular phone model, but much isn't.

This is an ever evolving document.

# Before

I use a Samsung Galaxy S9 (codename `starlte`). My most used Google apps on it were probably Keep, Youtube, and Drive. I host my own email since many years back, but I used the Gmail app to access it. I used Google Calendar, albeit with another app as frontend. And yes, I also synced my phone's photos with Google Photos. :-/

When backing up my computers, I also used Drive for storage; however, the files I sent were already encrypted by [Duplicati](https://www.duplicati.com/), so at least I had _some_ sense. And of course I use Firefox for browsing, and [Duckduckgo](https://duckduckgo.com/?q=rick+astley+never+gonna+give+you+up&iax=videos&ia=videos) for searching (only downsides: Google's image search is admittedly better, and I can't seem to get rid of all those search results in Chinese).

# Choosing a distribution

My first choice was [LineageOS](https://lineageos.org/). However, I soon learned I would need [MicroG](https://microg.org/) in order to receive push notifications and such stuff ... I think. My understanding of this is a bit patchy. Anyway, vanilla LineageOS does not include MicroG, the [LineageOS for MicroG](https://lineage.microg.org/) project doesn't have any builds for my phone (also, their site was down at the time), and I didn't want to make the installation more of a hassle than necessary (I don't really have a backup phone should I brick my current one). 

Solution: I went for the impossibly named LineageOS fork [/e/](https://e.foundation/).

# Installing the OS

## Easy install

I found that the /e/ "easy installer" software actually worked like a charm with my device; check [here](https://doc.e.foundation/devices) to find out if it's available for yours. Only problem is it insists on encrypting your Data partition, which (as I understand it) makes it impossible for you to root your phone. But that can be fixed afterwards; more on that later.

## Less easy install

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

Rename `Magisk-vXX.apk` to `Magisk-vXX.zip`.

Boot to download mode (hold down `Volume down` + `Bixby` + `Power`). Run:

```shell
sudo heimdall flash --RECOVERY twrp-3.2.3-0-starlte.img --no-reboot
```

(I don't know if it's always necessary to do this as root, but it was for me.)

Do not detach the USB cable before rebooting now, regardless of that the instructions say. I had some trouble getting it to reboot to recovery at this point; it would insist on rebooting normally, which would overwrite TWRP and render the whole exercise pointless. After some attempts, I discovered it actually worked with the USB cable still attached for some reason.

Power off device (`Volume down` + `Power`).

Immediately when the screen turns black: boot to recovery (`Volume up` + `Bixby` + `Power`).

**IMPORTANT: This is the point of no return. The actions hereafter will wipe all data from your phone.**

Now we're in TWRP. Do `Wipe > Format Data > type "yes"`. Back to main menu, reboot to recovery again.

Run (with the correct filenames of course):

```shell
adb -d shell "twrp mount system"
adb -d shell "twrp wipe system"
adb -d shell "twrp wipe cache"
adb -d push Disable_Dm-Verity_ForceEncrypt_quota_11.02.2020.zip /sdcard
adb -d shell twrp install /sdcard/Disable_Dm-Verity_ForceEncrypt_quota_11.02.2020.zip
adb -d push VENDOR-27_ARI9.zip /sdcard
adb -d shell twrp install /sdcard/VENDOR-27_ARI9.zip
adb -d push e-0.20-o-20220118158074-stable-starlte.zip /sdcard
adb -d shell twrp install /sdcard/e-0.20-o-20220118158074-stable-starlte.zip
adb -d push Magisk-v24.1.zip /sdcard
adb -d shell twrp install /sdcard/Magisk-v24.1.zip
```

(The `adb shell twrp install` commands may also be done directly in TWRP, by selecting _Install_ from the main menu, saving you some tedious copy-pasting of filenames.)

Then, in TWRP: `Wipe > Advanced wipe > Data > Repair or change file system > Resize file system`.

Still in TWRP, choose to reboot normally. It will prompt you to install the TWRP app; however, every time I have agreed to do this, the system has refused to start afterwards, so I don't let it do that. But again, your mileage may vary. And if you end up with a non-booting phone after letting it install this app, just look [here](#uninstalling-the-twrp-app).

Reboot, and the new OS plus Magisk should now be installed!

# Updating the OS

/e/ sometimes prompts you to install an OS update, which you of course should. However, this also automatically encrypts the Data partition, making you lose your sweet root privileges. So, whenever it wants to update, here is what to do. Don't forget to uncheck the "install TWRP app" every time you exit TWRP, if you experience the same trouble as me with that one (see previous section).

1. Manually back up any images, documents, etc that you want to keep. My preferred method of doing this:
   ```
   adb -d pull /storage/self/primary/Pictures .
   adb -d pull /storage/self/primary/Documents .
   etc.
   ```
   N.B. Don't include the _Android_ directory.
2. Boot to recovery a.k.a. TWRP (`Volume up` + `Bixby` + `Power`)
3. Do a backup of the Data partition, preferably to external SD card
4. Reboot and let it upgrade and encrypt
5. Reboot to recovery; /e/ will probably have installed its own recovery now, just to fuck with you; in that case, reinstall TWRP:
   1. Boot to download mode (`Volume down` + `Bixby` + `Power`)
   2. Run `sudo heimdall flash --RECOVERY twrp-3.2.3-0-starlte.img --no-reboot`
   3. Boot to recovery again (`Volume down` + `Power`, then `Volume up` + `Bixby` + `Power`)
   4. This should land you in TWRP
6. Wipe Data partition, possibly you need to reboot once more
7. Re-install Magisk:
   1. `adb -d push Magisk-v24.1.zip /sdcard`
   2. In TWRP, push _Install_ and install this file
8. Re-install ramdisk modification:
   1. `adb -d push Disable_Dm-Verity_ForceEncrypt_quota_11.02.2020.zip /sdcard`
   2. In TWRP, push _Install_ and install this file
9. Restore backup of Data
10. Reboot
11. Restore backup from (1):
    ```
    adb -d push Pictures /storage/self/primary/
    adb -d push Documents /storage/self/primary/
    etc.
    ```
13. You're now updated and rooted! \o/
14. Optional step: [Remap the Bixby button](#the-bixby-button)

# Replacing Google

## The simple stuff

* Gmail -> [K-9 Mail](https://k9mail.app/) (simple because I really don't use my Gmail address for other than junk, and don't need to sync it on my phone; there probably is a way to do this, but I haven't bothered looking into it)
* Youtube -> [Newpipe](https://newpipe.net/) (with migration of my subscriptions etc via Google Takeout)
* Maps -> [Osmand](https://osmand.net/) (I use the the map source "OsmAnd (online tiles)", which I think looks nicest. Unfortunately, app settings seem to reset after [OS update](#updating-the-os), so make sure to back them all up as soon as you're satisfied with them)
* Google Authenticator -> [AndOTP](https://f-droid.org/en/packages/org.shadowice.flocke.andotp/)

## Play Store

First choice for us [RMS](https://stallman.org/) wannabes is of course [F-Droid](https://f-droid.org/).

For non-FOSS apps, I just use the preinstalled *App Lounge* application, where it seems you can access all of Play Store's apps anonymously, including my local Swedish services such as Swish and BankID (which work flawlessly, BTW). There are some annoying graphical bugs still, but basically it works fine.

## Calendar

I started out by installing [a basic CalDAV server](https://radicale.org/v3.html) on my trusty Raspberry Pi. But then I realised I wanted to try out that [Nextcloud](https://nextcloud.com/) thing everybody is raving about, so I installed that too on my poor Pi (with much help from [this page](https://docs.nextcloud.com/server/latest/admin_manual/installation/nginx.html), as I was already running Nginx). Not surprisingly, it runs a bit sluggish, and large imports tend to require some retries and also increasing the server timeout limits. But for my humble needs, it will probably suffice. Importing the `.ics` file exported from Google Calendar was a breeze IIRC. On the phone, I just use [/e/'s preinstalled fork of the Etar calendar app](https://gitlab.e.foundation/e/apps/calendar). 

## Keep

I love lists; nay, I _need_ lists. For all kinds of lists and short notes, I have been using Google Keep. I tried out a bunch of open source alternatives, but the one I landed on is [Carnet](https://www.getcarnet.app/). It looks like a more beautiful Keep and has all the features I want. Also, I can sync it with the Nextcloud I already installed, as well as import my old Keep items (via Google Takeout again) using [their Nextcloud web app](https://apps.nextcloud.com/apps/carnet). The only major downside is that after clearing and restoring the _Data_ partition, which I do when [updating the OS](#updating-the-os), it tends to throw all my notes in the trash and behaving as a new install, forcing me to manually recover my old notes! But this is a nuisance at most.

### Update 2023-08-06

I wasn't really satisfied with Carnet, so I set about creating my own note/checklist app, called Retain. It is open source, can sync its data via Nextcloud, SFTP, or Dropbox, and is available [here](https://github.com/Eboreg/Retain). Please note that it's in beta. Already works good enough for me, though!

## Drive & Photos

The downside of Nextcloud is of course that you have to host your files yourself, or rely on there existing a Nextcloud integration with your cloud storage provider of choice (and the supply of such integrations honestly leaves a lot to be desired). So, as I neither want to invest in a NAS (plus the fact that backups should really be hosted off-site), nor want to settle for Dropbox or Onedrive, I decided to outsource the "file storage" part of my cloud solution to [Mega](https://mega.io/), who offer end-to-end encryption as well as native Linux (including Raspbian!) clients. 

### Nerd notes

The only major downside is that there isn't a really efficient and reliable way to mount my Mega files as a local directory. The [Mega CMD](https://mega.nz/cmd) utils include tools to serve your files via WebDAV and FTP, which in turn enables you to mount using [davfs2](https://savannah.nongnu.org/p/davfs2) or [CurlFtpFS](http://curlftpfs.sourceforge.net/), respectively. But the WebDAV solution has been a bit janky for me, sometimes refusing to work because of filenames with some weird characters, and the FTP alternative is in beta and has also been a bit buggy for me.

The Mega SDK source code includes an [example implementation of a FUSE module](https://github.com/meganz/sdk/tree/master/examples/linux), but it's extremely basic and does not implement any caching. This makes transfers way too slow for usages such as video streaming. I have actually been working on my own FUSE module, but as my C++ skills leave a lot to be desired, the future for this project is uncertain.

## Software keyboard

This is probably where it makes the most sense to use open source software, since this app literally sees everything you type. Here is also where I ended up sinning, as Swiftkey is simply too damn good for me to switch, and none of the FOSS keyboards were to my satisfaction. But I did try!

[Openboard](https://f-droid.org/en/packages/org.dslul.openboard.inputmethod.latin/) looks to me like the best alternative, although [Florisboard](https://f-droid.org/en/packages/dev.patrickgold.florisboard/) shows great promise.

The Openboard versions in the app stores didn't have a Swedish wordlist, though (although this may have changed by now). To get that, you either need to build the app from [source](https://github.com/dslul/openboard) or trust one of the [user built packages](https://github.com/dslul/openboard/issues/454) (which you probably shouldn't).

## Chromecast

I regularily used my Chromecast device for listening to music and watching video. This is obviously out of the question now, as casting to Chromecast requires proprietary Google services (with the exception of VLC, which somehow manages to do it anyway?!). I replaced it with a Raspberry Pi, on which I installed [OSMC](https://osmc.tv/), which allows me to painlessly stream Netflix, Youtube, and local videos with [Kodi](https://kodi.tv/), while at the same time offering the freedom and familiarity of a Debian installation. I control it from my phone using [Kore](https://f-droid.org/en/packages/org.xbmc.kore/).

### Spotify

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

Your Raspberry Pi should now pop up as a device in your Spotify clients. Works like a charm for me (except for [one issue](#raspotify-not-working) that showed up in summer 2022):

![image](https://user-images.githubusercontent.com/1786886/156905263-df935a4c-4f17-4439-a27c-d2dcaca02fcc.png)

## The Bixby button

My phone is one of those equipped with an extra button, originally hardcoded to invoke Samsung's stupid Bixby assistant. I used to reassign it however I wanted (specifically, short press: play/pause media, long press: "do not disturb" on/off, double press: flashlight on/off) with the brilliant [BxActions](https://apkpure.com/bixbi-button-remapper-bxactions/com.jamworks.bxactions), but apparently, this requires the Bixby software to be installed, which it's not now (nor would I want it to be). I ended up manually re-mapping the button to toggle playing/pausing media, which is what I mostly wanted it to do. Here is a little shell script I wrote to accomplish this:

```shell
#!/bin/sh

# N.B: DON'T USE THIS VERBATIM ON YOUR DEVICE! ONLY FOR INSPIRATION.

adb -d root
adb -d remount
adb -d shell "cd /system/usr/keylayout && cp gpio_keys.kl gpio_keys.kl.backup"
adb -d pull /system/usr/keylayout/gpio_keys.kl ./
grep -q '^key 703' gpio_keys.kl
if [ $? -eq 0 ]; then
    # key found
    sed -i 's/^key 703.*$/key 703 MEDIA_PLAY_PAUSE/' gpio_keys.kl
else
    echo 'key 703 MEDIA_PLAY_PAUSE' >>gpio_keys.kl 
fi
adb -d push gpio_keys.kl /system/usr/keylayout/
# Should not be needed, but just for safety:
adb -d shell "cd /system/usr/keylayout && chown root:root gpio_keys.kl && chmod 644 gpio_keys.kl"
# Now reboot the phone.
```

Basically, it's just re-mapping key 703 (which is the Bixby button) to trigger the `MEDIA_PLAY_PAUSE` action. Nothing complicated.


# Non-phone de-googling

For those instances where you need to use Google services on your computer, I can recommend the add-ons [Firefox Multi-Account Containers](https://addons.mozilla.org/firefox/addon/multi-account-containers/) and [Google Container](https://addons.mozilla.org/firefox/addon/google-container/), which will at least keep your Google identity from leaking all over the place.

If Google requires two-factor authentication, you can select the option "Get a verification code from the **Google Authenticator** app", but use an open source TOTP app instead, like the excellent [AndOTP](https://f-droid.org/en/packages/org.shadowice.flocke.andotp/).


# Various tips & fixes

## Starting a root shell

This should normally be pretty straight forward:

```shell
adb -d kill-server
adb -d root
adb -d shell
```

(I add the `-d` to make it work on the USB connected device, as I also have an emulated phone created in Android Studio.)

However, this normally results in this happening for me:

```shell
klaatu@jacob:~/e$ adb -d root
* daemon not running; starting now at tcp:5037
* daemon started successfully
restarting adbd as root
timeout expired while waiting for device
```

Solution: Go to _Settings_ -> _System_ -> _Developer options_ on the phone, disable _Android debugging_ and immediately re-enable it. `adb -d shell` is now working.

## Uninstalling the TWRP app

Letting TWRP install its "official TWRP app" makes my phone refuse to boot for some reason. If this is the case for you, just follow these instructions for a really crude uninstall:

1. Boot to recovery a.k.a. TWRP (`Volume up` + `Bixby` + `Power`)
2. Mount the required partitions:
   1. Press _Mount_
   2. Press _Select Storage_ -> _Internal Storage_ -> _OK_
   3. Select at least the _System_ and _Data_ partitions
   4. Make sure _Mount system partition read-only_ is deselected
3. Start a root shell using one of these methods:
   1. On a computer connected via USB, do this to get a root shell:
      ```shell
      adb -d kill-shell
      adb -d root
      adb -d shell
      ```
   2. From the TWRP main menu, select `Advanced`, then `Terminal`
4. Run
   ```shell
   find / -name '*twrpapp*' -print -exec rm {} \; 2>/dev/null
   ```

The TWRP app should now be removed and the system able to start as usual. This solutions was found [here](https://android.stackexchange.com/a/197178).

## Raspotify not working

In July 2022, my [Raspotify installation](#chromecast) suddenly refused to play anything; librespot panicked and complained about "channel closed". I found [this issue](https://github.com/librespot-org/librespot/issues/972), which suggested there is some problem with one of Spotify's access points. So I added this to `/etc/hosts` on the Pi:

```
104.199.65.124  ap-gew4.spotify.com
```

... then restarted the Raspotify daemon, and it works again.
